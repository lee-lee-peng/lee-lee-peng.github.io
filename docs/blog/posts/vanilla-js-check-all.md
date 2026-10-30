---
date: 2026-10-30
authors:
  - lee
categories:
  - Javascript
  - 编程
tages:
  - javascript
---
# 纯原生Javascript全选checkbox

```javascript
var cbs = document.querySelectorAll('input[type="checkbox"]');
for(var i=0; i < cbs.length; i++){
 cbs[i].checked = true;
}
```