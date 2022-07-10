---
title: What version of a package added a function; or, binary search in R
author: Mikko Marttila
date: '2022-07-09'
slug: what-version-of-a-package-added-a-function-or-binary-search-in-r
categories:
  - r
tags:
  - stack-overflow
---

I came across this [Stack Overflow question](https://stackoverflow.com/q/72912513/4550695)
about how to find out when a given function was released. It turns out that a
binary search lets us find out faster.

First, let's think through what we need to answer the question.

To see if a function is included in an R package, we can check the contents of
the `NAMESPACE` file included in the package's sources. The [`pacs` package](https://cran.r-project.org/package=pacs) helps with reading that file
for any version of an R package published in CRAN, and with
[`pkgsearch`](https://cran.r-project.org/package=pkgsearch) we can easily
list all CRAN versions that have been published for a given package.

Let's define some helper functions to encapsulate those tasks:


```r
is_exported_in <- function(fn, pkg, version = NULL) {
  fn %in% pkg_namespace(pkg, version)$exports
}

pkg_namespace <- function(package, version = NULL) {
  pacs::pac_namespace(package, version = version)
}

pkg_versions <- function(package) {
  pkgsearch::cran_package_history(package)$Version
}
```

Looks like we have all the crucial pieces. What's missing is a strategy for
picking which versions to check and in what order---a search algorithm.


## Linear search

A linear scan is simple: Go through package versions in order, one-by-one,
and stop at the first version where the function is found. Often, this is all
you need.

``` r
implemented_in <- function(fn, pkg) {
  for (version in pkg_versions(pkg)) {
    if (fn |> is_exported_in(pkg, version)) return(version)
  }
  
  NA_character_
}

implemented_in("relocate", "dplyr")
```

However, in this case, there's a problem. Each check involves a relatively slow
file download in order to pull down an archived copy of a `NAMESPACE` file.
At the time of writing, `dplyr` has 39 published versions. Going through them
one-by-one, we'll quickly be waiting in the order of tens of seconds for our
search to finish. In fact, the `relocate()` function above was added in version
1.0.0 (the 30th version on CRAN), and took about 13 seconds to find.
Nobody has the patience for that!


## Binary search

A binary search is a more efficient way to tackle this problem. It takes
advantage of the ordered nature of the search space by always checking the
middle choice. Based on the check, half of the remaining search space can be
ruled out due to the ordering.
As a result, we only end up needing to check at most
<span class="math inline">\\( \\lceil log_2(n) \\rceil + 1 \\)</span>
choices.

It takes a bit more work to set up than a linear scan, but certainly pays off:


```r
implemented_in <- function(fn, pkg) {
  versions <- pkg_versions(pkg)
  
  lower <- 0
  upper <- length(versions) + 1
  
  while (upper - lower > 1) {
    mid <- floor((upper + lower) / 2)
    version <- versions[mid]
    if (fn |> is_exported_in(pkg, version)) {
      upper <- mid
    } else {
      lower <- mid
    }
  }
  
  versions[upper]
}

implemented_in("relocate", "dplyr")
```

```
## [1] "1.0.0"
```

The binary search finds the correct version in just under 2 seconds.


## Conclusion

Most of the time in R you don't end up needing to think too much about the
choice of a search algorithm. Often results are pre-computed for a vector of
values, and going through them is a matter of milliseconds.

However, this problem turned out to benefit a lot from a more carefully chosen
algorithm. It was also the first time I had to manually implement a binary
search in R. I certainly had a lot of fun with it!
