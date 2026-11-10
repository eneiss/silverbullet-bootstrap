---
pageDecoration:
  icon: dollar-sign
---

## Navigation

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
