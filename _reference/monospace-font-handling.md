---
url: "https://code.iamkate.com/html-and-css/fixing-browsers-broken-monospace-font-handling/"
author: "Kate Rose Morley"
snapshot_date: "24/05/2021"
---

# Fixing browsers’ broken monospace font handling

Major web browsers automatically reduce the size of monospace text in a range of situations. They do this due to the greater width of many monospace typefaces in comparison to other fonts at the same text height. While well-intentioned, their handling of monospace fonts is fundamentally broken, and an unusual CSS rule is required to fix the problem.

## The solution
The following style rule restores the normal text size without having any other effect on the text.

```css
pre,
code,
kbd,
samp,
tt {
  font-family: monospace, monospace;
  font-size: 1em;
}
```

Lines 1 to 5 refer to the five HTML elements that default to a monospace typeface in most browsers. Line 6 restores the normal text size in Mozilla Firefox, Google Chrome, and Safari; this unusual style rule should also be used anywhere where you would otherwise set the font-family property to ‘monospace’. Line 7 restores the normal text size in Internet Explorer and Opera.

## Situations where browsers reduce the text size
All of the major browsers reduce the text size for HTML elements that default to a monospace typeface: pre (preformatted text), code (programming code), kbd (keyboard input), samp (sample output), and tt (typetype text).

In addition Mozilla Firefox, Google Chrome, and Safari reduce the font size within elements whose font-family property is explicitly set to ‘monospace’.

## Situations where browsers do not reduce the text size
None of the major browsers reduce the text size within an element that has its font size specified in absolute units (such as pixels). Because the font size can be set on any ancestor element, it is possible to disable font scaling entirely by setting an absolute font size on the body element. However, absolute units harm accessibility (as some older web browser cannot resize text sized in absolute units), and so should not be used.

Internet Explorer and Opera also do not shrink text within an element that has its font size specified in relative units (such as ems). This feature is used in the solution above, where the font size is set to 1em — the value it would otherwise have been if the browser had not shrunk the text.

In Google Chrome and Safari, setting the font family to anything other than just the generic family ‘monospace’ disables the text scaling. The solution above uses the value ‘monospace,monospace’, which works despite being logically equivalent to just ‘monospace’. Alternatively you can refer to specific fonts — for example, ‘Consolas,monospace’.

Mozilla Firefox behaves like Google Chrome and Safari, except that changing the font family to a value other than ‘monospace’ does not work if an ancestor element has its font family set to ‘monospace’. In effect, the reduction is font size is inherited by child elements, regardless of their font family.

## How browsers should handle monospace fonts
If web browser makers feel it really is necessary to automatically resize text within the HTML elements that default to a monospace typeface, the correct way to do this is to use a CSS rule such as the following in their user agent stylesheets:

```css
pre,
code,
kbd,
samp,
tt{
  font-size:0.8125em;
}
```

This results in the same reduction in font size produced by the current method (that is, text that would usually be 16 pixels high is instead rendered at 13 pixels high), but allows website authors to change the font size in a more intuitive way by using the CSS font-size property.