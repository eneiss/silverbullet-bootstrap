## Widgets

### Direct child pages query

```space-lua
function directChildPages()
  return query[[
    from p = index.tag "page"
    where string.match(p.name, "^" .. editor.getCurrentPage() .. '/[^/]+$' )
    order by p.name
    select {
      Child_page = "[[" .. p.name .. "]]",
    }
  ]]
end
```

### Last edited child pages query

```space-lua
function lastEditedChildPages()
  return query[[
  from p = index.tag "page"
  where p.name:startsWith(editor.getCurrentPage() .. "/")
  order by p.lastModified desc
  limit 5
  select {
    page = p.name,
    created = p.created:sub(9,10) .. "/" .. p.created:sub(6,7) .. "/" .. p.created:sub(1,4) .. " " .. p.created:sub(12,16),
    lastModified = p.lastModified:sub(9,10) .. "/" .. p.lastModified:sub(6,7) .. "/" .. p.lastModified:sub(1,4) .. " " .. p.lastModified:sub(12,16),
    ref = p.ref
    }
  ]]
end
```

## Tasks

### Priority ordering

Ordering and rendering helpers for the per-page task queries.

```space-lua
-- Highest first; insert new priorities where they belong, no numbers to bump.
taskPriorityOrder = { "top", "high", "medium", "low" }

local ranks = {}
for i, p in ipairs(taskPriorityOrder) do
  ranks[p] = i
end

-- Unknown values sort just above the unprioritized bucket, so typos surface.
function taskPriorityRank(p)
  if not p then return 999 end
  return ranks[p] or 998
end

function taskLink(t)
  return "[[" .. t.page .. "@" .. t.pos .. "|" .. t.name .. "]]"
end

function taskItem(t)
  return t.name .. " [[" .. t.page .. "@" .. t.pos .. "| 🔗 ]]"
end

templates.prioTaskItem = template.new([==[
* [${state}] ${taskLink(_)}${priority and " `" .. priority .. "`" or ""}
]==])
```

### Tasks in child pages

```space-lua
function childTasks()
  local prefix = editor.getCurrentPage() .. "/"
  local tasks = query[[
    from t = index.tag "task"
    where not t.done and t.page:startsWith(prefix)
    order by t.page, t.pos
    select {
      Task = "[[" .. t.page .. "@" .. t.pos .. "|" .. t.name .. "]]",
      Page = "[[" .. t.page .. "]]",
    }
  ]]
  if #tasks == 0 then
    return "_No open tasks below this page._"
  end
  return tasks
end
```
