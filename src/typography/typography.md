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

## Property files

The typography module contains a file for each property that it interacts with. These generally follow a similar pattern to each other, and are then all `@used` in the module's `index.scss` file to have some common behaviours/configuration applied. Here follows an example of a typical 'property' file and a detailed explanation of the different sections of it. We'll then take a close look at the `typography/index.scss` file and how these properties are consumed.
First, the file (the example is the `typography/properties/_font-sizes.scss` file):

```scss
// # 1. IMPORTS
@use 'sass:map';
@use 'sass:list';
@use '../../config';

// # 2. PROPERTY CONFIG
$size-utils: false !default;

$finch-font-sizes: (
  small-print: map.get(config.$size-scale, 300),
  base: map.get(config.$size-scale, 400),
  lead: map.get(config.$size-scale, 500),
  sub-heading: map.get(config.$size-scale, 800),
  heading: map.get(config.$size-scale, 900),
) !default;

$set-font-sizes: () !default;
$sizes: map.merge($finch-font-sizes, $set-font-sizes);

// # 3. PROPERTY GETTER FUNCTION
@function size($key) {
  // return the given value immediately if it is a reserved keyword for this property:
  $spec-values: (xx-small, x-small, small, medium, large, x-large, xx-large, xxx-large);
  @if list.index(config.$global-values, $key) or list.index($spec-values, $key) {
    @return $key;
  }

  $value: map.get($sizes, $key);
  @if $value {
    @return $value;
  }

  @warn 'no font-size found for "#{$key}"';
  @return null;
}

// 4. UTILITY CLASSES
@if $size-utils {
  @each $key, $value in $sizes {
    .size--#{$key} {
      font-size: $value;
    }
  }
}

```

The numbered comments are just to give us an index to work with, you won't find them if you go and look in the source code.

### 1. Imports

This is simple enough, we use the global 'map' and 'list' modules that are built in to Sass. We also bring in the Finch config, that lives in the project root directory (two levels up from this file).

### 2. Property config

This is where some of the fun starts. First off, we define a boolean variable, that is defaulted to 'false'. This will be used as a flag for whether or not the utility class names will be generated for this property (more on this later). Next, we define a couple of Sass maps. The first are the default values for the property. In the case of some properties (like font-weight, for example), we define a series of variables for each item in the map just before the map itself. The next is an empty map that is declared as a `!default`, meaning it can therefore be overridden. These two maps are then merged. Obviously, the default result of that merge is that (in this example) `$sizes` is exactly the same as `$finch-font-sizes` BUT this pattern means the end user has complete flexibility to implement their own sizes instead of just using the defaults. For example, a developer might construct a `$set-font-sizes` map that has a `lead` key to override the default value for `lead`. Or they might pass in a map that contains a new font-size definition, like "hero", set to 5rem. This would get effectively 'appended' to the default values. A full example of this is shown below:
```scss
// developer uses the typography module their own custom font-sizes:
@use '<path-to-finch>/typography' with ($font-sizes: (lead: 4rem, hero: 5rem));

// this will then get passed into the font-size property file and merged with Finch's defaults,
// the final `$sizes` map would contain the following key: value pairs:
$sizes: (
  small-print: map.get(config.$size-scale, 300),
  base: map.get(config.$size-scale, 400),
  sub-heading: map.get(config.$size-scale, 800),
  heading: map.get(config.$size-scale, 900),
  lead: 4rem,
  hero: 5rem,
);
```

This would mean that the developer would be able to do things like `font-size: size(hero)` in their scss code, or, if they have configured Finch to generate utility classes, they would have access to the `.font-size--hero` class name for their markup, or for `@extend`-ing in their scss.

### 3. Property "getter" function

This simply defines a function that will return a value for the property. We first of all define a list of `spec-values`, that are the css-defined keywords for this property. We then check if the value that has been passed in exists in one of two lists - the spec values, or the global-values that are defined in Finch config. This basically means, if you're using the function, you can use any of the keyword values without having to worry about rewriting the whole line. So I could write a line like this:
```scss
font-size: typ.size(lead);
```
and then come back and refactor to this, without any worries:
```scss
font-size: typ.size(inherit);
```
Also, stuff like `font-weight: typ.weight(lighter);` will just work as well.
If the given value doesn't appear in either of those maps, we then check the property's map for a key of that name. If none is found, we return null and print a warning that would be seen in the console at build time. If the key is found, we return that value.

### 4. Utility classes

Unlike some frameworks, Finch is not utility-class driven. *But* We totally understand that they have their place and you may well want to use them. For each property, we check if the `utils` variable has been set to `true` (more on this in the config section) and then generate a class name for each key in the property map if so. The result of this is that you would get access to classes like `.font-size--lead` and `.font-weight--heavy` to use in your markup.

## Configuration

When you use the typography module, you can configure it with a number of variables that are declared as `!default` in the module's index. The typography module has the current, configurable, variables:

- $set-responsive
- $set-utilities
- $font-sizes
- $font-families
- $font-weights
- $letter-spacings
- $line-heights
- $text-decoration-thicknesses
- $text-indents
- $word-spacings

The first two (set-responsive, set-utilities) require some extra attention and explanation. The rest can be discussed quicly as one. Basically, for every property that the module deals with, you can pass your own map to override or the extend the default Finch values. This was discussed briefly in the **Property files** section above, but another quick example follows:

```scss
@use '<path-to-finch>/typography with (line-heights: (double: 2));'
```

The above code would mean that Finch would take your extra `line-heights` into consideration when running the rest of it's processes. So, for example, if it was generating utility classes for `line-heights` you would end up with an extra `.line-height--double` class available to you.

The *set-responsive* and *set-utilities* map are special configuration maps. Both of them hold a list of all the properties that the module handles as 'keys', and sets a boolean value against each one. These are *all* `false` by default and must be manually/intentionally overridden by the user.
The boolean values from the `set-utilities` map are passed into their corresponding property files to determine whether or not that property should generate utility classes or not. So, you could, for example, use the typography module with the following configuration:

```scss
@use 'finch/typography' as typ with (
  $set-utilities: (
    font-size: true,
    font-weight: true,
  ),
);
```

That would mean in your output css, you would have use of classes like `.font-size--small-print` and `.font-weight--x-light`, but *not* `.line-height--tall` or `.letter-spacing--wide` since neither of those properties have been set to `true` in the `set-utilities` map.

Similarly, Finch can do further heavy lifting for you via the `set-responsive` map. This map will configure Finch to create additional, responsive utility class names. What that means is that you'll get one class per breakpoint for the given property. So, if you were to configure Finch to generate responsive utilities for `font-size`, you'd end up with class names like `.xl:font-size--lead` and `.sm:font-size--base` that you could use in your markup. These would apply the given style to the breakpoint specified.

*N.B. for responsive classes to be generated, the property **must** be set to `true` in both the `set-utilities` and `set-responsive` maps.*

An example, to close, would be the following:

```scss
@use 'finch/typography' as typ with (
  $set-utilities: (
    font-size: true,
    font-weight: true,
  ),
  $set-responsive: (
    font-size: true,
    letter-spacing: true,
  ),
);
```

The above code would generate class names `.font-size--small-print` etc, and `.font-weight--light` etc and `.lg:font-size--lead` etc; but *not* `sm:font-weight--heavy` etc, since the `font-weight` property was not set to true in the responsive map. It would also not generate *any* utility classes at all for `letter-spacing` since that property was ommitted from the `set-utilities` map.

Happy typographying.
