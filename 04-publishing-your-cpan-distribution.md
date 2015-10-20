---
layout: plain_bootstrap
title: Publishing Your CPAN Distribution
---
# Publishing Your CPAN Distribution

Ways to share your distribution code with other users.

## Introduction

Now that you've created your distribution, you're probably ready to share it.
While it is perfectly reasonable to create private distributions solely to
organize the code in large private projects, it's more common to build
distributions so that they can be used by multiple projects. This guide
discusses the various ways that you can share your distribution with the
world.

## Sharing source code

The simplest way (from the author's perspective) to share a distribution for
other developers' use is to distribute it in source code form. If you place
the full source code for your distribution on a public git repository (often,
though not always, this means sharing it via [GitHub](https://github.com)),
then other users can install it with cpanm's git functionality.

For example, you can install the latest code for the `Path::Tiny` distribution
by running

    cpanm https://github.com/dagolden/Path-Tiny.git

Installing a distribution directly from a git repository is an additional
feature of `cpanm` tool. At the moment, it's not possible to do the same with
original `cpan` command or specify a git repository as a dependency of another
distribution.

## Serving your own distributions

If you want to control who can install a distribution, or directly track the
activity surrounding a distribution, then you'll want to set up a private
CPAN-like server. You can set up your own server (e.g. using
[Pinto](https://metacpan.org/release/Pinto) or
[OrePAN2](https://metacpan.org/pod/OrePAN2)) or use a commercial service such
as [Stratopan](https://stratopan.com/).

## Publishing to CPAN

The simplest way to distribute your code for public consumption is to
upload the distribution to CPAN. Distributions published there can be
installed via the `cpan` or `cpanm` commands and can be easily required by
other distributions as dependencies.

To begin, you'll need to [create an account on
PAUSE](https://pause.perl.org/pause/query?ACTION=request_id). PAUSE stands for
"The [Perl programming] Authors Upload Server. After creating the account, you
can log in at the PAUSE site and upload your distribution tarball manually.

Alternatively, if you used `Minilla` for authoring your distribution,
publishing is even simpler! First, install a few necessary modules (`cpanm
Version::Next CPAN::Uploader`), then publishing is as simple as running one
command:

    $ minil release

Contratulations! Your new distribution is now ready for any Perl user in the
world to install!

## Changes file

It is a good practice to include `Changes` file with your distribution. It
contains a description of every released version. This time, we had the file
pre-created by Minilla, it only contained one line saying this is the initial
version of our distribution. Next time when you're releasing the next version
of your distribution, be sure to have your improvements listed under {{$NEXT}}
line (`Minilla` will take care of the rest).

## Push permissions on CPAN

If you have multiple maintainers for your distribution, you can give your
fellow maintainers permission to push the distribution to CPAN through [adding
a co-maintainer](http://www.cpan.org/modules/04pause.html#add-comaintainer) at
the PAUSE website.
