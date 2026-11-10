## Action buttons

### Sidebar (tree view)

```space-lua
actionButton.define {
  icon = "sidebar",
  description = "Toggle Tree View",
  run = function()
    editor.invokeCommand("Tree View: Toggle")
  end
}
```

### Search (silversearch)

```space-lua
actionButton.define {
  icon = "search",
  run = function()
    editor.invokeCommand("Silversearch: Search")
  end
}
```
