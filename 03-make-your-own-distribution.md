---
layout: plain_bootstrap
title: Make Your Own CPAN Distribution
---
# Make Your Own CPAN Distribution

Creating and publishing your own gem is simple thanks to the tools baked right
into the cpan ecosystem. Let's make a simple "hello world" distribution, and
feel free to play along at home! The code of the distribution we're going to
make here is up on GitHub.

## Your first distribution

I started with just one Perl file for my Acme::Hola distribution and few
mandatory files. You'll need a new name for yours (maybe
Acme::Hola::YourUserName) to publish it. Check the Patterns guide for basic
recommendations to follow when naming a distribution.

    $ tree
    .
    ├── lib
    │   └── Acme
    │       └── Hola.pm
    ├── META.json
    └── Build.PL

Code for your package is placed within the `lib` directory. The convention is
to have one `.pm` file with the same name as your distribution.

The code inside of `lib/Acme/Hola.pm is pretty bare bones`. It just makes sure
you can see some output from the module:

    $ cat lib/Acme/Hola.pm
    package Acme::Hola;

    use strict;
    use warnings;

    sub hi {
        print "Hello world!\n";
    }

    1; # a Perl module must end with a true value

`META.json` file defines what's in the distribution, who made it, the version of
the distribution, the license and other information. For now, we'll stick only
with the basic fields.

    $ cat META.json
    {
        "version" : "0.1.0",
        "name"    : "Acme-Hola",
        "author"  : "Your name here",
        "abstract": "A module from tutorial",
        "license" : "perl"
    }

Last but not least, `Build.PL` file defines the installation procedure. There
are several tools (or sets of tools) for creating CPAN distributions. For our
minimal example, I've chosen `Module::Build::Tiny`. It's the right time to
install it now using command `cpanm Module::Build::Tiny`. Thanks to this
module, our `Build.PL` file is as simple as these two lines:

    $ cat Build.PL
    use Module::Build::Tiny;
    Build_PL();

After you have created these essential files, you can build a distribution
from it. Then you can install the generated distribution locally to test it
out.

    $ perl Build.PL
    Creating new 'Build' script for 'Acme-Hola' version '0.1.0'
    $ ./Build
    cp lib/Acme/Hola.pm blib/lib/Acme/Hola.pm
    $ ./Build install
    Installing .../lib/site_perl/.../Acme/Hola.pm
    Installing .../man/man3/Acme::Hola.3

Of course, the smoke test isn't over yet: the final step is to `use` the
installed module from our new distribution and try to run the code from it:

    $ perl -MAcme::Hola -EAcme::Hola::hi
    Hello world!

## Scaffolding


