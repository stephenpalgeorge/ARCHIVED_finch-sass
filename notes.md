- typography module:
  - add 'text-shadow' property.

- layout module properties:
  - display: might be worth breaking this up into 3 or 4 different files as it will include grid and flexbox.

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
