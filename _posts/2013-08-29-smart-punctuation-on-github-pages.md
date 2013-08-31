---
layout: post
published: true
title: Smart Punctuation on GitHub Pages
---

As part of my blog redesign and migration to GitHub pages I found out that you can enable
**SmartyPants** -- an extension for Markdown which formats punctuation marks properly for you.

Things that it can do include:

- Quotation marks: `""` and `''` into curly quotes.
- Dashes: `--` and `---` into en and em dashes.
- Elipses: From three consecutive dots `...`.

To enable this feature you need to be using the rdiscount engine with the `smart` extension
enabled. This is just a matter of adding the following to `_config.yml`.

{% highlight yaml %}
markdown: rdiscount
rdiscount:
  extensions: [smart]
{% endhighlight %}

I find this incredibly useful and convenient as it means I don't have to fiddle about finding the
appropriate HTML characters in order to make the text look better.
