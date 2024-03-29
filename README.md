[![Actions Status](https://github.com/lizmat/Env/workflows/test/badge.svg)](https://github.com/lizmat/Env/actions)

NAME
====

Raku port of Perl's Env module

SYNOPSIS
========

    use Env;
    use Env <PATH HOME TERM>;
    use Env <$SHELL @LD_LIBRARY_PATH>;

DESCRIPTION
===========

This module tries to mimic the behaviour of Perl's `Env` module as closely as possible in the Raku Programming Language.

Raku maintains environment variables in a special hash named `%*ENV`. For when this access method is inconvenient, the Raku module Env allows environment variables to be treated as scalar or array variables.

The `Env` binds environment variables with suitable names to "our" Perl variables with the same names. By default it binds all existing environment variables (%*ENV.keys) to scalars. If names are specified, it takes them to be a list of variables to bind; it's okay if they don't yet exist. The scalar type prefix '$' is inferred for any element of this list not prefixed by '$' or '@'. Arrays are implemented in terms of split and join, using `$*DISTRO.path-sep` as the delimiter.

After an environment variable is bound, merely use it like a normal variable. You may access its value

    @path = split(':', $PATH);
    say join("\n", @LD_LIBRARY_PATH);

or modify it

    $PATH .= ":.";
    push @LD_LIBRARY_PATH, $dir;

however you'd like. Bear in mind, however, that each access to a bound array variable requires splitting the environment variable's string anew.

The code:

    use Env <@PATH>;
    push @PATH, '.';

is equivalent to:

    use Env <PATH>;
    $PATH .= ":.";

except that if $*ENV{PATH} started out empty, the second approach leaves it with the (odd) value ":.", but the first approach leaves it with ".".

To remove a bound environment variable from the environment, undefine it:

    undefine $PATH;
    undefine @LD_LIBRARY_PATH;

IDIOMATIC PERL 6 WAYS
=====================

If you're only interested in a few environment variables to be exported to your lexical context as constants, you can use the auto-destructuring feature of signatures in Raku:

    my (:$PATH, :$SHELL, *%) := %*ENV;

This will still collide with already defined variables (such as `$_`). This can be circumvented by creating a new scope:

    given %*ENV -> (:$_, :$PATH, *%) {
        dd $_, $PATH
    }

AUTHOR
======

Elizabeth Mattijsen <liz@raku.rocks>

Source can be located at: https://github.com/lizmat/Env . Comments and Pull Requests are welcome.

COPYRIGHT AND LICENSE
=====================

Copyright 2018, 2019, 2020, 2021 Elizabeth Mattijsen

Re-imagined from Perl as part of the CPAN Butterfly Plan.

This library is free software; you can redistribute it and/or modify it under the Artistic License 2.0.

