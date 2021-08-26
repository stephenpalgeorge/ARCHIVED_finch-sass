- global:
  - add `animations` module.
  - add `form` module.

- typography module:
  - add 'text-shadow' property.

- layout module:
  - add 'box-shadow' stuff.
  - add 'outline' stuff.
  - add 'object-fit' stuff.

## possible text style considerations:
```scss
.text-content {
  // default text styles:
  @include type.text-style;
  // default `font-variant` values:
  @include type.text-alter;
  word-break: break-all;
  hyphens: manual;
}

.lead-content {
  @include type.text-style($size: lead);
  @include type.text-alter;
}
```

### deprecated

```scss
/*
The code below has been removed for the time being. The decision has been taken that 
layouts should not be included if they depend on a particular setup/structure in the
markup.

We may revisit this at a later date, or implement some kind of Finch UI library...

/// CLUSTER [LAYOUT PRIMITIVES]
/// ----------
@mixin cluster(
  $gap: map.get(config.$size-scale, 400),
  $justify: flex-start,
  $align: center
) {
  overflow: hidden;

  > * {
    display: flex;
    flex-wrap: wrap;
    justify-content: $justify;
    align-items: $align;
    margin: ($gap / 2) * -1;

    > * {
      margin: $gap / 2;
    }
  }
}

/// SIDEBAR [LAYOUT PRIMITIVES]
/// ----------
/// @param $gap {Number} one of the key values for the modular scale map
/// @param $side-width the ideal size of the 'sidebar' element
/// @param $break-at the 
@mixin sidebar($gap: 400, $side-width: 20rem, $break-at: 50%, $side: left) {
  $gap-value: map.get(config.$size-scale, $gap);

  %main-content {
    flex-basis: 0;
    flex-grow: 999;
    min-width: $break-at - $gap;
  }
  
  > * {
    display: flex;
    flex-wrap: wrap;
    margin: ($gap-value / 2) * -1;
  }

  > * > * {
    margin: $gap / 2;
    flex-basis: $side-width;
    flex-grow: 1;
  }

  @if $side == left or $side == 'left' {
    &:last-child { @extend %main-content; }
  }

  @else if $side == right or $side == 'right' {
    &:first-child { @extend %main-content; }
  }

  @else {
    @warn "side value '#{$side}' not recognised. Shoulde be 'left' or 'right'.";
  }
}
*/
```
