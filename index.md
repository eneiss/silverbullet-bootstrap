---
pageDecoration:
  icon: home
---

# Recent incomplete tasks
${some(query[[
  from t = index.tasks()
  where not t.done
  order by t.pageLastModified
  desc limit 10
  select templates.taskItem(t)
]]) or "_All tasks done!_"}

# Recently modified pages
${query[[
  from p = index.contentPages()
  order by p.lastModified desc
  limit 10
  select templates.fullPageItem(p)
]]}
