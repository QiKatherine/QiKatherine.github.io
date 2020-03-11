+++
title = "Detailed key proof of ANOVA test: SST=SSE+SSB"
date = 2020-03-09T00:07:00+00:00
lastmod = 2020-03-11T17:21:33+00:00
tags = ["statistics"]
categories = ["MATH"]
draft = false
image = "img/111.jpg"
+++

The ANOVA test is to use (rigorous mathmatical based) calculation to judge
whether there are differences in performance/data in different groups/treatment.
An intuitive description is that ANOVA compares the within group variance (SSE)
and between group variance (SSB). The summation of SSE and SSB happens to equal
to the summation of deviation of all the individual observations. In this case if the SSB is **way larger** than the SSE, it's
very likely that these groups are from distinctly different worlds (or
population, or distribution) instead of one world.

When I review the most foundamental knowledge of ANOVA test, I
notice two issues that make me feel necessary to write this detailed proof.
(1) The notation is not quite consistent across different materials,
especially for non-balanced dataset expression. Non-balanced datasets is that
every test group, or treatment has different numbers of observations. This is
almost everywhere in real world empirical studies.
(2)  Some of the prove of SST=SSE+SSB is missing steps or gets too complex in
polynomial expansions. The trick of proving multiple summation with polynomials
is "not to expand polynomials, but to use more distributive law".

So here I provide a note with full proof with consistent notations.
![](/img/anova1.jpg)

{{< figure src="/img/anova2.jpg" >}}
