---
title: "How to: Install Rtools in a custom location"
author: Mikko Marttila
date: '2019-10-01'
lastmod: '2022-01-01'
slug: how-to-install-rtools-in-a-custom-location
categories:
  - r
tags:
  - how-to
  - note
---

Today, I needed to update the version of [Rtools](https://cran.r-project.org/bin/windows/Rtools/) I use at work. But: the original installation was done by IT, in the default path of `C:/Rtools`; I don't have write access there, and I didn't want to wait for IT to do it for me. "I'll just install it under my user folder. How hard can it be?"

## RTFM

The manual is a good place to start. The `Rtools.txt` file that comes with the installer is pretty clear about the requirements:

1. Add `Rtools\bin` to your system `PATH` environment variable.
2. Tell R where to find the compilers with `BINPREF`.

An example for setting the `BINPREF` environment variable is also given:

```
BINPREF="M:/R/Rtools-3.5/mingw_$(WIN)/bin/"
```

With an explanation:

> Note how we embed another variable $(WIN) which is set by R to either "32" or "64" depending on the target. Thereby this BINPREF works for both architectures. Also note that R requires forward slashes here.

There's two additional pieces of information I wish I had known:

1. The path **must not** have spaces in it.
2. The trailing forward slash **must** be there.

## Debug

The `BINPREF` I was originally trying to use looked like this:

```
BINPREF="C:/Program Files/R/Rtools-3.5/mingw_$(WIN)/bin"
```

Running `pkgbuild::has_compiler(debug = TRUE)` gave me:

```
/usr/bin/sh: C:/Program: No such file or directory
```

Okay, no spaces allowed. I considered, for a moment, changing the installation directory again. Thankfully I realized that using a "short path name" would probably work. Conveniently, R includes a utility function to find one. For me:

``` r
shortPathName("C:/Program Files/R/Rtools-3.5")
#> [1] "C:\\PROGRA~1\\R\\Rtools-3.5"
```

With my updated `BINPREF` in hand, I was faced with a new error:

```
/usr/bin/sh: C:/PROGRA~1/R/Rtools-3.5/mingw_64/bingcc: No such file or directory
```

After a bit of staring, I noticed that the name of the compiler was just concatenated to the path witout a separator. It was trying to find a folder named `bingcc` rather than a file named `gcc` in the `bin` folder. Added the trailing forward slash to `BINPREF`, and it works.

``` r
pkgbuild::has_devel()
#> Your system is ready to build packages!
```

Fantastic! :tada:

## Success

**In summary:** set the following in your `.Renviron`, making sure the path to your installation location is formatted such that it has no spaces. Be sure to have `BINPREF` end with a trailing forward slash.

```
RTOOLS_ROOT="C:/PROGRA~1/R/Rtools-3.5"
PATH="${RTOOLS_ROOT}/bin;${PATH}"
BINPREF="${RTOOLS_ROOT}/mingw_$(WIN)/bin/"
```
