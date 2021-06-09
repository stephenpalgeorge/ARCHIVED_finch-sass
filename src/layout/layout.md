# Layout module

The *Finch Layout module* handles everything to do with how things are positioned in the document. The layout module alone should give you all the tools you need to create any sort of layout that CSS can generate. The defaults should give a pleasing, well-spaced layout, but the module is also fully configurable to use your design system and variables.

This document contains:
- **Mixins:** an overview of all the mixins that this module makes available, including examples and a formal syntax.
- **Configuration:** a description of how the module can be adapted and customised.

## Mixins

The layout module exposes 4 main mixins for composing layouts, and many other `property` level mixins.

### Module-level mixins

#### 1. @include box-reset

The `box-reset` mixin is used to reset all of the 'box-model' related properties on a given element. The properties affected *can* include:

- width
- height
- margin
- padding
- border

The mixin accepts 2 arguments, both of which must be a boolean value.

1. **$size (default = true):** when true, the `width` and `height` properties will be reset to `initial`, otherwise they will retain whatever value has been set elsewhere.
2. **$border (default = true):** when true, the `border` property is reset to `none`, otherwise it will retain whatever value has been set elsewhere.

The `margin` and `padding` values will always be reset. By default, the mixin resets all of the listed properties, but the arguments can be used to "turn off" different groups of properties.

#### 2. @include box

The `box` mixin is a "layout" mixin that can help define a 'box' or 'area' of content. It will optionally 'reset' the box-model properties of the element in question (as detailed above) and set a padding. The mixin also draws an outline, which in some browsers/user preference situations, shows a visible outline when the background-color affect may be lost. Finally, the mixin defines that all the child elements of the 'box' should inherit this elements color values, meaning that we can easily redefine the color "theme" of a section of content, just by changing values on the parent box. Some examples of using the box mixin follow:

```scss
@use 'finch/layout' as lyt;

.container {
  @include lyt.box;
}

.frame {
  @include lyt.box($padding: large, $reset: true);
}
```

Box utility classes are also generated (according to the `box` value in the `$utilities` map). These classes (one for each padding) take the form `.box:pad-base`, `.box:pad-large` etc.

#### 3. @include pillar

The `pillar` mixin is a "layout" mixin that puts a 'pillar' or 'column' of content in the horizontal-center of it's parent. If the `$intrinsic` flag is set to `true`, the contents of the pillar will also be centred, whereas they would otherwise normally be tight to the left edge.
You can also set an optional `$max` width which, if set, will limit the size of the pillar. In small viewports, it will still be 100% width. Some examples of using the pillar mixin follow:

```scss
@use 'finch/layout' as lyt;

.center {
  @include lyt.pillar;
}

.center--narrow {
  @include lyt.pillar($max: 16rem, $padding: small, $intrinsic: true);
}
```

#### 4. @include stack

The `stack` mixin is used for spacing content that is arranged in a vertical column. It applies a consistent `margin-top` (accepted by the `$flow` parameter) to all child elements of the `.stack` (except the first) and can optionally be set to `$recursive`, in which case, all children of children etc will also recieve the same margin. This margin could be adjusted for nested children in that child's css code. The mixin defines a variable within it's scope - `stack-gap` - that could be redeclared within any child's style rules.
Some examples of using the stack mixin follow:

```scss
@use 'finch/layout' as lyt;

article {
  @include lyt.stack;
}

.contact-card {
  @include lyt.stack($flow: small, $recursive: true);
}
```

Stack utility classes are also generated (depending on the `stack` value in the `$utilities` map). These classes (one for each margin) take the form of `.stack:base`, `.stack:large` etc *and* `.stack-recursive:base`, `.stack-recursive:small` etc.

### Property-level mixins

#### @include border-radius

The `border-radius` mixin is used for setting the `border-radius` property on one or more corners of an element. If `null` or an invalid 'corner' name is passed, the mixin will fallback to setting the given radius on *all* corners.
Some examples of using the border-radius mixin follow:

```scss
@use 'finch/layout' as lyt;

button {
  // sets a 'round' border-radius (default: 1.5rem) on all corners:
  @include lyt.border-radius(round);
}

a[class*="btn"] {
  // sets a 'subtle' border-radius (default: .125rem) on top-left and bottom-right corners:
  @include lyt.border-radius($rad: subtle, $corners: top-left, bottom-right);
}
```

