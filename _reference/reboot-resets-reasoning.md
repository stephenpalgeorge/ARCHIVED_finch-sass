---
url: "https://css-tricks.com/reboot-resets-reasoning/"
author: "Chris Coyier"
snapshot_date: "24/05/2021"
---

# Reboot, Resets and Reasoning

I saw in an article by Nicholas Cerminara the other day that Bootstrap 4 has a new CSS reset baked in they are calling Reboot:

> Reboot, a collection of element-specific CSS changes in a single file, kickstart Bootstrap to provide an elegant, consistent, and simple baseline to build upon.


If you’re new to CSS development, the whole idea of a CSS reset is to deal with styling inconsistencies across browsers. For example, just now I popped a `<button>` onto a page with no other styling whatsoever. Chrome applies `padding: 2px 6px 3px;` – Firefox applies `padding: 0 8px;`. A CSS reset would apply *new* `padding` to that element, so that all browsers are consistent about what they apply. There are loads of examples like that.

## By way of a bit of history…

In 2007 Jeff Starr rounded up a bunch of different CSS resets. The oldest one dated is Tantek Çelik’s undohtml.css. We can see that the purpose behind it was to strip away default styling.

```css
/* undohtml.css */
/* (CC) 2004 Tantek Celik. Some Rights Reserved.             */
/*   http://creativecommons.org/licenses/by/2.0                   */
/* This style sheet is licensed under a Creative Commons License. */

/* Purpose: undo some of the default styling of common (X)HTML browsers */
```

By far, the most popular reset came shortly after: the Meyer reset. It has different stuff in it than Tantek’s did (it has even been updated with some HTML5 elements) but the spirit is the same: remove default styling. You’ll probably recognize this famous block of code, finding its way into your DevTools style panel everywhere:

```css
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed, 
figure, figcaption, footer, header, hgroup, 
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
```

Start with a reset like this (at the top of your production stylesheet) and the styles you write afterword will be on a steady foundation.

Years later, as HTML5 became more real, resets like Richard Clark’s HTML5 Reset gained popularity. It was still a modified version of the Meyer reset, and the retained that spirit.

```css
article,aside,details,figcaption,figure,
footer,header,hgroup,menu,nav,section { 
    display:block;
}
```

Sprinkled all throughout this, there were plenty of developers who went minimal by just zapping margin and padding from everything and leaving it at that:

```css
* {
  padding: 0;
  margin: 0;
}
```

Dumb trivia: the CSS-Tricks logo was inspired by the universal selector and that idea.

## Along comes Normalize.css…

Normalize.css represents the first meaningful shift in spirit for what a CSS reset should do. This is what seemed so different about it to me:

It was a fresh evaluation of everything that could be styled different across browsers and it address all of it. Where older CSS resets were a handful of lines of code, the uncompressed and documented normalize is 447.
It didn’t remove any styling from elements that were already consistent across browsers (for the most part). For example, there isn’t anything in Normalize for h2-h6 elements, just a fix for a weird h1 thing. That means you aren’t zapping away header hierarchy, that default styling remains.
It was more accommodating to the idea of altering it, rather than just including it. For example, there is a section just for the <pre> tag and one line of that sets its font-family. You could change that to the font-family you want, and it would be just as effective of a reset.
The code is satisfying to read, as it explains what it’s doing without drowning in specifics:

```css
/**
 * 1. Remove the bottom border in Chrome 57- and Firefox 39-.
 * 2. Add the correct text decoration in Chrome, Edge, IE, Opera, and Safari.
 */

abbr[title] {
  border-bottom: none; /* 1 */
  text-decoration: underline; /* 2 */
  text-decoration: underline dotted; /* 2 */
}
```

Today Normalize is at 7.0.0 and has going on 30,000 GitHub stars. It’s wicked popular.

## So… resets can be opinionated?

We’ve seen lots of different takes on CSS resets and we’ve seen fundamental shifts in the approach, so I think it’s fair to say CSS resets can take an opinionated stance.

Let’s consider some ways…

