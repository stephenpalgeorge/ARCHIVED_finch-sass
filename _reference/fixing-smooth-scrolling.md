# Fixing Smooth Scrolling with Find-on-Page

Back when we released the v17 design (we’re on v18 now) of this site. I added html { scroll-behavior: smooth; } to the CSS. Right away, I got comments like this (just one example):

> …when you control+f or command+f and search on CSS-Tricks, it’ll scroll very slowly instead of snapping to the result, which makes finding information and keywords on CSS-Tricks much slower. As someone who uses this shortcut frequently, this is a usability issue for me.

Not terribly long after, I just removed it. I didn’t feel that strongly about it, and the fact that you have almost zero control over it, made me just can the idea.

I see it come up as a “CSS tip” a lot, so I chimed in with my experience
*(original article contains embedded tweet here)*

After mentioning that, Christian Schaefer chimed in with a great idea
*(original article contains embedded tweet here)*

Love that!

Christian blogged it:

> Smooth scrolling is consequently applied to everything. Always. Even when cycling through the browser’s page search results. At least that’s the case for Chromium. So for the page search it would be desirable for the browser to make an exception to that rule and to deactivate smooth scrolling. Until the Chromium team fixes it, here is a trick how to solve the problem on your own with a little bit of extra CSS and HTML.

I’m not sure if Chrome (or any other browser) would consider that a bug or not. I doubt it’s specced since find-on-page isn’t really a web technology feature. But anyway, I much prefer find-on-page without it.

```html
html:focus-within {
  scroll-behavior: smooth;
}
```

It mostly works. The bummer part about it is situations like this…

```html
<a href="#link-down-the-page">Jump down</a>
...
<h2 id="link-down-the-page">Header</h2>
```

That will jump the page down. With `scroll-behavior: smooth`; in place, that’s kinda nice. But `<h2>` is typically not a “focusable” element. So, with the trick above, there is now no focus within `<html>` anymore, and the smooth scrolling is lost. If you want to preserve that, you’d have to do:

```html
<h2 tabindex="-1" id="link-down-the-page">Header</h2>
```
