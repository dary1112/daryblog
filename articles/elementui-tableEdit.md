---
title: 使用element-ui完成表格批量编辑
date: 2021-01-15 21:11

categories:
- 大前端
tags:
- JavaScript
- vue

---

**最近开发中遇到了这个需求,这里总结写一下实现方案。**

<br>

@[toc]

<br>

## 一、数据规划

首先,简化后的数据列表长这样( `limitList`是我这次需求里面的列表命名：限制参数列表)

| 字段名       | 类型   | 示例值       | 含义                              |
| ------------ | ------ | ------------ | --------------------------------- |
| limitList    | array  |              | 限制参数列表                      |
| - id         | number | 1            |                                   |
| - moduleName | string | 模块甲       | 配置所属模块名                    |
| - configDesc | string | 这是测试配置 | 配置描述信息                      |
| - dataStatus | number | 1            | 是否立即生效,1代表有效,-1代表无效 |

模块名也是后端返回的模块列表，只能在这些项里选择不能手动填写

```json
moduleList: [
  { id:1, moduleName:'模块甲' },
  { id:2, moduleName:'模块乙' },
  { id:3, moduleName:'模块丙' }
]
```

是否有效字段`dataStatus`后端返回的1和1，1代表有效，-1代表无效，所以我定义了一个 `dataStatusMap`

```json
dataStatusMap:[{ value:1,label:"是" }, { value:-1, label:"否" }]
```

第一件事是获取并用表格展示数据，但是又涉及到批量即时编辑，所以封装了一个方法从后端获取到`limitList`之后立即拷贝一份出来用于编辑

```js
getLimitList () {
this.limitList=await this.$http.getLimitList()
this copyLimitList= JSON.parse(JSON.stringify(this.limitList))
}
```

表格结构先定义出来

```json
limitColumns: [
  { type: 'index', width: 60, align: 'center' },
  { title: '所属模块', slot: 'moduleName' },
  { title: '配置描述', slot: 'configDesc' },
  { title: '是否有效', slot: 'dataStatus' },
	{ title: '勾选', slot: 'action' }
]
```



## 二、表格展示

表格需要即时编辑，所以要定义`slot`,`template`里需要两套结构，分别是默认状态和编辑状态

```html
<Table
  height="520"
  style="border: none"
  data="limitList"
  columns="limitColumns"
  stripe
  show-header="true"
>
  <template slot-scope="{ row, index }" slot="moduleName">
    <Select v-model="copyLimitList[index].moduleName" v-if="copyLimitList[index] && copyLimitList[index].isEdit">
      <Option
        v-for="moduleName in moduleList"
        key="moduleName.moduleName"
        label="moduleName.moduleName"
        value="moduleName.moduleName"
      ></Option>
    </Select>
    <span v-else>{{ row.moduleName }}</span>
  </template>
  <template slot-scope="{ row, index }" slot="configDesc">
    <Input type="text" v-model="copyLimitList[index].configDesc" v-if="copyLimitList[index] && copyLimitList[index].isEdit" />
    <span v-else>{{ row.configDesc }}</span>
  </template>
  <template slot-scope="{ row, index }" slot="dataStatus">
    <Select v-model="copyLimitList[index].dataStatus" v-if="copyLimitList[index] && copyLimitList[index].isEdit">
      <Option
        v-for="dataStatus in dataStatusMap"
        key="dataStatus.value"
        label="dataStatus.label"
        value="dataStatus.value"
      ></Option>
    <Select>
    <span v-else>{{ row.dataStatus | dataStatusToChinese }}</span>
  </template>
  <template slot-scope="{ row, index }"slot="action">
    <Checkbox
      v-if="copyLimitList[index]"
      v-model="limitList[index].isEdit"
    ></Checkbox>
  </template>
</Table>

```



> 细心的童鞋应该能发现勾选的checkbox用了isEdit字段，而我数据里并没有这个字段，但是要的就是这个效果：没有这个字段自然默认不勾选，后续勾选了这个字段就为ue了。



## 三、开始编辑

到这里默认的数据展示已经ok了，下一步就需要编辑了，我在表格上面放了一些按钮：

```html
<Row>
  <Col v-if="!isLimitListEdit" span="4" offset="14">
    <Button type="primary" @click="handleEditLimitList">修改选中</Button>
  </Col>
  <Col span="4" v-else offset="14">
    <Button @click="handleEditLimitOk" type="success" style="margin-right: 20px">确定</Button>
    <Button @click="handleEditLimitCancel" type="warning" style="margin-right: 20px">取消</Button>
  </Col>
  <Col span="2">
    <Button @click="handleDeleteLimitList">删除选中</Button>
  </Col>
</RoW>
```

其中，`isLimitListEdit`用来控制编辑时按钮显示的切换

```json
isLimitListEdit: false
```



## 四、修改选中

修改选中按钮点击事件

```javascript
handleEditLimitList () {
  // 编辑限制参数列表
  const isAllUncheck = this.limitList.every(copyItem => !copyTtem.isEdit);
  if (isAllUncheck) {
    this.$toast({
      title: '请至少选择一项',
      type: 'warning'
    })
    return;
  }
  this.isLimitListEdit = true;
  this.copyLimitList = this.copyLimitList.map((copyItem, index) => {
    copyItem.isEdit = this.limitList[index].isEdit
    return copyItem
  })
}

```

这时选中的几行就会切换成可编辑状态了。



## 五、完成编辑

编辑完成点击确定或取消

```javascript
handleEditLimitOk () {
  const editLimitList = this.copyLimitList.filter(copyItem=> copyItem.isEdit)
  // 把 copyLimitList里的isEdit为true的发送后端
  await this.$http.sendEditLimitList(editLimitList)
  //恢复未编辑状态
  this.isLimitListEdit = false
  //完成以后获取最新的列表
  this.getLimitList()
},
handleEditLimitCancel () {
  this.isLimitListEdit = false
  this.limitList = this.limitList.map(limitItem => {
    limitItem.isEdit = false
    return limitItem
  })
  this.copyLimitList = JOSN.parse(JSON.stringify(this.imitList))
}
```



## 六、删除

```javascript
handleDeleteLimitList () {
 const isAllUncheck = this.limitList.every(copyItem => !copyTtem.isEdit);
  if (isAllUncheck) {
    this.$toast({
      title: '请至少选择一项',
      type: 'warning'
    })
    return;
  }
  this.$Modal.confirm({
    title: '提示',
    content: '是否确认删除选择的项',
    onOk: () => {
      const deleteLimitList = this.limitList.filter(copyItem => copyItem.isEdit)
      
      // 把limitList里的isEdit为true的发送后端
      await this.$http.sendDeleteLimitlist(deleteLimitList)
      this.isLimitListEdit = false
      this. getLimitList()
    }
  })
}
```