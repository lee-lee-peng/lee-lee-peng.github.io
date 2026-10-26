# 纯原生Javascript全选checkbox

```javascript
var cbs = document.querySelectorAll('input[type="checkbox"]');
for(var i=0; i < cbs.length; i++){
 cbs[i].checked = true;
}
```