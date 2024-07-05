<%*
  let filename = tp.file.title

  let title = filename.split('-').map(x => x.charAt(0).toUpperCase() + x.slice(1)).join(' ')
  tR += "---"
%>
title:  <%* tR += title %>
created: <% tp.date.now("YYYY-MM-DD") %>
draft: true
publish: false
tags: 

---
# <%* tR += title %>
<%* app.workspace.activeLeaf.view.editor?.focus(); %>
