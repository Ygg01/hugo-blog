---
title: Why Features > Performance
draft: false
date: 2026-03-18
tags:
  - programming
  - performance
---
I had this talk several times, and I wanted a place to write this down and explain my reasoning why features are on average more important than performance.

## eductio ad pixeles

Let's have a thought experiment. Imagine we want to edit an image. The image is in a simple format that is just a `width` x `height` sized array of RGBA values. It can be exported to other formats, but the image itself is elementary.

But image editors are slow to start, slow to save, overall, very slow. 

What is faster than an image editor? A text editor. The text editor starts in milliseconds, and we can configure it to open a the image as a hex file.

So instead of an image editor, we can use a hex editor to open our image as a series of bytes and modify them easily. It just works. Each image is just an array of RGBA values. So we need to draw a a line. That means change pixel @(x: `1000`, y: `300`) to (`0`, `0`, `0`,  `255`) and then @(x: `1001`, y: `300` ) to (`0`, `0`, `0`,  `255`) and then @(x: `1002`,y: `300`) to (`0`, `0`, `0`,  `255`). Even if we edit pixels by hand.

And this is just a three-pixel line. What happens if we miss a pixel and set `y` to `303`. That means we need to view it. So every edit or so, we need to step out of the hex editor, run a CLI to render the image, and then return to the hex editor to continue editing. So by the end our total time consumed via manual vs visual method is like following:


{{< figure
  src = "/time_saved.png"
  caption = "While they are somewhat arbitrary, any manual action has a minimum 0.2s for you to start acting on visual input."
  >}}

Even though each change is fast, the process of editing an image using HexEditor and CLI is painful. What if instead you could see where you're editing? Congrats, you reinvented the "**W**hat **y**ou **s**ee **i**s **w**hat **y**ou **g**et" ([WYSIWYG UI](https://en.wikipedia.org/wiki/WYSIWYG)).

## But, I need my performance.

Sure, we all do. But why do we need performance? We need it to stop wasting our time. We hate long compile times, because we could be doing something better! Why do load GUI? Because we using keyboard for navigation is inferior and less precise than a mouse.

Consider the following: don't features also exist to save our time? We also hate editing images pixel by pixel because we could let the computer do it way, way faster and more reliably than our wetware is capable of.


## But, I don't need _that_ feature.

Sure, but it's a feature that someone else _might_ need. As the saying goes:

>[!quote]
>## Everyone's Different 20%
>There is a broader truth about software usage that follows something like the 80/20 principle: most users will only ever use about 20% of your application's features, but each user uses a different 20%.
>
>The writer uses Word for drafting but never touches mail merge. The analyst uses Excel for pivot tables but never for scripting. A few PowerPoint users will never animate a single object. They are all using a different slice of the same monolithic suite, and each thinks their slice is the most important part.
>[Users care only about 20%](https://idiallo.com/blog/users-only-care-about-20-percent)

## When does the performance matter then?

Keen readers will note that while I pointed to performance playing second fiddle to features, I didn't disqualify it either.

### Performance matters when in competition

If whoever uses your software has a similar suite with all the features they need but way faster, now using your software is a waste of time compared to the other one.

### Performance matters if the slowdown is constant

I presented an idealized case. What happens if you get interrupted each time you draw a pixel on canvas? Well, if you waste time constantly, the time needed to complete some job will go up. Of course this might cause other side-effect like frustration and anger.

### But keep in mind performance is relative

I suspect that performance also suffers from the [Anchoring effect](https://en.wikipedia.org/wiki/Anchoring_effect). If you're used to slow HDD speed, you might be fine with games loading very slowly because you lack a better reference point. But on the flip side, if you encounter a way better thing that satisfies your other needs, you're way more likely to pick it over a slower one.

You can sense a 10fps drop (60->50) drop in a competitive multiplayer shooter, but it's less noticeable in a single-player grand strategy, where nothing happens for several minutes.

## So are the features the only thing?

No, of course not. I'd be a fool to think that time saved is the only important thing.

>[!info] The Gist
>Performance doesn't matter than much if a feature will save way more time than performance can recoup. 
>However a feature doesn't matter either if it's faster to do it manually. 

There are other things to consider; we love fast and responsive things but will tolerate a bit of speed loss for **reliability** and **fewer errors**. Plus, there are **individual preferences**, etc.

Features are easy to market, and adding more and more features will probably, in the extreme, affect performance as well.
