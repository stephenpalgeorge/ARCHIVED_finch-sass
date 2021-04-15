- think about what to do with font-variant.

- font-kerning should be used in text-styles, might be ok to leave as the default actually.


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
