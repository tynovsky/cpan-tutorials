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

    1 # a Perl module must end with a true value

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
called *Minilla* (it's a very opinionated one but I believe that for you as a
novice CPAN author, it is most important to start with something. If you find
later that it doesn't fit *your* opinions, you'll have enough knowledge to
replace it with any of the plenty of other tools) . Please install it with
command `cpanm Minilla`. Then let's clean up what we build until now:

    $ cpanm --uninstall Acme::Hola
    Acme::Hola contains the following files:

      .../lib/site_perl/.../Acme/Hola.pm
      .../man/man3/Acme::Hola.3

    Are you sure you want to uninstall Acme::Hola? [y] y

    Unlink: .../lib/site_perl/.../Acme/Hola.pm
    Unlink: .../man/man3/Acme::Hola.3
    Unlink: .../lib/site_perl/.../auto/Acme/Hola/.packlist

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

`Minilla` also created Git repository for us, where we can version the code.
It's a good idea to commit it right away:

    $ git commit -m 'Initial commit - Minilla scaffold'

## Using more files

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

    1
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

    1

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
        return $translator->hi();
    }

    1

Let's try this out:

     $ perl -Ilib -MAcme::Hola -E 'say Acme::Hola::hi("english")'
     Hello world

     $ perl -Ilib -MAcme::Hola -E 'say Acme::Hola::hi("spanish")'
     Hola mundo

We need to use a strange command line flag here: `-Ilib`. Once the
distribution is installed, you don't need to worry about configuring the load
paths.  However, if you're running the code from outside installed CPAN
modules, you have to configure things yourself. It's possible to manipulate
the `@INC` from the code itself but that's considered an anti-pattern in most
cases.

If you've added more files to your distribution, make sure to remember and add
them to the Git repository

    $ git add lib/Acme/Hola/Translator.pm

This way, you'll let `Minilla` index this file as a part of your CPAN
distribution. And of course, it's good idea to commit your changes anyway as
they represent a reasonable chunk of work:

    $ git commit -a -m 'hi function and Translator class'

## Adding an executable

In addition to providing libraries of Perl code, CPAN distributions can also
expose one or many executable files to your shell's `PATH`. One such example
we've already seen, the `cpanm` command comes from the distribution
`App::cpanminus`. Another example is `ack` command which can be installed by
`cpanm App::Ack` and serves as pretty convenient `grep` alternative:

<pre>
$ ack english
lib/Acme/Hola.pm
12:    $language //= '<span style='background-color:lightyellow'>english</span>';

lib/Acme/Hola/Translator.pm
8:        <span style='background-color:lightyellow'>english</span> => 'Hello world',
</pre>

Adding an executable to a distribution is a simple process. You just need to
place the file in your distribution's `script` directory and `git add` it.
Minilla will take care of the rest. Let's add one for our distribution. First
create the file and make it executable:

    $ mkdir script
    $ touch script/hola
    $ chmod a+x script/hola

