---
title: Nirvana fallacy and Rust
draft: false
date: 2025-08-23
tags:
  - rust
  - fallacy
  - seatbelts
---

# What is a Nirvana fallacy

Basically it's a fallacy where you compare actual things to unrealistic idealized alternatives, see [nirvana-fallacy](/notes/nirvana-fallacy).  It's a form of appeal to purity for the high-thonking intellectuals.

# Rusting trust

What does it have to do with Rust? 

Well the argument against using Rust goes like this. Rust promises no UB (Undefined Behavior) but:
1. If you use `unsafe` block/functions or traits, you can still trigger UB. 
2. The Rust codebase contains bugs labeled `l-unsound` [This is the link to GitHub issues](https://github.com/rust-lang/rust/issues?q=is%3Aissue%20state%3Aopen%20label%3AI-unsound)
3. Even then, you could call the C FFI (Foreign function interface), which means it's not safe. And by same extension, Java isn't safe nor any language for that matter, so you might as well use an unsafe language like C, C++, Zig or Go[^1].

To that, here is my honest and **NOT-SARCASTIC-FUTURAMA-RESPONSE** [^2].

{{< figure
  src = "/technically_correct.jpg"
  caption = "You are **technically** correct. The best kind of correct. Copyright: Disney 2026"
>}}


# Story of [Strawman](https://en.wikipedia.org/wiki/Hyperbole) Jim

Once upon a time I worked at a casino[^3]. And there I met Jim. Jim was an obsessive gambler and a barely functioning drug addict. At the end of my shift, while having to pick off unwanted stragglers from the floor, I asked him how he became a drug addict. 
  
His story goes like this: One day Jim went to buy a new car, and a decently priced one at that. So as he was about to sign the dotted line, a thought struck him. Did they test the seatbelts? The salesman told him that no, they don't test each seatbelt; rather, they test models of seatbelts, which are the same within some margin of manufacturing error.  
  
Unsatisfied, Jim decided not to buy the car but to investigate further the seatbelt safety. To his horror, he discovered that not only was the salesman right, but that seatbelts will prevent you from escaping in case a car is submerged!  
  
As he pored over page after page of data, he realized that in the case of a large meteor (one the size of Chicxulub), a seatbelt would hinder his attempts to dodge it.

Unsatisfied he slumped in his chair. But like the lightning of Zeus himself, he was struck by an idea! Should such a meteor strike him, his life and relationships would be moot anyway, so why waste his time on them?

Later on he broke with friends and family and devoted himself to gambling and snorting away whatever time he had left before the meteor struck. The meteor that could wipe us all out is probably just around the corner. Plus or minus a few million years.

# QED

Was Jim full of shit? I dunno. But it seems to me like a way to justify his bad habits.

---

[^1]: See https://www.ralfj.de/blog/2025/07/24/memory-safety.html
[^2]: It is sarcasm.
[^3]: I never worked at a casino. This story is fictional.