#### @include border

The `border` mixin is used for setting all border-related properties of an element. This is similar to the css 'border' shorthand property, except it will also set 'border-radius' if a value is passed. The `border` mixin will only set `border-radius` consistently on all corners, if different radii are required on different corners, the `border-radius` mixin should be used.
The mixin will set the border on one, several, or all sides of an element.
Some examples of using the border mixin follow:

```scss
@use 'finch/layout' as lyt;

button {
  @include lyt.border($width: thick);
}

a[href*="button"] {
  @include lyt.border($color: #222, $sides: (bottom, right));
}
```

#### @include border-logical

The `border-logical` mixin is much the same as the `border` mixin, except it sets the border based on logical properties (inline, block etc) rather than 'top, right' etc. Rather than specifying 'sides', the `border-logical` mixin expects a 'direction' (`$dir`) argument to be passed.
There are 2 other mixins provided:

1. **border-inline**
2. **border-block**

Which are shorthands for using the `border-logical` mixin with the direction preset accordingly.
Some examples of using these mixins follow:

```scss
@use 'finch/layout' as lyt;

.card {
  @include lyt.border-logical($width: thick, $dir: block);
  // the line above is the exact equivalent of:
  @include lyt.border-block($width: thick);
}
```

#### @include flex-center

The `flex-center` mixin uses css `flexbox` to center an elements children on one or both axis. The `$axis` and `$dir` arguments are used to determine whether or not the centering is achieved with the `justify-content` or `align-items` property. The `$axis` defaults to `both`, in which case both of these properties will be set.
Some examples of using the `flex-center` mixin follow:

```scss
@use 'finch/layout' as lyt;

.container {
  // the following usage represents centering with `justify-content`, since the default direction of `row` will be used.
  @include lyt.flex-center($axis: x);
}

.middle {
  @include lyt.flex-center($axis: y, $dir: column);
}
```

#### @include flex-blocks

The `flex-blocks` mixin addresses the specific use-case of needing a row of elements that wrap into a "grid" like structure. Each child element will respect a minimum-width if one if provided, and that will, in turn, be used by the layout to calculate at which point different elements should fall onto the next line.
The mixin sets a default minimum width of `16rem`, since it is assumed that a minimum would always be wanted, however this can be overridden by passing your own value, or "turned off" completely by passing `$block-min: null`. You can also provide a `$reverse` argument, which expects a boolean value and changes the `flex-flow` accordingly.
Some examples of using the `flex-blocks` mixin follow:

```scss
@use 'finch/layout' as lyt;

.cards-container {
  // this implementation makes use of all of the defaults.
  @include lyt.flex-blocks;
}

.cards-container--center {
  @include lyt.flex-blocks($justify: center, $align: center);
}
```

#### @include margin

The `margin` mixin sets margin on one or both axis of an element. If no `$axis` is provided, the margin will be applied to all sides.
Some examples of using the `margin` mixin follow:

```scss
@use 'finch/layout' as lyt;

.container > * {
  @include lyt.margin(large, y);
}
```

#### @include margin-logical

The `margin-logical` mixin does much the same as the `margin` mixin, but sets margin using logical properties (inline, block, etc) rather than an axis (x or y). There are also 2 other mixins that act as shorthands for `margin-logical` with the `$dir` preset according to their name.
Some examples of using the `margin-logical` mixin follow:

```scss
@use 'finch/layout' as lyt;

.container > * {
  @include lyt.margin-logical(large, inline);
  // the above implementation is identical to the following:
  @include lyt.margin-inline(large);
}
```

#### @include padding

The `padding` mixin sets padding on one or both axis of an element. If no `$axis` is provided, the padding will be applied to all sides.
Some examples of using the `padding` mixin follow:

```scss
@use 'finch/layout' as lyt;

.container > * {
  @include lyt.padding(x-large, x);
}
```

#### @include padding-logical

The `padding-logical` mixin does much the same as the `padding` mixin, but sets margin using logical properties (inline, block, etc) rather than an axis (x or y). There are also 2 other mixins that act as shorthands for `padding-logical` with the `$dir` preset according to their name.
Some examples of using the `padding-logical` mixin follow:

```scss
@use 'finch/layout' as lyt;

.container > * {
  @include lyt.padding-logical(x-large, block);
  // the above implementation is identical to the following:
  @include lyt.padding-block(x-large);
}
```

