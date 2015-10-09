---
layout: plain_bootstrap
title: CPAN Basics
---
# CPAN Basics

The *cpan* and *cpanm* command allow you to interact with CPAN. *cpan* is
installed with Perl, *cpanm* might also be already included in your system,
otherwise consult this page to see [how to install
cpanm](https://metacpan.org/pod/release/MIYAGAWA/App-cpanminus-1.7039/lib/App/cpanminus.pm#INSTALLATION).

## Finding CPAN distributions

The CPAN shell lets you find remote distributions by name. First you need to
enter the CPAN shell:

    $ cpan

    cpan shell -- CPAN exploration and modules installation (v2.05)
    Enter 'h' for help.

Then, you can search with `d` command followed with your query (which can be
regular expression):

    cpan[1]> d /Path-Tiny/
    Reading '/home/tynovsky/.cpan/Metadata'
      Database was generated on Sun, 04 Oct 2015 12:41:02 GMT
    Distribution    DAGOLDEN/Path-Tiny-0.072.tar.gz
    Distribution    DAGOLDEN/Types-Path-Tiny-0.005.tar.gz
    Distribution    DMUEY/File-Path-Tiny-0.7.tar.gz
    Distribution    ETHER/MooseX-Types-Path-Tiny-0.011.tar.gz
    4 items found

You can also search for distributions online at http://metacpan.org or
http://search.cpan.org.


## Installing CPAN distributions

The `cpanm` command downloads, tests and installs the distribution and any
necessary dependencies.

    $ cpanm Path::Tiny
    --> Working on Path::Tiny
    Fetching http://www.cpan.org/authors/id/D/DA/DAGOLDEN/Path-Tiny-0.072.tar.gz ... OK
    Configuring Path-Tiny-0.072 ... OK
    Building and testing Path-Tiny-0.072 ... OK
    Successfully installed Path-Tiny-0.072
    1 distribution installed

## Using fetched code

Now when you write your code, you can use the modules from installed
distributions.

    #!/usr/bin/env perl
    use Path::Tiny;
    say Path::Tiny->cwd;

## Listing installed distributions

Use command `cpan -a` to list all installed distributions together with their
version. As a side effect, the list is written to Bundle/Snapshot\*.pm file.

## Uninstalling distributions

The command `cpanm --uninstall` can be used to remove distributions that you
have installed.

    $ cpanm --uninstall Path::Tiny
    Path::Tiny contains the following files:

      /home/tynovsky/perl5/perlbrew/perls/perl-5.20.2/lib/site_perl/5.20.2/Path/Tiny.pm
      /home/tynovsky/perl5/perlbrew/perls/perl-5.20.2/man/man3/Path::Tiny.3

    Are you sure you want to uninstall Path::Tiny? [y] y

    Unlink: /home/tynovsky/perl5/perlbrew/perls/perl-5.20.2/lib/site_perl/5.20.2/Path/Tiny.pm
    Unlink: /home/tynovsky/perl5/perlbrew/perls/perl-5.20.2/man/man3/Path::Tiny.3
    Unlink: /home/tynovsky/perl5/perlbrew/perls/perl-5.20.2/lib/site_perl/5.20.2/x86_64-linux/auto/Path/Tiny/.packlist

    Successfully uninstalled Path::Tiny

## Viewing documentation

To find out how to use the modules within the installed distributions, you can
use `perldoc` command to read the documentation which was automatically
installed with the distribution.

You can view the documentation for your installed distributions in your browser
if you install a tool for it (from CPAN of course). One of such tools is
`Pod::POM::Web`:

    $ cpanm Pod::POM::Web
    --> Working on Pod::POM::Web
    Fetching http://www.cpan.org/authors/id/D/DA/DAMI/Pod-POM-Web-1.20.tar.gz ... OK
    ==> Found dependencies: ...

    # ... here it works on dependencies

    Successfully installed Pod-POM-Web-1.20
    20 distributions installed

    $ perl -MPod::POM::Web -e server

And now you can visit `http://localhost:8080` in your browser and browse the
documentation of installed modules (and of perl itself).

## Fetching and unpacking distributions

If you wish to audit a distribution's contents without installing it, you can
use the `cpanm --look` command. It downloads and unpacks the distribution and
opens the directory with your shell.

    $ cpanm --look Try::Tiny
    --> Working on Try::Tiny
    Fetching http://www.cpan.org/authors/id/D/DO/DOY/Try-Tiny-0.22.tar.gz ... OK
    Entering /home/tynovsky/.cpanm/work/1443994273.5522/Try-Tiny-0.22 with /bin/bash

## Further reading

This guide only shows the basics of using CPAN related commands to download
and use the distributions published there. For information on what's inside a
distribution and how to use one you've installed, see the next section.

For a complete reference of `cpan` and `cpanm` commands see the following
links:

- [cpanm
  documentation](https://metacpan.org/pod/distribution/App-cpanminus/bin/cpanm)
- [cpan
  documentation](https://metacpan.org/pod/distribution/CPAN/scripts/cpan)

Plus you can read FAQ related to CPAN using command `perldoc -q cpan`.


