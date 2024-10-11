---
title: ggragged 0.2.0
author: Mikko Marttila
date: '2024-10-11'
slug: ggragged-0-2-0
categories:
  - r
tags: []
---

[ggragged](https://cran.r-project.org/package=ggragged) 0.2.0 hit CRAN earlier this week.
This release gives you more control over how axes and panel strips are drawn, along with
a new layout option.

<!--more-->

You can install it with:

``` r
install.packages("ggragged")
```

## New features

ggragged facets, like those that come with ggplot2, by default try to reduce visual clutter
in the layout caused by excessive graphical elements. Axes and panel strips are removed when
they're shared between adjacent panels.

As a baseline, let's first look at the default behaviour:

``` r
library(ggplot2)
library(ggragged)

p <- ggplot(mpg, aes(displ, cty)) + geom_point()
p + facet_ragged_rows(vars(drv), vars(cyl))
```

<!-- blogdown images are broken.. probably time to move on. -->

![](images/baseline-1.png)

With the new `axes` and `strips` parameters you can customise the de-cluttering process.

The `axes` parameter works like the same in built-in ggplot2 facets since 3.5.0.
It lets you request that particular axes are always drawn, regardless of whether
or not shared by an adjacent panel. You can for example set `axes = "all"` to
draw both axes for all panels:

``` r
p + facet_ragged_rows(vars(drv), vars(cyl), axes = "all")
```

![](images/axes-1.png)

Other options are `"all_x"` and `"all_y"` if you only want one of the axes to always be drawn.

Similarly, using `strips = "all"` will draw strips around all panels, even those on the same
row:

``` r
p + facet_ragged_rows(vars(drv), vars(cyl), axes = "all", strips = "all")
```

![](images/strips-1.png)

The third new parameter, `align`, gives you a little more control over the panel
positioning. You can now align the panels to the right of rows or to the
bottom of columns by setting `align = "end"`:

``` r
p + facet_ragged_rows(vars(drv), vars(cyl), axes = "all", strips = "all", align = "end")
```

![](images/align-1.png)

## Other news

Prompted by a [bug report](https://github.com/mikmart/ggragged/issues/2) and fix,
the panel rendering logic was fully rewritten in this release.

Previously, panel rendering was delegated to `FacetWrap`, with some post-processing
done to add missing elements. This always felt like a bit of hack to me. It
didn't really allow easily extending ggragged with new features, and I never
could figure out how to resolve obvious code duplication between the row and column
layouts with that approach.

Now gggragged implements the full panel rendering itself without the need to
call on other facets. The new logic follows the [architecture coming to ggplot2 in
the development version](https://github.com/tidyverse/ggplot2/pull/5917), which made
the refactoring process a lot easier than I had feared. It also allowed me to
consolidate the core of the rendering logic to a shared `FacetRagged` class, with
the row and column variants only implementing slight tweaks to achieve their
characteristic results.

Overall I'm really happy with how the code base turned out after these changes,
and I'm confident this puts the package on stable ground for future development.
I've done quite a bit of testing to ensure I didn't miss any features that rendering
via `FacetWrap` was giving for free. But if you spot something I missed, I'd really
appreciate it if you [opened an issue on GitHub](https://github.com/mikmart/ggragged/issues)
to let me know.