#### @include pos

The `pos` mixin is used to set the `position` property, and the associated `top, right, bottom, left` co-ordinates. The purpose for the mixin is specifically for setting a common value on more than one side. The rationale for this is that a mixin implementation that was something like: `@include pos(absolute, $top: 2rem, $right: 4rem);` is really no better than the following, standard css:
```css
.selector {
  position: absolute;
  top: 2rem;
  right: 4rem;
}
```

The mixin does, however, bring some value when applying the same value to multiple sides, as it then prevents the duplication of typing out the same value in several places. There are also three other mixins that act as a shorthand for the `pos` mixin, with the `$type` variable preset according to the name.
Some examples of using the `pos` mixin follow:

```scss
@use 'finch/layout' as lyt;

.modal {
  @include lyt.pos(absolute, 2rem, (top, left));
  // the above implementation is identical to the following:
  @include lyt.absolute(2rem, (top, left));
}

// positioning an element that perfectly covers another, could be achieved with the following code:
.parent {
  position: relative;

  .mask {
    @include lyt.absolute(0, (top, right, bottom, left));
  }
}
```

The shorthand mixins (`absolute()`, `fixed()` and `sticky()`) also accept a `$reset` boolean argument which, if true, will include the `pos-reset` mixin before any other declarations.

#### @include pos-reset

The `pos-reset` mixin is used to remove any values that have been set on an element so the its position can safely be redeclared. A use might be a menu or icon that sits at the top of the page on desktop, but the bottom on mobile. Assuming a mobile-first approach, the desktop version would need to `unset` the `bottom` value before declaring the `top`. More complicated examples than this could arise.
Some examples of using the `pos-reset` mixin follow:

```scss
@use 'finch/layout' as lyt;

.top-bar {
  @include lyt.pos-reset;
  @include lyt.fixed(0, (top, left));
  // the above implementation is identical to the following:
  @include lyt.fixed(0, (top, left), true);
}
```

#### z-index mixins

These are best understood by looking at the file itself (`/src/layout/properties/_z-indices.scss`). There are many mixins, each performing one, small task of setting the z-index to a specified "layer". Possible "layers" in *Finch* are `back, background, base, foreground, front`, although, as with everything, custom values can be set as well.

---

## Configuration

When you use the layout module, you can configure it with a number of variables that are declared as `!default` in the module's index. The layout module currently has the following configurable variables:

- $set-responsive
- $set-utilities
- $border-radii
- $border-widths
- $margins
- $paddings
- $z-indices

The top two variables (`$set-responsive` and `$set-utilities`) should be explained by themselves, but the rest can all be briefly discussed together.
As with all of *Finch's* modules, passing in a custom map under any of these variable names will see your values be merged with (or override where there are naming colisions) the default values set by *Finch*. A simple example of this follows:

```scss
// bring in the config module so we have access to the global maps like `$size-scale`.
@use 'finch/config';
// bring in the layout module and pass in our own margins.
@use 'finch/layout' as lyt with ($margins: (
  // we create 2 new keys in the `$margin-sizes` map that Finch uses internally. Note with
  // the second example (`tiniest`) we are still using our modular scale.
  tiny: .25rem,
  tiniest: map.get(config.$size-scale, 100),
  // we also override one of the Finch defaults, replacing `x-large` with our own value.
  x-large: 10rem,
));
```

### Utilities

The *set-responsive* and *set-utilities* maps are special configuration maps. Both of them hold a list of all the properties that the module handles as 'keys', and sets a boolean value against each one. These are *all* `false` by default and must be manually/intentionally overridden by the user.
The boolean values from these maps are iterated over to generate utility classes for the properties and their values, and for each breakpoint, if the `responsive` flag is `true`. Each utility class also creates an `!important` version, the class name for which simply appends a '!'.

```scss
@use 'finch/layout' as lyt with (
  $set-utilities: (
    padding: true,
    margin: true,
  ),
);

// classes like `.padding:large` and `.margin:small` are now available.
```

```scss
@use 'finch/layout' as lyt with (
  $set-utilities: (
    padding: true,
    margin: true,
  ),
  $set-responsive: (
    padding: true,
    margin: true,
  ),
);

// classes like `.padding:large`, `.margin:small!` and `.padding:small@sm`
// are now available.
```
