---
title: Hyrum's law
draft: false
date: 2025-08-27
tags:
  - programming-law
  - dependency
  - breaking-change
---

> [!cite]
> With a sufficient number of users of an API, 
> It does not matter what you promise in the contract: 
> all observable behaviors of your system 
> will be depended on by somebody.
> --- Hyrum Wright [source](https://www.hyrumslaw.com/)

# Hyrum's law

An observation (aka #programming-law ) that says that as number of users of API increase, the more will the programs depend on the API's internal structure. I.e. what you promise in public API doesn't matter.

While it's not true generally, as most programs use small subset of API, it's anecdotally true in aggregate. Related to the [XKCD#1172](https://xkcd.com/1172/)