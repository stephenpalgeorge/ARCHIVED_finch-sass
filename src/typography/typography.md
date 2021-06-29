# Typography Module

The *Finch Typography Module* handles everything to do with how text is styled and rendered in the browser. The typography module alone should be enough to make the text of any website readable, accessible and aesthetically pleasing (when using the defaults, and finch elements), but also provides mixins and functions to make the styling of text within your own design system as straighforward as possible.

This document contains:

- **Mixins:** an overview of the main mixins that the typography module provides, including examples and a formal syntax.
- **Configuration:** a description of how the module can be adapted and customised.

## Mixins

The typography module exposes 3 main mixins for composing text styles:

1. text-format
2. text-alter
3. decorate (`decorate` is used to generate some other utility mixins, which we'll see shortly)

### 1. @include text-format

The `text-format` mixin is used to define the fundamental styles that are most commonly used to render text and differentiate between different "types" or "roles" of copy (e.g. headings, main body, highlighted text etc...). The properties that this mixin exposes are:

- font-family
- font-size
- font-weight
- line-height
- text-align

In the case of `text-align` only "keyword" values are accepted. The other properties expect a name that corresponds to one of the keys in the property's map (see the 'configuration' section below and the separate 'property-files' doc).
The formal syntax (representing the keyword values, and map keys that each property accepts by default) is as follows:

```scss
// global-values: initial | inherit | unset

$alignment: start | end | left | right | center | justify | match-parent | <global-values> // default: start
$family: serif | sans-serif | cursive | monospace | <global-values> // default: sans-serif
$line-height: x-tight | tight | base | tall | x-tall | <global-values> // default: tall
$size: small-print | base | lead | sub-heading | heading | <global-values> // default: base
$weight: x-light | light | base | thick | heavy | <global-values> // default: base
```

Some examples of using the `text-format` mixin:
```scss
.text-main {
  @include text-format;
}

.text-lead {
  @include text-format($size: lead);
}

.text-block {
  @include text-format($size: lead, $alignment: justify, $weight: thick);
}
```

### 2. @include text-alter

The `text-alter` mixin exposes css porperties that alter the layout of the text by adjusting it's spacing, the type of typography and other, lesser-used properties that alter the appearance of the text. This mixin also allows for optional "optimisations" - these are values that are not the browser defaults that Finch thinks should be set, like `font-kerning: normal`, for example. The properties that this mixin exposes are:

- font-variant
- letter-spacing
- text-indent
- text-transform
- word-spacing

The `font-variant` and `text-transform` properties can only accept one their "keyword" values. The other properties expect a variable name that corresponds to a key from that property's map (see the 'configuration' section below, and the separate 'property files' doc).
The $optimise `boolean` flag will, when set to `true`, include some other font-related properties with some (hopefully) sensible defaults.
The formal syntax is as follows:

```scss
// global-values: initial | inherit | unset

$indent: none | tight | base | wide | <global-values> // default: none
$letter-spacing: tight | base | wide | <global-values> // default: base
$transform: none | capitalize | uppercase | lowercase | full-width | full-size-kana | <global-values> // default: none
$variant: small-caps | all-small-caps | petite-caps | all-petite-caps | unicase | titling-caps | <global-values> // default: normal
$word-spacing: tight | base | narrow | <global-values> // default: base
$optimise: true | false // default: false
```

Some examples of using the `text-alter` mixin follow (these examples combine `text-alter` with `text-style`):
```scss
.text-main {
  @include text-style;
  @include text-alter;
}

.text-lead {
  @include text-style($size: lead);
  @include text-alter($word-spacing: wide, $optimise: true);
}
```

### 3. @include decorate

The decorate mixin is a utility for the `text-decoration` property. The `text-decoration-line` property is used as the variable 'pivot' to create other utility mixins that accept arguments for the other properties that handled by the `text-decoration` shorthand. I.e. there exists an `underline` mixin, which directly sets the decorate mixin's `$line` parameter to `underline` and passes along arguments for $color, $style and $thickness.

*N.B. `text-decoration-thickness` is not currently well supported, so the value passed here shouldn't be expected to make any visual difference in all browsers. For support statistics, see [Can I Use.](https://caniuse.com/?search=text-decoration-thickness)*

Examples of using the decorate utilities follow:
```scss
em.text-emphasis {
  @include text-alter($letter-spacing: wide, $word-spacing: wide);
  @include underline;
}

.text-warning {
  @include underline($color: red, $thickness: .25rem);
}

.text-framed {
  @include text-style(size: lead);
  // the keyword 'both' is used in the `decorate` mixin to set the text-decoration-line property to `underline overline`
  @include decorate($line: both);
}
```

### 4. @include truncate

The truncate mixin is a utility for shortening text fit within a given container. By default, the text is truncated with an ellipsis (...), although this can be turned off so the text simply ends. The mixin accepts 2 parameters, one for setting the `max-width` of the container and one for specifying the number of lines across which the text can be written.

Examples of using the truncate utility follow:
```scss
.preview {
  @include truncate;
}

.synopsis {
  @include truncate($maxWidth: 10rem, $lines: 4);
}
```

---

## Configuration

When you use the typography module, you can configure it with a number of variables that are declared as `!default` in the module's index. The typography module currently has the following configurable variables:

- $use-web-safe
- $set-responsive
- $set-utilities
- $font-families
- $font-sizes
- $font-weights
- $letter-spacings
- $line-heights
- $text-decoration-thicknesses
- $text-indents
- $word-spacings

The top variables ($use-web-safe), should be explained. The next two (set-responsive, set-utilities) also require some extra attention and explanation. The rest can be discussed quickly as one. Basically, for every property that the module deals with, you can pass your own map to override or the extend the default Finch values. A quick example follows:

```scss
@use 'finch/typography' with (line-heights: (double: 2));
```

The above code would mean that Finch would take your extra `line-heights` into consideration when running the rest of it's processes. So, for example, if it was generating utility classes for `line-heights` you would end up with an extra `.line-height--double` class available to you.

### Utilities

The *set-responsive* and *set-utilities* maps are special configuration maps. Both of them hold a list of all the properties that the module handles as 'keys', and sets a boolean value against each one. These are *all* `false` by default and must be manually/intentionally overridden by the user.
The boolean values from these maps are iterated over to generate utility classes for the properties and their values, and for each breakpoint, if the `responsive` flag is `true`. Each utility class also creates an `!important` version, the class name for which simply appends a '!'.

```scss
@use 'finch/typography' as typ with (
  $set-utilities: (
    font-size: true,
    font-weight: true,
  ),
);

// classes like `.font-size:lead` and `.font-weight:heavy` are now available.
```

```scss
@use 'finch/typography' as typ with (
  $set-utilities: (
    font-size: true,
    font-weight: true,
  ),
  $set-responsive: (
    font-size: true,
    font-weight: true,
  ),
);

// classes like `.font-size:lead`, `font-weight:heavy!` and `.font-size:small-print@sm`
// are now available.
```

### Preferences

The 'preference' variable is `$use-web-safe`, which accepts a boolean value. Setting this variable to `true` (it is `false` by default) will cause *Finch* to use a different collection of fonts. By default, we are importing 4 fonts from [Google Fonts](https://fonts.google.com/) to make up our standard Finch font collection:

1. Serif font: [Playfair Display](https://fonts.google.com/specimen/Playfair+Display)
2. Sans-serif font: [Poppins](https://fonts.google.com/specimen/Poppins)
3. Cursive font: [Dancing Script](https://fonts.google.com/specimen/Dancing+Script)
4. Monospace font: [Source Code Pro](https://fonts.google.com/specimen/Source+Code+Pro)

But, you may want to use safe fonts instead. By default, when set to "use web safe", *Finch* will override the above families (and not import them) with the following font families:

1. Serif font: Georgia
2. Sans-serif font: Verdana
3. Cursive font: Brush Script MT
4. Monospace font: Courier New

You can replace any of these by passing in a custom `font-families` map to the module.

Happy typographying.