- Does the reset touch every single possible element? Or a subset of elements? How does it decide which elements to touch and which not to?
- What properties are changed? Only ones with cross-browser differences? Or some other criteria, like the similarity to other elements that needed changes? Is it OK to apply properties to elements that don’t have cross-browser issues in the name of consistency and efficiency?
- Do you try to preserve the spirit of the user agent stylesheet? Sensible defaults?
- Do you apply any properties that don’t have cross-browser issues could be considered beneficial to “reset”, like typographic defaults or box-sizing?
- Do you include “toolbox” classes for common needs? Or leave that for other projects to handle?
- Are you concerned about the size of it?
- Do you use a preprocessor or any other tooling?

Take a look at Vanilla CSS Un-Reset. Loads of opinions here, starting with the idea that it’s meant to re-style elements after you un-style then with a reset. It set’s the body font size in `pt`, set a very specific monospace font stack, includes a `ol ol ol ol` selector, a clearfix, and alignment helper classes. No judgment there. People make things to help with their own problems and I’m sure this was helpful to the creator. But we can see the opinions shine through there.

Now look at MiniReset.css. Very different! It does wipe out type styles “so that using semantic markup doesn’t affect the styling”, but leaves some defaults in place on purpose “so that buttons and inputs keep their default layout”, puts in some things that don’t have cross-browser problems but are useful globally (`box-sizing`), and adds some minor responsive design helpers.

Totally different set of opinions there.

Jonathan Neal created a reset called santize.css that is very clear about it’s opinions. Search for the word “opinionated” in the source code and you’ll see it 19 times. All these are choices that Jonathan made based on research and what seem to be modern best practices, and no doubt sprinkled with his own needs and desires for what should be in a reset.

```css
/*
 * Remove the text shadow on text selections (opinionated).
 * 1. Restore the coloring undone by defining the text shadow (opinionated).
 */

::-moz-selection {
	background-color: #b3d4fc; /* 1 */
	color: #000000; /* 1 */
	text-shadow: none;
}

::selection {
	background-color: #b3d4fc; /* 1 */
	color: #000000; /* 1 */
	text-shadow: none;
}
```

## The word “reset”

Personally, I think it’s useful to think of all of them under the same umbrella term and just be aware of the philosophical differences. But, Normalize intentionally separates itself:

> A modern, HTML5-ready alternative to CSS resets

Sanitize calls itself a CSS library and doesn’t use the word “reset” anywhere except to cite the Meyer reset.

## Reboot

Reboot is interesting as it’s perhaps the newest player in this world. It’s file history dates back to 2015, which is probably related to Bootstrap 4 taking a while to drop after Bootstrap 3. Reboot doesn’t have its own repo, it’s a part of Bootstrap. Here’s the direct file and the docs.

The way they think about it is interesting:

> Reboot builds upon Normalize, providing many HTML elements with somewhat opinionated styles using only element selectors. Additional styling is done only with classes. For example, we reboot some `<table>` styles for a simpler baseline and later provide `.table`, `.table-bordered`, and more.

You can have a class that does styling, but if you use a reset, you don’t have to overload that class with reset styles that handle cross-browser consistency issues.

```scss
//
// Tables
//

table {
  border-collapse: collapse; // Prevent double borders
}

caption {
  padding-top: $table-cell-padding;
  padding-bottom: $table-cell-padding;
  color: $text-muted;
  text-align: left;
  caption-side: bottom;
}

th {
  // Matches default `<td>` alignment by inheriting from the `<body>`, or the
  // closest parent with a set `text-align`.
  text-align: inherit;
}
```

It’s definitely opinionated, but in a way that rolls with Bootstrap nicely. The fact that it’s buried within Bootstrap is pretty good signaling this is designed for that world, not as a drop-in for any project. That said, I did my best to compile a straight CSS version of it here.

## Tailoring a reset based on browser support

So long as we’re talking about the past and future of resets, it’s worth mentioning Browserslist again, which is a standardized format for declaring what browsers/versions a project supports.

A reset could be built in a such a way that the things it includes know why they are there. Exactly what browser and version it is there to support. Then if browserslist configuration says that particular browser isn’t supported by this project anyway, that CSS could be removed.

That’s what PostCSS Normalize does.