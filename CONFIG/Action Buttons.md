---
pageDecoration:
  icon: mouse-pointer
---

_Action buttons are the buttons you see in the header of all Silverbullet pages. They are configurable!_

### Sidebar (tree view)

```space-lua
actionButton.define {
  icon = "sidebar",
  description = "Open Tree View",
  run = function()
    editor.invokeCommand("Navigate: Tree")
  end
}
```

### Search (Silversearch)

```space-lua
actionButton.define {
  icon = "search",
  run = function()
    editor.invokeCommand("Silversearch: Search")
  end
}
```
