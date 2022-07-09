---
title: "Blogging: Icons with Font Awesome"
author: Mikko Marttila
date: '2018-02-18'
slug: testing-font-awesome
categories:
  - blogging
tags:
  - note
  - random
---

When googling... something[^1] **blogdown** related, I stumbled onto some [slides from an R-Ladies workshop](https://alison.rbind.io/slides/blogdown-workshop-slides.html), where [Alison Persmanes Hill](https://alison.rbind.io) goes through the process of setting up a website with **blogdown** and Hugo. The running example in the slides is the set up of a [website for the R-Ladies Portland chapter](http://rladies-pdx.rbind.io/), and I can assure you that the slides serve as a great tutorial for getting into **blogdown**, even without being actively presented by Alison! 

[^1]: What exactly it was persistently eludes me.

# <i class = "fab fa-font-awesome-alt fa-lg"></i> Icons!

I was already familiar with most of the topics, but [her introduction](https://alison.rbind.io/slides/blogdown-workshop-slides.html#74) of [Font Awesome icons](https://fontawesome.com/get-started) came as a (very pleasant) new discovery for me. However, it took me some time to figure out a little detail<sup>*</sup> needed to add the icons to my navigation bar -- where they have now replaced the text-based links to my social media pages.

## Setting up <i class = "fab fa-font-awesome fa-lg"></i>

The set up is really simple, just two steps:

1. Go to the [Font Awesome website](https://fontawesome.com/get-started) and copy their CDN link.[^3]
2. Paste the code into the HTML `<head>` of your site.

And that's it! Now you can effortlessly add little HTML elements (like this one `<i class="fab fa-fort-awesome"></i>`) to include cool icons (like this one <i class="fab fa-fort-awesome"></i>) on your website!

[^3]: I'm not including it here just so that in case it changes in the future, this post wont end up with a sneakily broken link.

## <i class="fas fa-asterisk"></i> The little detail

The method that Alison used in her talk for including icons in the navigation bar in a Hugo website was to simply add a `pre` field to the menu declaration in the TOML config file for the site. I did that, and nothing happened. The icons just wouldn't show up. What is this!?

After some head-scratching and additional googling I remembered that the slide before mentioned something about overriding the `nav.html` partial template included in the Lithium theme ([Yihui Xie's modified version](https://github.com/yihui/hugo-lithium-theme), which is what I'm using as well). I opened up the template, and lo and behold, I learned something about Hugo: my navigation bar template didn't include a `{{ .Pre }}` tag in it at all, so the `pre` field that I declared earlier simply wasn't used anywhere.

So, to fix my issue, and include icons in the navigation bar, I just had to make sure the `pre` value was being added to the navigation bar elements. I ended up with the list items in my `nav.html` looking like this:

```{html}
<a href="{{ .URL }}">{{ .Pre }} {{ .Name }}</a>
```


And, as you can tell, now everything works! :tada:[^2] Happy times. :grin:

[^2]: Yup, [emojis (... emoji? I don't know.) too](https://alison.rbind.io/slides/blogdown-workshop-slides.html#54)! [Only for plain Markdown though, not for R Markdown.](https://github.com/rstudio/blogdown/issues/171)

### In memoriam

And finally, here's a little memorial to my initial testing to see how I could include the Font Awesome icons in these posts:

> <i class="fas fa-camera-retro"></i>
> <i class='fab fa-github-square'></i>
> "<i class='fas fa-heart'></i>"
> <i class='fas fa-heart fa-2x'></i>
> <i class="fas fa-sync fa-spin fa-2x"></i>
> <i class="fab fa-twitter fa-2x" style = "color:#00aced"></i>