The executable file itself just needs a
[shebang](https://en.wikipedia.org/wiki/Shebang_%28Unix%29) in order to figure
out what program to run it with. Here's what Hola's executable looks like:

    $ cat script/hola
    #!/usr/bin/env perl

    use Acme::Hola;
    print Acme::Hola::hi($ARGV[0]), "\n";

All it's doing is loading up the `Acme::Hola` module and passing the first
command line argument as the language to say hello with. Here's an example of
running it:

    $ perl -Ilib script/hola
    Hello world

    $ perl -Ilib script/hola spanish
    Hola mundo

Your new command line utility works like a charm! Don't forget to `git add
script/hola` and `git commit`.

## Writing tests

Testing your distribution is extremely important. Not only does it help assure
you that your code works, but it helps others know that your distribution does
its job. When evaluating a distribution, Perl developers tend to view a solid
test suit (or lack thereof) as one of the main reasons for trusting that piece
of code.

Distributions support adding test files into the package itself so tests can
be run when a distribution is installed. An entire community effort has sprung
up called CPAN Testers to hel document how CPAN distribution test suites run
on different architectures and interpreters of Perl.

In short: *Test your distribution!* Please!

`Test::More` is Perl's built-in test framework. There is a nice tutorial on
how to write tests with it by its author:
[Test::Tutorial](https://metacpan.org/pod/Test::Tutorial).  There are many
other test frameworks available for Perl as well (for example
[Test::Spec](https://metacpan.org/pod/Test::Spec) if you prefer writing tests
in [BDD style](https://en.wikipedia.org/wiki/Behavior-driven_development)). At
the end of the day, it doesn't matter what you use, just *test*!

Let's add some tests to `Hola`. We already have the auto-generated test which
checks if our module compiles. Let's add one which checks if our `hi` function
returns expected greetings for given languages:

    $ cat t/01_test_hola.t
    use strict;
    use Test::More tests => 3;
    use Acme::Hola;

    is(Acme::Hola::hi('english'), 'Hello world', 'English correct');
    is(Acme::Hola::hi('spanish'), 'Hola mundo', 'Spanish correct');
    is(Acme::Hola::hi(), 'Hello world', 'English is default');

Now you can try it out:

    $ perl -Ilib t/01_test_hola.t
    1..3
    ok 1 - English correct
    ok 2 - Spanish correct
    ok 3 - English is default

It's green! Well, depending on your shell colors. Don't forget to `git add
t/01_test_hola.t` and `git commit`. You can then also run the test using
`minil test` command.  For more great examples, the best thing you an do is
hunt around [Metacpan](https://metacpan.org) or [GitHub](https://github.com)
and read some code.

## Documenting your code

The CPAN distributions are documented with inline documentation in [POD
format](http://perldoc.perl.org/perlpod.html). You have it pre-generated by
`Minilla` in `lib/Acme/Hello.pm` file, so it's pretty easy just to fill the
prepared sections. Remember, one of the most important things about your
distribution is the *SYNOPSIS* chapter in your docs. Give a good example there
on what is the typical use of your distribution.

Here's just a simple example of how the POD in `lib/Acme/Hello.pm` can look
like:

    $ cat lib/Acme/Hola.pm
    ... # here is the code
    __END__

    =encoding utf-8

    =head1 NAME

    Acme::Hola - It's a program that greets world in given language.

    =head1 SYNOPSIS

        use Acme::Hola;
        Acme::Hola::hi("spanish);

        # or in your shell:
        hola spanish

    =head1 DESCRIPTION

    Acme::Hola is a library that greets world in given language. Together with
    the module an executable `hola` is installed which does the same on
    command line.

    =head1 LICENSE

    Copyright (C) Your Name.

    This library is free software; you can redistribute it and/or modify
    it under the same terms as Perl itself.

    =head1 AUTHOR

    Your Name E<lt>your.name@example.comE<gt>

    =cut

If you now run `minil test`, you'll see that not only the distribution was
tested but also the `README.md` file was regenerated. So if you upload your
distribution to GitHub, it'll have nice documentation right on the main page.

Don't forget to `git add` and `git commit` your changes.

## Handling dependencies

You might have noted that we were using `Moo` module in our
`Acme::Hola::Translator` module. The code for us without any problems because
coincidentally the `Moo` distribution is a dependency of `Minilla`. But when
another user installs our distribution, their don't necessarily have the `Moo`
dependency installed. To be sure that the dependency is installed, we need to
specify it in `META.json` package. To simplify the process, there is
`cpanfile` pre-created by `Minilla` in our distribution directory.

Let's add the dependency there:

    $ cat cpanfile
    requires 'perl', '5.008001';
    requires 'Moo', '2'; # <--- here we require Moo version 2 or higher.

    on 'test' => sub {
        requires 'Test::More', '0.98';
    };

The format of `cpanfile` is self-explanatory, you can see there is a section
for modules which are only needed during testing. If you want to know all
possible usages, please read [the documetation of
`cpanfile`](https://metacpan.org/pod/distribution/Module-CPANfile/lib/cpanfile.pod).

Now if we run `minil test`, not only the test will be executed but we will
also get our `META.json` file regenerated so that it includes the `Moo`
dependency.

## Creating the distribution tarball with Minilla

TODO: `Changes` file.

Now we have a decent quality distribution, it does something and tests and
documentation are there! Let's create the distribution tarball now. It is as
simple as:

    $ minil dist
    ...
    All tests successful.
    Files=7, Tests=4,  0 wallclock secs ( 0.03 usr  0.01 sys +  0.10 cusr
    0.01 csys =  0.15 CPU)
    Result: PASS
    Wrote Acme-Hola-0.1.0.tar.gz
    Removing .../Acme-Hola/.build/JjrPuBTf

Congratulations, your distribution is done! In next chapter, we'll describe
how to get it onto CPAN.


## Further reading

Minilla alternatives:

- [Dist::Zilla](http://dzil.org/)
- [Dist::Milla](https://metacpan.org/pod/release/MIYAGAWA/Dist-Milla-v1.0.15/lib/Dist/Milla.pm)
- [Module::Starter](https://metacpan.org/pod/release/XSAWYERX/Module-Starter-1.71/lib/Module/Starter.pm)
- [mbtiny](https://metacpan.org/pod/App::ModuleBuildTiny)
