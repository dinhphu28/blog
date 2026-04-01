---
layout: page
title: Dictionary
description: Just for fun simple Apple-liked Dictionary to quickly look up words from books, blogs, and papers. Make it easier and faster, less-distraction when reading.
img: /assets/img/proj_dictionary-1.png
importance: 3
category: fun
giscus_comments: true
---

I think any people who read a lot have an headache when they want to quickly look up a word but every open the browser, search for the word, and then come back to the book.
Then ...
forget all of what they've just read. Me too.

So anyone has been used macOS know the built-in Dictionary with quick lookup with just right-clicking a word and then click "Look Up". Especially MacBook with haptic trackpad, just a force click. Tadaaa, we can see details of the word definition, and even more.

The problem is that not all of my machine is macOS, some of them are Linux, and I want to have the same experience on all of my machines.
So I made this simple Apple-liked Dictionary to quickly look up words from books, blogs, and papers.
Make it easier and faster, less-distraction when reading.

Besides, I also build a desktop app with Go Wails to make it more convenient to use.
Currently, I've just complete the just-enough features to address my needs. But I will keep improving and publish all of them in the future.

The quick lookup currently supports on browser (Chrome, Firefox) extension on Linux and macOS.

The reason why it's still not support on Windows is that the native messaging API of Windows is quite different from Linux and macOS, and I haven't had time to implement it yet.

And, honestly, I don't have a Windows machine to test it on.
But I will try to add support for Windows in the future.

If you are interested and want to contribute, please feel free to check out the repositories and give me a star if you like it. I will really appreciate it.

You can find them on GitHub:

- [dinhphu28/dictionary](https://github.com/dinhphu28/dictionary) for the core engine.
- [dinhphu28/dictionary-desktop](https://github.com/dinhphu28/dictionary-desktop) for the desktop app.
- [dinhphu28/dictionary-browser-extensions](https://github.com/dinhphu28/dictionary-browser-extensions) for the browser extensions.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj_dictionary-2.png" title="quick lookup" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Quick lookup with only double-click.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj_dictionary-3.png" title="quick lookup" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Multi-language support.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj_dictionary-4.png" title="quick lookup" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Desktop app.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/proj_dictionary-1.png" title="quick lookup" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Fuzzy match and suggestions.
</div>
