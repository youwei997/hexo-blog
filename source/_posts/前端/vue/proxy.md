---
title: vue proxy
tags: vue
categories: 前端
date: 2023-11-11 16:15:23
---
<meta name="referrer" content="no-referrer"/>

```html
<script>

    const person = {
        name: 'John',
        age:12,
    }
    const p = new Proxy(person,{
        get(target,propName){
            console.log('读取p的'+propName+'属性')
            return target[propName]
        },
        set(target,propName,value){
             console.log('修改或新增p的'+propName+'属性')
             target[propName] = value
        },
        deleteProperty(target,propName){
            console.log('删除p的'+propName+'属性')
            return delete target[propName]
        }
    });
</script>
```

