---
title: Zero Copy Parsing Primer
draft: false
date: 2026-03-03
tags:
  - programming
  - rust
  - parsing
  - zero-copy
---
# What is zero-copy parsing?

It's a technique in parsing that avoids, if not outright minimizes, CPU usage and memory bandwidth.

So how do you achieve it? Simply. Just use a pointer.

# Essence of Zero Copying

In Rust, a `str` is a view into an array of UTF-8 bytes like so:
{{< figure
  src = "/zc_string.png"
  caption = "Behold! A String!"
  >}}

Imagine that our parser parses strings like `"Hi {user}"` into an expression that's an array of terms. 

The first term is an `InlineText("Hi ")` and second is `Variable(user)` like in the following picture


{{< figure
  src = "/zc_ast.png"
  caption = "Behold! An AST!"
>}}



If we wanted to minimize the amount of copying, we could simply just have string views into it.

We can achieve that with slices; they are essentially a `struct` with a `pointer` to some address and `length`. With it, we no longer need to allocate extra bytes, just two `usize` to store all data!

{{< figure
  src = "/zc_slices.png"
  caption = "Final form of Zero-Copying: Pointer + Length"
>}}



Ok, so that's that. We just output slices, and everyone has a great day, and good times are had by all, right? No.

# The Complications

## Character escaping

Here comes the first issue. Character escaping. Most languages support it. JSON, XML, YAML, OGDL, Java, etc. So, how do you solve this issue?

Let's first look at a message at the byte level. In our example, it's a simple `Hi \u1F600!` message, which renders to  `Hi 😀!`. Couldn't be simpler. 

{{< figure
  src = "/zc_cursed_emoji.png"
  caption = "It's just an emoji. It can't hurt us."
>}}



So you just go slice the whole message, right? Great idea. Set the pointer to start and say the length is 10. We look at our `InlineString("Hi \u1F600!")`. Ah, right, we need to convert the `1F600` from Unicode code point to bytes. And if [you check](https://www.fileformat.info/info/unicode/char/1f600/index.htm), you'll find out it's `0xF0`, `0x9F`, `0x98`, and `0x80`. Doesn't really roll off the tongue, but beggars can't be choosers. So we just need to modify the input string. And here lies the first complication. 

### The input string is unmodifiable but copyable. The COW method.

This isn't that uncommon of a limit, and if your code can't get access to a mutable string, your only course of action is to allocate a bit. It's not perfect, but it's good enough.

{{< figure
  src = "/zc_cow_method.png"
  caption = "This is the infamous COW method"
>}}


### The input string is unmodifiable. Be Lazy!

I do remember considering this method, but it's a bit more problematic. Basically store the content of slice of the original string, without transformation.

{{< figure
  src = "/zc_lazy.png"
  caption = "The success of being lazy"
>}}


It's definitely space optimal, but therein lies the rub. On access, you need to.
1. Always perform the conversion, which wastes CPU time on each call.
2. On first access, convert and store data somewhere. Which wastes time on allocation and uses space to store the escape. This comes with an extra caveat: if you have several different types of strings, like YAML, you need to also store the style of the node. 

>[!warning]
>Another thing to consider: this doesn't work with buffered readers. Lazy strings pointing to buffer would be invalidate when buffer advances.

### The input string is modifiable. Use in-place parsing

If the input is modifiable, there is a neat trick you can use to avoid any extra allocation, but it is a bit hacky. Essentially replace your characters `\`, `u`, `1`, `F`, `6` with new bytes as shown below.

{{<figure
  src = "/zc_inplace_edit.png"
  caption = "We heard you like editing strings"
>}}

As can also be seen, the fate of the remaining characters (faint `???` in the picture or `00!` in the original string) is in limbo. Do you skip them? Replace them with zeros? Leave as garbage? Up to you.

>[!tip] This trick only works if the total number of bytes goes down. 
>If by any chance they don't. This method breaks down. In case of UTF-8, the number of bytes goes down, once the `\u` are accounted for.

## Expansion of references

Same as before, but some languages like XML and YAML allow some reference to expand. If you're doing this in read-only, you're going to allocate a lot, and if you're doing this in-place, you might break the above-mentioned trick. 

For example, in XML a Doctype Definition reference like `&lol;` can be expanded into `lololololololol`. Coincidentally, this is great for a billion laughs attack.

## Trimming, concatenation, and transformations

Some formats might allow for text to be trimmed and concatenated.

For example, in the following text:

```yaml
a
b␣␣  
␣c
d
```

Which becomes a `VAL(a b c d)` even though the second line ends with two spaces (`␣`) and the third line starts with a space. There doesn't exist a continuous slice from `a` to `d` that can express the `a b c d` string value without modifications.  So our only choice is in-place modification, 

## Buffering

If you're doing buffering, which makes a lot of sense if you're working with huge documents, you are by definition forced to copy stuff. Buffer is a sliding window into the data, and anything that isn't copied can be lost on the next mutable buffer call.

# Conclusion

Zero copy is a great way to do parsing; it can minimize memory usage, but it comes with its caveats.

- The more the parsed AST is altered from the plaintext format, the more  it will need to allocate. By altered, I assume transformations of text that change the content of the string, so stuff like character escaping, reference expansions, but also line trimming and concatenation
- Zero copy and buffering are somewhat at odds; Zero copy assumes that most of the text is available, while buffering assumes that only a sliver of the underlying text is available.
- If processing huge documents, zero copy parsers suffer a disadvantage without a buffered fallback
