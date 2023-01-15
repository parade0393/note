1. 获取对象的动态属性名称

   已知对象

   ```javascript
   let obj = {
       baseInfo: {
           name: "独特"
       }
   }//对象，是后台返回的
   let attr = "baseInfo.name"//属性配置，是写在前台的
   let result = attr.split(".").reduce((pre, cur) => {
       return pre?.[cur]
   }, obj)
   console.log(result)//独特  如果有一个属性值不存在，则返回undefined
   ```

2. 分组

   ```javascript
   <script>
       const list = [
           {
               key: "第二步",
               type: 2,
               name: "郭富城"
           },
           {
               key: "第一步",
               type: 1,
               name: "刘德华"
           },
   
           {
               key: "第一步",
               type: 2,
               name: "刘罗锅"
           },
       ]
   
   
       //一步操作
       let newLit = list.reduce((res, item) => {
           let temp = res.find(el => el.type === item.type)
           if (temp) {//数组里有该type分组
               temp.list.push(item)
           } else {//数组里没有该type分组
               let obj = Object.create(null)
               obj.key = item.key
               obj.type = item.type
               obj.list = [item]
               res.push(obj)
           }
           return res
       }, []).sort((v1, v2) => v1.type - v2.type)//按type升序排序
       console.log(newLit)
       //两部操作
       // let firstResult = list.reduce((res,item) => {
       //     res[item.type] = res[item.type] || []
       //     res[item.type].push(item)
       //     return res
       // },Object.create(null))
       // console.log(firstResult)
       // const  second = Object.values(firstResult).reduce((res,item) => {
       //     let obj = Object.create(null)
       //     obj.key = item[0].key
       //     obj.type = item[0].type
       //     obj.list = item
       //     res.push(obj)
       //     return res
       // },[])
       // console.log(second)
   </script>
   ```

3. [js reduce示例运用之扁平数组与树形结构的互转，数组分组](https://blog.csdn.net/parade0393/article/details/121267624)