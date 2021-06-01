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
@use '../node_modules/finch/layout' as lyt;

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
@use '../node_modules/finch/layout' as lyt;

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
@use '../node_modules/finch/layout' as lyt;

article {
  @include lyt.stack;
}

.contact-card {
  @include lyt.stack($flow: small, $recursive: true);
}
```

Stack utility classes are also generated (depending on the `stack` value in the `$utilities` map). These classes (one for each margin) take the form of `.stack:base`, `.stack:large` etc *and* `.stack-recursive:base`, `.stack-recursive:small` etc.
