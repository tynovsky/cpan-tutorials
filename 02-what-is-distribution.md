---
layout: plain_bootstrap
title: What is a CPAN Distribution
---
# What is a CPAN Distribution

*Unpack the mystery behind what's in a CPAN distribution*

## Structure of a distribution

Each CPAN distribution is a gzipped tar archive with a filename consisting of
distribution name and version. The distribution can contain one or more
modules; the distribution name is then usually derived from its most important
module.

For example the distribution of `Path-Tiny` is in an archive called
`Path-Tiny-0.072.tar.gz` and consists of a single module `Path::Tiny`.

Inside a distribution are the following components:

- code
- tests
- documentation
- additional files that specify:
  - installation procedure
  - contents of the package
  - meta information

A distribution usually has the following directory structure:

    Sample-Distribution-0.1.0
    .
    ├── lib
    │   └── Sample
    │       └── Distribution.pm
    └── t
        └── 00_my_first_test.t
    ├── README
    ├── Changes
    ├── LICENSE
    ├── Build.PL
    ├── MANIFEST
    └── META.yml

Here, you can see the major components of a distribution:

- `lib` and `t` directories contain the code and tests respectively. If there
  are any executables in the package, they are usually in `bin` or `script`
  directory.

- Documentation is usually included in the `README` (or `README.md`) and
  inline with the code. When you install a distribution, the documentation is
  generated automatically for you. The inline documentation is in POD format.

- `Changes` is the change log of the distribution, it documents the history of
  features and fixes in current and historical distribution versions.

- `LICENSE` contains the license of the code.

- `Build.PL` or `Makefile.PL` (or both) describe the installation procedure of
  the distribution.

- `MANIFEST` is a list of all files in the archive. Some tools use it for
  sanity checks

- `META.yml` describes in computer-readable form what the distribution
  contains. It includes distribution name, version, authors, license,
  dependencies etc.

There can be and usually are more files in a distribution, here we only
described those which are in almost all distributions.

## Further reading

The next section will show you how to create your own distribution.

See
  [Module::Build](https://metacpan.org/pod/release/LEONT/Module-Build-0.4214/lib/Module/Build.pm)
  for detailed information about `Build.PL` script.

See [ExtUtils::MakeMaker](http://perldoc.perl.org/ExtUtils/MakeMaker.html)
  for detailed information about `Makefile.PL` script

See
  [CPAN::Meta::Spec](https://metacpan.org/pod/release/DAGOLDEN/CPAN-Meta-2.150005/lib/CPAN/Meta/Spec.pm)
  for detailed information about `META.yml` file format.
