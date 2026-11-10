---
pageDecoration:
  icon: terminal
---

## Tasks

### Sort tasks in paragraph

_not recursive on purpose, but could be adapted to be recursive_

```space-lua
command.define {
  name = "Sort tasks around cursor",
  run = function()
    local text = editor.getText()
    local cursor = editor.getCursor()

    -- Split into lines and record 0-based start offsets
    local lines, starts = {}, {}
    local p = 1
    while true do
      local nl = string.find(text, "\n", p, true)
      table.insert(starts, p - 1)
      if nl then
        table.insert(lines, string.sub(text, p, nl - 1))
        p = nl + 1
      else
        table.insert(lines, string.sub(text, p))
        break
      end
    end

    -- Locate the line under the cursor
    local cur = 1
    for i = 1, #lines do
      if starts[i] <= cursor then cur = i else break end
    end

    local function blank(s) return s:match("^%s*$") ~= nil end
    if blank(lines[cur]) then return end

    -- Expand to the surrounding paragraph (blank lines as boundaries)
    local s, e = cur, cur
    while s > 1     and not blank(lines[s - 1]) do s = s - 1 end
    while e < #lines and not blank(lines[e + 1]) do e = e + 1 end

    -- Detect a top-level task line: returns indent length and "done" flag
    local function task(line)
      local ind, mark = line:match("^(%s*)%- %[([ xX])%]")
      if ind then return #ind, mark ~= " " end
    end
    -- Use the smallest task indent in the paragraph as the "top level"
    local minIndent
    for i = s, e do
      local ind = task(lines[i])
      if ind and (not minIndent or ind < minIndent) then minIndent = ind end
    end
    if not minIndent then return end

    -- Group each top-level task with the lines that follow it (until the next one)
    local pre, groups, g = {}, {}, nil
    for i = s, e do
      local ind, done = task(lines[i])
      if ind == minIndent then
        g = { done = done, lines = { lines[i] } }
        table.insert(groups, g)
      elseif g then
        table.insert(g.lines, lines[i])
      else
        table.insert(pre, lines[i])
      end
    end

    -- Stable partition: undone first, done last
    local out = {}
    for _, l in ipairs(pre) do table.insert(out, l) end
    for _, gr in ipairs(groups) do
      if not gr.done then
        for _, l in ipairs(gr.lines) do table.insert(out, l) end
      end
    end
    for _, gr in ipairs(groups) do
      if gr.done then
        for _, l in ipairs(gr.lines) do table.insert(out, l) end
      end
    end

    local from = starts[s]
    local to   = starts[e] + #lines[e]
    editor.replaceRange(from, to, table.concat(out, "\n"))
  end
}
```

### Move task page to done/archived section

```space-lua
local function moveTaskPage(section)
  local current = editor.getCurrentPage()
  local topic, name = string.match(current, "^Tasks/([^/]+)/(.+)$")
  if not topic or topic == "Done" or topic == "Archived" then
    error("Current page is not Tasks/<Topic>/<page name>: " .. current)
  end
  local parent = string.match(current, "^(.*)/") -- greedy: up to the last slash

  -- renamePageCommand follows the page only while it is the active one.
  editor.navigate({ kind = "page", page = parent })
  system.invokeFunction("index.renamePageCommand", {
    oldPage = current,
    page = "Tasks/" .. section .. "/" .. topic .. "/" .. name
  })
  -- Backlink rewrites land inside the client's 5s post-save reload suppression.
  editor.reloadPage()
end

command.define {
  name = "Tasks: Move to Done",
  run = function() moveTaskPage("Done") end
}

command.define {
  name = "Tasks: Move to Archived",
  run = function() moveTaskPage("Archived") end
}
```

## Movements

### Move to top of page (ctrl+h)

```space-lua
-- unbind ctrl+h
command.update {
  name = "Editor: Delete Char Backward",
  key = "",
  mac = ""
}

command.define {
  name = "Go to top",
  key = "Ctrl-h",
  run = function()
    editor.moveCursor(0, true)
  end
}

command.define {
  name = "New h2 header on top of page",
  key = "Alt-h",
  run = function()
    editor.moveCursor(0, true)
    editor.insertAtCursor("## |^|\n\n", false, true)
  end
}
```
