# Typography Module

The *Finch Typography Module* handles everything to do with how text is styled and rendered in the browser. The typography module alone should be enough to make the text of any website readable, accessible and aesthetically pleasing (when using the defaults, and finch elements), but also provides mixins and functions to make the styling of text within your own design system as straighforward as possible.

## Mixins

The typography module exposes 3 main mixins for composing text styles:

1. text-style
2. text-alter
3. decorate (`decorate` is used to generate some other utility mixins, which we'll see shortly)

### 1. @include text-style

The `text-stlye` mixin is used to define the fundamental styles that are most commonly used to render text and differentiate between different "types" or "roles" of copy (e.g. headings, main body, highlighted text etc...). The properties that this mixin exposes are:

- font-family
- font-size
- font-weight
- line-height
- text-align

In the case of `text-align` only "keyword" values are accepted. The other properties expect a name that corresponds to one of the keys in the property's map (see the 'configuration' and 'property files' sections further down).
The formal syntax (representing the keyword values, and map keys that each property accepts by default) is as follows:

```
// global-values: initial | inherit | unset

$alignment: start | end | left | right | center | justify | match-parent | global-values
$family: serif | sans-serif | cursive | monospace | global-values
$line-height: x-tight | tight | base | tall | global-values
$size: small-print | base | lead | sub-heading | heading | global-values
$weight: x-light | light | base | thick | heavy | global-values
```

Some examples of using the `text-style` mixin:
``` scss
.text-main {
  @include text-style;
}

.text-lead {
  @include text-style($size: lead);
}

.text-block {
  @include text-style($size: lead, $alignment: justify, $weight: thick);
}
```

## 2. @include text-alter

The `text-alter` mixin exposes css porperties that alter the layout of the text by adjusting it's spacing, the type of typography and other, lesser-used properties that alter the appearance of the text. This mixin also allows for optional "optimisations" - these are values that are not the browser defaults that Finch thinks should be set, like `font-kerning: normal`, for example. The properties that this mixin exposes are:

- font-variant
- letter-spacing
- text-indent
- text-transform
- word-spacing

The `font-variant` and `text-transform` properties can only accept one its "keyword" values. The other properties expect a variable name that corresponds to a key from this property's map (see the 'configuration' and 'property files' sections further down).
The formal syntax is as follows:

```
// global-values: initial | inherit | unset

$indent: none | tight | base | wide | global-values
$letter-spacing: tight | base | wide | global-values
$transform: none | capitalize | uppercase | lowercase | full-width | full-size-kana
$variant: small-caps | all-small-caps | petite-caps | all-petite-caps | unicase | titling-caps | global-values
$word-spacing: tight | base | narrow
$optimise: true | false
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

## 3. @include decorate

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