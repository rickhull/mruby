# Introduction

While mruby code is nearly identical to ruby code, an mruby project works much
differently than a ruby project in terms of project structure, code reuse, and
code loading.

mruby has no `require` method, so mruby projects tend to put all of their
ruby code into a single .rb file.  This means that splitting ruby code into
lib/ bin/ test/ (etc) is not easily or typically performed.  The mruby
methodology is to rebuild the mruby interpreter with mgems built in.  This
acts like lib/ in a standard ruby project.  Once the mruby interpreter is
rebuilt with the desired lib/ functionality (via mgems), it can accept a
single .rb file on the command line, where that .rb file acts like a "script"
or "driver".

# Objective

Write a hello_world.rb file that calls Sleep.sleep.  mruby does not have any
sleep built in.  In order to sleep, we need the mruby-sleep mgem.

What follows is a step by step procedure to accomplish and demonstrate the
objective.  As mruby is heavily C-based, there is a great deal of flexibility
for how to set up an mruby project, and many ways to set up a project that
seem correct but do not behave as expected.  What follows is one way to do
it that should make sense and behave as expected for those with much more
Ruby experience than C.  It is not necessarily the best or recommended way,
and this procedure may improve over time.

# Prerequisites and Assumptions

* Linux
* gcc
* ruby (MRI)
* ruby-install - https://github.com/postmodern/ruby-install

# Here goes nothing

## Install mruby via ruby-install

```
$ ruby-install mruby
```

By default, the mruby source will live at e.g. `~/src/mruby-1.3.0`

Confirm this location, and then do something like

```
$ MRUBY_SRC=~/src/mruby-1.3.0
```

## Create project dir

```
$ mkdir -p ~/git/sleep_world
$ cd ~/git/sleep_world
```

## Create hello_world.rb

```
$ echo "puts :hello_world" > hello_world.rb
```

## Confirm execution via `mruby`

```
$ $MRUBY_SRC/bin/mruby hello_world.rb
```
