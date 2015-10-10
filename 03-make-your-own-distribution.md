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

The code inside of `lib/Acme/Hola.pm` is pretty bare bones. It just makes sure
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

    $ perl -MAcme::Hola -E 'Acme::Hola::hi()'
    Hello world!

## Scaffolding

Congratulations, you've just built your first working CPAN-like distribution.
It was a very basic one but it works! Before you upload your new distribution
to CPAN, it should fullfill some quality standard. It should contain tests and
documentation. At the moment, none of them is present:

    $ ./Build test
    Files=0, Tests=0,  0 wallclock secs ( 0.00 usr +  0.00 sys =  0.00 CPU)
    Result: NOTESTS

    $ perldoc Acme::Hola
    No documentation found for "Acme::Hola".

If we started adding more files and functionality, things would get
overcomplicated very soon. There are several tools that can assist with
tedious tasks during CPAN distribution development, we will pick one of them
called *Minilla*. Please install it with command `cpanm Minilla`. Then let's
clean up what we build until now:

    $ cpanm --uninstall Acme::Hola
    Acme::Hola contains the following files:

      .../lib/site_perl/5.20.2/Acme/Hola.pm
      .../man/man3/Acme::Hola.3

    Are you sure you want to uninstall Acme::Hola? [y] y

    Unlink:
    .../lib/site_perl/.../Acme/Hola.pm
    Unlink:
    .../man/man3/Acme::Hola.3
    Unlink:
    .../lib/site_perl/.../x86_64-linux-thread-multi/auto/Acme/Hola/.packlist

    Successfully uninstalled Acme::Hola

    $ cd .. && rm -rf Acme-Hola

And start from scratch (please
[install](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and
[do the basic
setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) of
[Git](http://git-scm.com) first):

    $ cpanm Minilla
    ...

    $ minil new Acme::Hola
    ...

    $ tree Acme-Hola
    ... # files that we had before
    ├── t
    │   └── 00_compile.t
    ├── Changes
    ├── cpanfile
    ├── LICENSE
    ├── minil.toml
    └── README.md

You can see that `minil` command generated all the files that we created
manually before and moreover several others:

- `t/00_compile.t` - `t` directory for tests and one example test which tests
  whether your module is compilable.
- `Changes` - file where you will describe what you improved on your
  distribution on every release.
- `LICENSE` - contains license which defines what others can do with your
  code.  By default it is dual license of GNU/GPL and Artistic license.
- `minil.toml` - configuration file of `Minilla`.
- `README.md` - extracted documentation from the main module of the
  distribution.

Let's re-create our code that prints out "Hello world". This time we'll add
translations to other languages.

    package Acme::Hola;
    use 5.008001;
    use strict;
    use warnings;

    our $VERSION = '0.1.0';

    sub hi {
        my ($language) = @_;
        $language //= 'english';
        my $translator = Acme::Hola::Translator->new(language => $language);
        print $translator->hi(), "\n";
    }

    {   package Acme::Hola::Translator;
        use Moo;
        has language => (is => 'ro');

        sub hi {
            my ($self) = @_;
            my %how_to_say_hi_in = (
                english => 'Hello world',
                spanish => 'Hola mundo',
            );
            return $how_to_say_hi_in{$self->language};
        }
    }

    1;
    # here starts the pre-generated documentation, we'll get back to it later

This file is getting pretty crowded. Let's break out the `Translator` into a
separate file. As mentioned before, the distribution's root file is typically
in charge of loading code for the distribution. The other files of a
distribution are placed in a directory of the same name of the distribution
inside of `lib`. We can split this distribution like so:

    $ tree lib
    lib/
    └── Acme
        ├── Hola
        │   └── Translator.pm
        └── Hola.pm

The `Translator` is now in `lib/Acme/Hola`, which can be easily picked up with
a `use` statement from `lib/Acme/Hola.pm`. The code for the `Translator` did
not change much:

    $ cat lib/Acme/Hola/Translator.pm
    package Acme::Hola::Translator;
    use Moo;
    has language => (is => 'ro');

    sub hi {
        my ($self) = @_;
        my %how_to_say_hi_in = (
            english => 'Hello world',
            spanish => 'Hola mundo',
        );
        return $how_to_say_hi_in{$self->language};
    }

    1;

But now the `Acme/Hola.pm` file has some code to load the `Translator`:

    package Acme::Hola;
    use 5.008001;
    use strict;
    use warnings;

    use Acme::Hola::Translator;

    our $VERSION = '0.1.0';

    sub hi {
        my ($language) = @_;
        $language //= 'english';
        my $translator = Acme::Hola::Translator->new(language => $language);
        print $translator->hi(), "\n";
    }

    1;

