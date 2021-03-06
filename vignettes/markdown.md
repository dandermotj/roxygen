---
title: "Write R Documentation in Markdown"
author: "Gábor Csárdi"
date: "2016-09-01"
output: rmarkdown::html_vignette
vignette: >
  %\VignetteIndexEntry{Write R Documentation in Markdown}
  %\VignetteEngine{knitr::rmarkdown}
  %\VignetteEncoding{UTF-8}
---



# Introduction

Starting from version 6.0.0, roxygen supports markdown markup within most roxygen tags. Roxygen uses the `commonmark` package, which is based on the CommonMark Reference Implementation to parse these tags. See http://commonmark.org/help/ for more about the parser and the markdown language it supports.

# Turning on markdown support

Currently you have to turn on markdown formatting manually for each roxygen chunk. You can do this by using the `@md` tag anywhere in the chunk. In the future roxygen will provide a global switch that turns on markdown formatting for the whole package. (And also a `@noMd` to turn it off, optionally.)

Here is an example roxygen chunk that uses markdown.

```r
#' Use roxygen to document a package.
#'
#' This function is a wrapper for the [](roxygen2::roxygenize) function from 
#' the `roxygen2` package. See the documentation and vignettes of
#' that package to learn how to use roxygen.
#'
#' @param pkg package description, can be path or package name.  See
#'   [](::as.package) for more information
#' @param clean,reload Deprecated.
#' @inheritParams roxygen2::roxygenise
#' @seealso [](roxygen2::roxygenize), `browseVignettes("roxygen2")`
#' @export
#' @md
```

# Syntax

## Emphasis

*Emphasis* and **strong** (bold) text are supported. For emphasis, put the text between asterisks or underline characters. For strong text, use two asterisks at both sides.

```r
#' See `::is_falsy` for the definition of what is _falsy_
#' and what is _truthy_.
```

```r
#' @references
#' Robert E Tarjan and Mihalis Yannakakis. (1984). Simple

#' linear-time algorithms to test chordality of graphs, test acyclicity
#' of hypergraphs, and selectively reduce acyclic hypergraphs.
#' *SIAM Journal of Computation* **13**, 566-579.
```

## Code

Inline code is supported via backticks.

```r
#' @param ns Optionally, a named vector giving prefix-url pairs, as
#'   produced by `xml_ns`. If provided, all names will be explicitly
#'   qualified with the ns prefix, i.e. if the element `bar` is defined ...
```

For blocks of code, put your code between triple backticks:

```r
#' ```
#' pkg <- make_packages(
#'   foo1 = { f <- function() print("hello!") ; d <- 1:10 },
#'   foo2 = { f <- function() print("hello again!") ; d <- 11:20 }
#' )
#' foo1::f()
#' foo2::f()
#' foo1::d
#' foo2::d
#' dispose_packages(pkg)
#' ```
```

Note that this is not needed in `@examples`, since its contents is formatted as R code,
anyway.

## Lists

Regular Markdown lists are recognized and converted to `\enumerate{}` or `\itemize{}` lists:

```r
#' There are two ways to use this function:
#' 1. If its first argument is not named, then it returns a function
#'    that can be used to color strings.
#' 1. If its first argument is named, then it also creates a
#'    style with the given name. This style can be used in
#'    `style`. One can still use the return value
#'    of the function, to create a style function.
```

```r
#' The style (the `...` argument) can be anything of the
#' following:
#' * An R color name, see `colors()`.
#' * A 6- or 8-digit hexa color string, e.g. `#ff0000` means
#'   red. Transparency (alpha channel) values are ignored.
#' * A one-column matrix with three rows for the red, green
#'   and blue channels, as returned by [](grDevices::col2rgb)
```

Nested lists are also supported.

Note that you do not have leave an empty line before the list. This is different from some markdown parsers.

## Links

Markdown hyperlinks work as usual:

```r
#' See more about the markdown markup at the
#' [Commonmark web site](http://commonmark.org/help)
```

URLs are also automatically converted to hyperlinks:

```r
#' The main R web site is at https://r-project.org.
```

Markdown notation can be used to create links to other manual pages. There are four kinds of links:

```r
#' 1. Link to another manual page in the same package: [](::my_function).
#' 2. Link to a manual page from another package: [](devtools::document).
#'
#' 3. You can change the link text: Look at [this function](::=my_function).
#' 4. Also look at [this function in `devtools`](devtools::document).
#'
#' It is possible to make text links verbatim, [`like this`](::=my_function).
#' Even if they refer to another package,
#' [`like this`](maxygen::macument).
#' With quotes: [`"like this"`](maxygen::macument).
```

S3/S4 classes can be linked the same way:

```r
#' * [terms](::=terms.object) becomes \link[=terms.object]{terms}
#' * [abc](::=abc-class) becomes \link[=abc-class]{abc}
```

## Images

The parser recognizes the markdown notation for embedded images. The image files must in the  `man/figures` directory:

```r
#' Here is an example plot:
#' ![](example-plot.jpg "Example Plot Title")
```

# Roxygen and Rd tags *not* parsed as markdown

Some of the roxygen tags are not parsed as markdown. Most of these are unlikely to contain text that needs markup, so this is not an important restriction. Tags without markdown support: `@aliases`, `@backref`, `@docType`, `@encoding`, `@evalRd`, `@example`, `@examples`, `@family`, `@inheritParams`, `@keywords`, `@method` `@name`, `@md`, `@noRd`, `@rdname`, `@rawRd`, `@usage`.

When mixing `Rd` and markdown notation, most `Rd` tags may contain markdown markup, the ones that can *not* are: `\acronym`, `\code`, `\command`, `\CRANpkg`, `\deqn`, `\doi`, `\dontrun`, `\dontshow`, `\donttest`, `\email`, `\env`, `\eqn`, `\figure`, `\file`, `\if`, `\ifelse`, `\kbd`, `\link`, `\linkS4class`, `\method`, `\newcommand`, `\option`, `\out`, `\packageAuthor`, `\packageDescription`, `\packageDESCRIPTION`, `\packageIndices`, `\packageMaintainer`, `\packageTitle`, `\pkg`, `\PR`, `\preformatted`, `\renewcommand`, `\S3method`, `\S4method`, `\samp`, `\special`, `\testonly`, `\url`, `\var`, `\verb`.

# Possible problems

## Mixing markdown and `Rd` markup

Note that turning on markdown does *not* turn off the standard `Rd` syntax. We suggest that you use the regular `Rd` tags in a markdown roxygen chunk only if necessary. The two parsers do occasionally interact, and the markdown parser can pick up and reformat Rd syntax, causing an error, or currupted manuals.

## Leading whitespace

Leading whitespace is interpreted by the commonmark parser, whereas it is ignored by the `Rd` parser (except in `\preformatted{}`). Make sure that you only include leading whitespace intentionally, for example for nested lists.

## Spurious lists

The Commonmark parser does not require an empty line before lists, and this might lead to unintended lists if a line starts with a number followed by a dot, or with an asterisk followed by whitespace:

```r
#' You can see more about this topic in the book cited below, on page
#' 42. Clearly, the numbered list that starts here is not intentional.
```
