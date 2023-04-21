---
title: data.table joins
author: Mikko Marttila
date: '2023-04-21'
slug: data-table-joins
categories:
  - r
tags:
  - data.table
  - cheatsheet
---

Natural equi-joins in [data.table](https://rdatatable.gitlab.io/data.table/), no frills.

<!--more-->


```r
library(data.table)

data("band_members", package = "dplyr")
data("band_instruments", package = "dplyr")

setDT(band_members)
setDT(band_instruments)
```

## Datasets


```r
band_members
```

```
##    name    band
## 1: Mick  Stones
## 2: John Beatles
## 3: Paul Beatles
```

```r
band_instruments
```

```
##     name  plays
## 1:  John guitar
## 2:  Paul   bass
## 3: Keith guitar
```

## Full join


```r
merge(band_members, band_instruments, all = TRUE)
```

```
##     name    band  plays
## 1:  John Beatles guitar
## 2: Keith    <NA> guitar
## 3:  Mick  Stones   <NA>
## 4:  Paul Beatles   bass
```

## Left join


```r
merge(band_members, band_instruments, all.x = TRUE)
```

```
##    name    band  plays
## 1: John Beatles guitar
## 2: Mick  Stones   <NA>
## 3: Paul Beatles   bass
```

## Right join


```r
band_members[band_instruments, on = .NATURAL]
```

```
##     name    band  plays
## 1:  John Beatles guitar
## 2:  Paul Beatles   bass
## 3: Keith    <NA> guitar
```

## Inner join


```r
band_members[band_instruments, on = .NATURAL, nomatch = NULL]
```

```
##    name    band  plays
## 1: John Beatles guitar
## 2: Paul Beatles   bass
```
