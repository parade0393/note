此篇文章来源于掘金社区大佬文章[扁平数组与JSON树结构互转](https://juejin.cn/post/6844903847484211213)，在这里只是学习之后加上了注释，再次感受到了js的强大

扁平数组如下

```javascript
let flatArr = [
    {id: 1, title: "解忧杂货铺1", parent_id: 0},
    {id: 2, title: '解忧杂货铺2', parent_id: 0},
    {id: 3, title: '解忧杂货铺2-1', parent_id: 2},
    {id: 4, title: '解忧杂货铺3-1', parent_id: 3},
    {id: 5, title: '解忧杂货铺4-1', parent_id: 4},
    {id: 6, title: '解忧杂货铺2-2', parent_id: 2},
]
```

将扁平数组转树形方法如下

```javascript
/**
 *
 * @param {Array} list  原始扁平数组
 * @param {String} cIdName 代表孩子节点的属性名称
 * @param {String} pIdName 代表父节点的属性名称
 * @param {String} treeFieldName 返回的树形数据的子节点的属性名称
 * @param  rootPidValue 代表根节点的属性值
 * @returns res JSON树形机构数组
 */
function convertPlatListToTreeData(list,cIdName,pIdName,treeFieldName,rootPidValue = null) {
    const res = [];
    //(res[v[cIdName]] = v, res),这段代码用了逗号操作符，即（对它的每个操作数求值（从左到右），并返回最后一个操作数的值），剩余的就是reduce的常规操作
    const map = list.reduce((res, v) => (res[v[cIdName]] = v, res), {});
    for (const item of list) {
        if (item[pIdName] === rootPidValue) {
            res.push(item);
            continue;
        }//这一的目的是计算出父级元素
        //in 操作符 检测属性是否在对象中
        if (item[pIdName] in map) {
            const parent = map[item[pIdName]];//map里的对象和res里的是同一个对象
            parent[treeFieldName] = parent[treeFieldName] || [];//这里只是容错，因为刚开始没有这个字段,这时候则赋值为空数组
            parent[treeFieldName].push(item);
        }
    }
    return res;
}
let returnTree = convertPlatListToTreeData(flatArr,"id","parent_id","items",0);
console.log(returnTree);
```

树形结构转扁平结构（这里我没有归纳成通用的方法）

```javascript
function flatten(data) {
    //{id, title, pid, children = []}，这段代码利用了对象的展开语法
    return data.reduce((arr, {id, title, pid, children = []}) =>
        arr.concat([{id, title, pid}], flatten(children)), []);
}
```

数组按照某一个字段进行分组

```javascript
let cars = [
    {make: 'audi', model: 'r8', year: '2012'},
    {make: 'audi', model: 'rs5', year: '2013'},
    {make: 'ford', model: 'mustang', year: '2012'},
    {make: 'ford', model: 'fusion', year: '2015'},
    {make: 'kia', model: 'optima', year: '2012'}];
function groupArr(arr, p) {
    return arr.reduce((r, a) => {
        r[a[p]] = r[a[p]] || []
        r[a[p]].push(a)
        return r
    }, Object.create(null))
}
console.log(groupArr(cars, "make"));
```

