---
title: Custom Themes
---

Custom Themes are CSS targetting specific elements of the Redactor editor. They can target just the editor and toolbar, or also affect the actual styling of the content inside it.

[TOC]

## Theme Requirements

- The theme css file must be loaded into the manager. You can add the URL for a CSS file to the `redactor.css` system setting to load it whenever Redactor is used.
- The theme must be enabled by setting its name into the Appearance > Toolbar theme setting. 
- All CSS rules **must** be scoped to `.redactor-theme-<THEMENAME>.redactor-box`. If you use Sass, use this as a wrapping rule. 

Use the browsers' developer tools to inspect the markup of the editor to find the right classes to target your custom styling.

Note that some elements in the content might have a different markup in the editor compared to the markup that is outputted or shown in source view. 

## Example

Changing the background color of the toolbar for theme "mycolor" with Sass:

``` scss
.redactor-theme-mycolor.redactor-box {
    .redactor-toolbar-wrapper {
        background: #FF9900;
        padding-bottom: 16px;
        border-radius: 2px 2px 0 0;
    }
}
```

or plain CSS:

```css
.redactor-theme-mycolor.redactor-box  .redactor-toolbar-wrapper {
    background: #FF9900;
    padding-bottom: 16px;
    border-radius: 2px 2px 0 0;
}
```