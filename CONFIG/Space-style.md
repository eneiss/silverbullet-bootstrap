---
pageDecoration:
  icon: eye
---


## Space-style

### Blue headers

```space-style
#sb-main .cm-editor {
  .sb-line-h1,
  .sb-line-h2,
  .sb-line-h3,
  .sb-line-h4,
  .sb-line-h5,
  .sb-line-h6 { color: royalblue;
  }
}

#sb-main .cm-editor {
  font-size: 13px;
}

.tree__label span {
  font-size: 15px;
}
```


### Change config file display in TreeView

```space-style
.sb-nav-row div[data-path="CONFIG"] {
  /*font-weight: bold;*/
  color: yellow;
}
```

### Page width

Increase page width to use 90% of horizontal space, and disable header text indent (can overflow on the left with full page width otherwise)

```space-style
html {
  --editor-width: 90% !important;
}

.sb-header-inside.sb-line-h1 {
    text-indent: 0ch !important;
}
.sb-header-inside.sb-line-h2 {
    text-indent: 0ch !important;
}

.sb-header-inside.sb-line-h3 {
    text-indent: 0ch !important;
}

.sb-header-inside.sb-line-h4 {
    text-indent: 0ch !important;
}

.sb-header-inside.sb-line-h5 {
    text-indent: 0ch !important;
}

.sb-header-inside.sb-line-h6 {
    text-indent: 0ch !important;
}
```

