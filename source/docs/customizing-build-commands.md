---
layout: post
title: Customizing build commands
category: Customizing your build
---

You can easily customize commands that Semaphore executes in order to build your project.

Typical use cases for custom build commands that we have seen so far are:

 * setting up custom database configuration files
 * calling a custom rake task for setting up seed db data
 * passing special flags to default rake tasks
 * exporting environment variables that contain sensitive information which you don’t want to keep in your repository

Each build or deploy command is executed as a Linux shell command.

To edit a project build commands, click on "Project settings" on the  project page.

<img src="/docs/assets/img/custom-build-commands/cbc-project-settings-link.png" class="img-bordered-padding img-responsive">

You will see the commands editor page with your current build commands.

<img src="/docs/assets/img/custom-build-commands/build-settings.png" class="img-bordered-padding img-responsive">

## Adding a new command

If your plan allows multiple jobs, you can add a new job by clicking on
"Parallel job" link.

<img src="/docs/assets/img/custom-build-commands/parallel-job.png" class="img-bordered-padding img-responsive">

A new job will show up, where you can add new commands.

Alternatively, you can drag an existing command to the new job. The drag
handler will show up when you move the mouse pointer over a command.

## Deleting a command

When you move the mouse pointer over a command, you will see an option that
allows you to remove the command.

<img src="/docs/assets/img/custom-build-commands/delete-command.png" class="img-bordered-padding img-responsive">

## Editing a command

If you want to edit a single command, click on the command to activate editing.
You can confirm the new command by pressing Enter or cancel editing by pressing
Escape key.

<img src="/docs/assets/img/custom-build-commands/edit-command.png" class="img-bordered-padding img-responsive">

## Setup commands

Setup commands are executed before each job. Usually, setup commands install
the application dependencies, gems, create database and set configuration files.

To create a new setup, click on the "Setup" link on the bottom of the page.

<img src="/docs/assets/img/custom-build-commands/setup-button.png" class="img-bordered-padding img-responsive">

This will create the Setup area where you can add setup commands.

<img src="/docs/assets/img/custom-build-commands/setup-commands.png" class="img-bordered-padding img-responsive">

A project can have only one setup.

## Editing a job

If you want to edit multiple commands at once or you want to paste few commands,
click on the "Edit job" link. This will show text area where you can insert
your commands.

<img src="/docs/assets/img/custom-build-commands/edit-job.png" class="img-bordered-padding img-responsive">

## Deleting a job

To delete a job, click "x" link in the top right corner of the tread. If the
tread is not empty - you need to confirm that the job and all commands should
be deleted.

<img src="/docs/assets/img/custom-build-commands/delete-job.png" class="img-bordered-padding img-responsive">

## Renaming a job

Jobs can be named. The name of a jobs will be displayed on the build page.
To name a job, click on the "Rename" link.

<img src="/docs/assets/img/custom-build-commands/rename-job.png" class="img-bordered-padding img-responsive">

Commands editor allows you to add [after
job commands](/docs/using-post-thread-commands.html) that execute after each
job, even if a job command failed.

Note that Semaphore exports some [environment
variables](/docs/available-environment-variables.html) which might be useful to
you. You can also [install additional
packages](/docs/how-to-install-dependency.html) with commands like `sudo apt-get
install -y something`.

## Customizing command timeout

The default timeout for build commands on Semaphore is 60 minutes.
This means that if Semaphore detects that a command has been running longer than
that, it will automatically stop and fail the build.

Tasks such as compiling large binaries, provisioning big infrastructures, or
running tests that are difficult to parallelize sometimes require more time than
that.

On the other hand, you may want to reduce the timeout in order to prevent a test
suite from getting stuck because of an accidental debug statement or a network
call that will never complete.

For this reason, Semaphore allows to configure a build command timeout on a
per-project basis. To do that, as project admin:

- Go to project settings
- Open "Admin" tab
- Find the "Build options" section
- Set a new value of "Command timeout".

Options range from 15 minutes to 180 minutes, in increments of 15 minutes.

__Note:__

Since Semaphore Classic has a hard limit of task running maximum 3 hours, if you need to run a non-trivial task that takes more than 3 hours to finish, this can be done by increasing the timeout on Semaphore Classic in Project Settings > Admin and by splitting a non-trivial task to two subtasks would allow for each running up to 3 hours.

## Retrying commands

Sometimes, commands like `bundle install` fail due to the intermittent network
issues. To avoid this, you can use `retry` to re-run the command several times.

The [retry command](https://github.com/renderedtext/scripts/blob/master/utility/retry)
is installed by default in all builds on Semaphore.

To retry a command use the following scheme in your build configuration:

``` bash
retry --times N <command>
```

For example, to retry `bundle install` five times use the following snippet:

``` bash
retry --times 5 bundle install
```

You can also specify a sleep duration between consecutive executions of your
command. You the following form for that:

``` bash
retry --sleep N <command>
```

For example, to test if an endpoint is alive, you can retry a `curl` command
several times with a `10` second delay:

``` bash
retry --times 20 --sleep 10 curl http://<your-domain>/is_alive
```
