### 模板编译阶段

- 将template中的dom先转ast抽象语法树

- 然后将ast抽象语法树转换为render方法

- 通过render函数，转化为虚拟dom

- 然后虚拟dom转换为真实dom

  - 更新的时候，重新生成虚拟dom
- 更新dom 
  
1. 将template转成ast语法树
  
     将html字符串解析成ast语法树:使用正则将dom匹配
  
  2. ```html+js
     <div id='app'>
     	<p>hello</p>
     	<a>123</a>
     </app>
     ```
```
     
解析函数html-parse,使用正则拿到标签节点值，属性节点值，文本节点值
     
     如何知道一个标签是否正确关闭呢？
     
将标签节点放入一个数组，匹配到结束标签的时候，看是否和末尾一个标签相同，如果相同则删除最后一个，依次删除之后看数组长度是否为0
     
[div,p,a]
     
     
     
     createASTElement函数，生成ast语法树
     
     ```js
     
```

     转换成语法树如下

  3. ```js
     let root = {
       tag: 'div',
       attrs: [{name:'id',value: 'app'}],
       parent: null,
       type: 1,
       children:[
         {
           tag: 'p',
           attrs: [],
           parent:root,
         type: 1,
           child:[
           {
               text: 'hello',
               type: 3
             }
           ]
         }
       ]
     }
     ```
  
  2. ast树转成js语法
  
  5. ast生成render函数

     ps: 关于正则的一个坑

     ```js
     let reg = /a/g
     reg.test('abc') // true
     reg.test('abc') // false
     // 所以每次匹配之后要让lastIndex 设置为0
     // exec同样 exec:找到了匹配的文本，则返回一个结果数组。否则，返回 null。
     reg.exec
     ```

     实现模板引擎  1 拼接字符串 2 增加with  3 new Function

     ```js
     // 将<div id="app"><p>hello {{name}}</p>hello</div> 生成render函数如下：
     _c("div",{id:app},_c("p",undefined,_v("hello"+_s(name))),_v('hello'))
     
     
     
     
     
     
     ```

  6. render生成一个对象

     ```js
     function vnode(){
       return {
         tag,
         data,
         key,
         children,
         text
       }
     }
     function createTextNode(text){
       return vNode(undefined,undefined,undefined,undefined,text)
     }
     function createElementNode(tag,data={},...children){
     	let key = data.key; // v-for的时候的key值 现在先用不到
       if(key){
         delete data.key
       }
       return vNode(tag,data,key,children,undefined)
     }
     // 可以等到 元素节点如下
     {
       tag:'div'
       key: undefined,
       data: {
         id: 'app',
         style: {color:'red',background: 'blue'}
       },
       children:[
         {
           tag: 'li',
           ……
         }
       ],
       key: undefined,
       text: undefined
     }
     ```

  7. 用虚拟阶段创建真是节点，替换掉真实的$el; 只有第一次需要生成ast语法树(ps:虚拟dom和ast类似)

     ```js
     // patch方法
     export function patch(oldvnode,vnode){
       // 判断需要跟新还是渲染
       const isRealElement = oldVnode.nodeType;
       if(isRealElement){
         const oldElm = oldVnode;
         const parentElm = oldElm.parentNode; //body
         let el = createElm(vnode);
     		// 将虚拟dom放到真实dom的兄弟👬节点之后再删除这个真实dom(过河拆桥)
         parentElm.insertBefore(el,oldElm.nextSibling)
         parentElm.removeChild(oldElm)
       }
     }
     
     function createElm(vnode){
       let {tag,children,key,data,text} = vnode;
       // 是标签就创建这个标签
       if(typeof tag === 'string'){
         vnode.el = document.createElement(tag)
         updateProperties(vnode)
         children.forEach(child => { //递归创建节点
           return vnode.el.appendChild(createElm(child))
         })
       }else{
         
       
       // 否则是文本直接插入
       }
       return vnode.el;
     }
     
     function updateProperties(vnode){
       let newProps = vnode.data;
       let el = vnode.el;
       for(let key in newProps){
         if(key === 'style'){
           for(let styleName in newProps.style){
             el.style[stylename] = newProps.style[styleName]
           }
         }else if(key === 'class'){
           el.className = newProps.class;
         }else{
           el.setAttribute(key, newProps[key])
         }
       }
     }
     ```

     8. 

     

     

     

  ```
     
     
  
  
  
  
  ```

渲染更新使用_update方法 虚拟节点渲染为真实dom

虚拟dom: 

虚拟dom到真实dom：

模板引擎只能通过with实现：



- 通过声明周期加上自己的需求：
- 生命周期的合并策略
  - 就是如何实现两个对象的合并
  - mixin中声生命周期合并成一个数组，依次调用；
- 依赖收集
  - 取值时，会进行依赖收集，更改数据时候，会进行对应的watch调用更新操作，dep和watcher是一个多对多的关系，dep存放着相关的watcher，是一个观察者模式







源码

面试常问 对象只会拦截已存在的属性，  更改所有㛑不会发声视图更新

ob.dep.notify()

生命周期合并 mergeOptions

依赖收集  ：管擦着

对象就是set get  数组就是重写方法 ，这样来实现数据的响应





