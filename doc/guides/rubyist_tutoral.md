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
# ruby-install mruby
```

By default, the mruby source will live at e.g. `~/src/mruby-1.3.0`

Confirm this location, and then do something like

```
# MRUBY_SRC=~/src/mruby-1.3.0
```

## Create project dir

```
# PROJ_DIR=~/mruby/sleep_world
# mkdir -p $PROJ_DIR
# cd $PROJ_DIR
```

## Create hello_world.rb

```
# echo "puts :hello_world" > hello_world.rb
```

## Confirm execution via `mruby`

```
# $MRUBY_SRC/bin/mruby hello_world.rb
```

```
hello_world
```

## Add a call to Sleep.sleep

```
# echo "Sleep.sleep 1" >> hello_world.rb
```

## Confirm NameError

```
# $MRUBY_SRC/bin/mruby hello_world.rb
```

```
hello_world
trace:
        [0] hello_world.rb:2
hello_world.rb:2:uninitialized constant Sleep (NameError)
```

# Rebuild mruby

In order to be able to call Sleep.sleep, we need to "load" that functionality.
In the mruby world, this is done by rebuilding the mruby interpreter itself
with additional mgems, which are vaguely similar to gems in the MRI world.

## Create build_config.rb

```
# echo 'puts "BUILD CONFIG: #{__FILE__}"' > build_config
```

## Edit build_config.rb

```
puts "BUILD CONFIG: #{__FILE__}"

MRuby::Build.new do |conf|
  toolchain :gcc
  conf.gem git: 'https://github.com/matsumotory/mruby-sleep'
  conf.gem "#{root}/mrbgems/mruby-bin-mruby"
  conf.gembox 'default'
end
```

## Tell mruby where build_config.rb lives

```
# export MRUBY_CONFIG=$PROJ_DIR/build_config.rb
```

## Build mruby

```
# cd $MRUBY_SRC
# ./minirake deep_clean
# ./minirake
```

Confirm that you see "BUILD CONFIG: path/to/build_config.rb" near the top of
the output.

## Run hello_world.rb with the new mruby

```
# cd $PROJ_DIR
# $MRUBY_SRC/bin/mruby hello_world.rb
```

```
hello_world
```

You should notice that the program goes to sleep for one second before exiting.

# Refinement

Rather than manipulating the contents of $MRUBY_SRC, we can copy the mruby
source tree into $PROJ_DIR and keep $MRUBY_SRC pristine.

## Copy mruby source tree into $PROJ_DIR

```
# cd $PROJ_DIR
# cp -R $MRUBY_SRC mruby_src
```

## Rebuild mruby inside $PROJ_DIR

```
# cd mruby_src
# ./minirake deep_clean
# ./minirake
# cd ..
```

## Confirm hello_world.rb again

```
# cd $PROJ_DIR
# mruby_src/bin/mruby hello_world.rb
```

```
hello_world
```
