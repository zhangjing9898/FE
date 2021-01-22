# Juicer 轻量级javascript模板引擎


juicer是一个javascript轻量级模板引擎。

## usage

- 编译模板并根据数据立即渲染出结果

```javascript
juicer(tpl, data);
```

- 仅编译模板暂不渲染，返回一个可重用的编译后的函数

```javascript
var compiled_tpl = juicer(tpl);
```

- 根据给定的数据对之前编译好的模板进行渲染

```javascript
var complied_tpl = juicer(tpl);
var html = complied_tpl.render(data);
```

- 注册/注销自定义函数（对象）

```javascript
juicer.register(‘function_name’, function);
uicer.unregister(‘function_name’);
```

- 默认参数配置

```
{
     cache: true [false];
     script: true [false];
     error handling: true [false];
     detection: true [false];
}
```

- 修改默认配置，逐条修改

```javascript
juicer.set('cache', false);
```

- 修改默认配置，批量修改

```javascript
juicer.set({
   'script': false,
   'cache': false
})
```
## notice：
**Juicer 默认会对编译后的模板进行缓存，从而避免同一模板多次数据渲染时候重复编译所耗的时间， 如无特殊需要，强烈不建议关闭默认参数中的 cache，这么做将会令 Juicer 缓存失效从而降低性能.**

-----

## 语法

- ${变量} 
  + 使用`${}`输出变量，其中_ 为对数据源的引用（${_})。支持使用自定义函数

```javascript
${name}
${name|function}
${name|function, arg1, arg2} 
```

```javascript
var = links: [{href: 'http://juicer.name', alt: 'Juicer’},
                      {href: 'http://benben.cc', alt: 'Benben’},
                      {href: 'http://ued.taobao.com', alt: 'Taobao UED’}   
                     ]};
var tpl = [ '{@each links as item}’,
                '${item|links_build} <br />’,   
                 '{@/each}'].join('’);
var links = function(data) {        
　　return '<a href="' + data.href + '" alt="' + data.alt + '" />’;
};
juicer.register('links_build', links); //注册自定义函数
juicer(tpl, json);
```

- 转义/避免转义
  * `${变量}`在输出之前会对其内容进行转义，如果你不想输出结果被转义，可以使用`$${变量}`来避免这种情况。

```javascript
var json = {
      value: '<strong>juicer</strong>'
};

var escape_tpl='${value}';
var unescape_tpl='$${value}';

juicer(escape_tpl, json); //输出 '&lt;strong&gt;juicer&lt;/strong&gt;'
juicer(unescape_tpl, json); //输出 '<strong>juicer</strong>'
```

- 循环遍历 `{@each} ... {@/each}`
    + 遍历数组，`${index}`当前索引
  
```javascript
{@each list as item, index}
    ${item.prop}
    ${index} //当前索引
{@/each}
```

- 判断 `{@if} ... {@else if} ... {@else} ... {@/if}`
  
```javascript
{@each list as item,index}
    {@if index===3}
        the index is 3, the value is ${item.prop}
    {@else if index === 4}
        the index is 4, the value is ${item.prop}
    {@else}
        the index is not 3, the value is ${item.prop}
    {@/if}
{@/each}
```

- 注释 {# 注释内容}

```javascript
{# 这里是注释内容}
```

- 辅助循环`{@each i in range(m, n)}`

```javascript
{@each i in range(5, 10)}
   ${i}; //输出 5;6;7;8;9;
{@/each}
```

- 子模板嵌套`{@include tpl, data}`

html：
```html
<script type="text/juicer" id="subTpl">
      I'm sub content, ${name}
</script>
```
JavaScript：
```javascript
var tpl = 'Hi, {@include "#subTpl", subData}, End.';

juicer(tpl, {
       subData: {
           name: 'juicer'
       }
});

//输出 Hi, I'm sub content, juicer, End.
//或者通过数据引入子模板，下述代码也将会有相同的渲染结果：

var tpl = 'Hi, {@include subTpl, subData}, End.';

juicer(tpl, {
        subTpl: "I'm sub content, ${name}",
        subData: {
               name: 'juicer'
        }
});
```

## 一个完整的例子

```html
<script id="tpl" type="text/template">
    <ul>
        {@each list as it,index}
            <li>${it.name} (index: ${index})</li>
        {@/each}
        {@each blah as it}
            <li>
                num: ${it.num} <br />
                {@if it.num==3}
                    {@each it.inner as it2}
                        ${it2.time} <br />
                    {@/each}
                {@/if}
            </li>
        {@/each}
    </ul>
</script>
```

```javascript
var data = {
    list: [
        {name:' guokai', show: true},
        {name:' benben', show: false},
        {name:' dierbaby', show: true}
    ],
    blah: [
        {num: 1},
        {num: 2},
        {num: 3, inner:[
            {'time': '15:00'},
            {'time': '16:00'},
            {'time': '17:00'},
            {'time': '18:00'}
        ]},
        {num: 4}
    ]
};

var tpl = document.getElementById('tpl').innerHTML;
var html = juicer(tpl, data);
```
