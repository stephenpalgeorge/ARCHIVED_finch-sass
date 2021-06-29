# Pseudo Module 

The *Finch Pseudo Module* handles everything to do with css pseudo classes and pseudo elements. The pseudo module alone should give you all the tools you need to style states like `hover`, `focus` etc and elements like `before` and `after`. This document outlines the mixins and functions that this module exposes.

A general point worth making here is that most pseudo classes/elements are not really helped by a wrapping mixin. Classes like `hover` and `focus` make sense, because we can add functionality (like only applying the styles to an element that *isn't* disabled), but there's no need for something like `focus-within` or `nth-last-child` to have a mixin. Similarly, these sorts of properties don't lend themselves easily to utility functions, since the actual style rules declared within that selector could be *anything*. I.e. there's no real difference or benefit to writing `@include first-letter { color: red; ... }` instead of `::first-letter { color: red; ... }`. The mixins included in this module are, therefore, only included where there is a genuine opportunity to add functionality or provide common boilerplate styles.

## Pseudo classes

### @include hover

The hover mixin provides a wrapper for writing styles that apply to an element when the user hovers over it. The styles are only applied if the element is *not* disabled. We also check for the presence of a `disabled` class, assuming that any dev applying such a class would be intending the same results as the `disabled` attribute.

### @include focus

The focus mixin works identically to the `hover` mixin, but applies styles to the `focus` state of an element.

### @include from

The from mixin is part of the child-selector family of utility selectors. It selects a target element and all its siblings from a given index. For example, consider the following markup:

```html
<div>
  <p>First paragraph</p>
  <p>Second paragraph</p>
  <p>Third paragraph</p>
  <li>Some other sibling</li>
</div>
```

Using the following sass code would select the 'Second paragraph', 'Third paragraph' and the `<li>`:

```scss
div {
  @include from(2) {
    color: red;
  }
}
```

The mixin also accepts an optional `filter` parameter, which expects any valid css selector, and will then only select the siblings of the target element that match. So, considering the same markup as given above, the following code would *only* select the 'Secord paragraph' (since this is the target element), and the `<li>`:

```scss
div {
  @include from(2, li) {
    color: red;
  }
}
```

### @include to

Much the same as the `from` mixin above, the `to` mixin selects a the first 'n' elements of the target type. Using the same markup as the `from` examples above, this scss code would select 'First paragraph', 'Second paragraph' and 'Third paragraph':

```scss
div {
  @include to(3) {
    color: red;
  }
}
```

### @include between

As the name suggests, this mixin targets elements of the target type that are between a starting and ending index. Again, using the same markup as above, the following scss code would select 'Second paragraph' and 'Third paragraph':

```scss
div {
  @include between(2, 3) {
    color: red;
  }
}
```

## Pseudo elements

### @include before, @include after

These 2 mixins set content for the `::before` and `::after` pseudo elements. The value of these mixins is that they add some common functionality in setting the position of the parent element, and pseudo element to some common defaults (which can of course be over-ridden). They also allow for passing content in the mixin's signature, so the style rules do not have to mix content and presentation, as in the following examples:

```scss
blockquote {
  @include before('\201C') {
    top: 0;
    left: -1em;
    font-size: 2em;
    color: rgba(red, .2);
  }
}

.count {
  @include after(attr(data-suffix), relative) {
    margin-left: .75rem;
  }
}
```
