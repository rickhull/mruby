# Introduction

While mruby code is nearly identical to ruby code, an mruby project works much
differently than a ruby project in terms of project structure, code reuse, and
code loading.

Mruby has no `require` method, so mruby projects tend to put all of their
ruby code into a single .rb file.  This means that splitting ruby code into
lib/ bin/ test/ (etc) is not easily or typically performed.  The mruby
methodology is to rebuild the mruby interpreter with mgems built in.  This
acts like lib/ in a standard ruby project.  Once the mruby interpreter is
rebuilt with the desired lib/ functionality (via mgems), it can accept a
single .rb file on the command line, where that .rb file acts like a "script"
or "driver".
