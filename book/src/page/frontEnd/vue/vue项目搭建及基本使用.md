## vue 项目搭建及基本使用

### 创建项目
vue create hello-world 

#### 1.选择基本配置

scss/less

ESLint + Prettier

TypeScript

#### 2.安装插件

element/view...   ui组件

vuex  状态管理

axios   http请求

qs  序列化/反序列化 链接参数

...

#### 3.编写通用工具

request.js  公共请求封装，拦截器等

tool.js   通用方法

common.css  通用基础样式，

variable.css 引入全局css变量

### 编写组件
signField,login...

### 配置编译
vue.config.js

## 常用特性介绍

响应原理  Object.defineProperty setter/getter

#### 指令
```
v-bind/: //绑定属性

v-on/@  //监听事件

v-if

v-for 

...
```

#### 绑定文本
```
<span v-once>{{变量|js表达式}}</span>
```
#### 绑定富文本
```
<span v-html="变量|富文本值"></span>
```
#### 计算属性/侦听器
```
computed: {
    provinceName() {
      let name = "";
      this.provinceList.forEach(province => {
        if (province.id == this.provinceId) {
          name = province.name;
        }
      });
      return name;
    }
}
watch: {
    fieldJson() {
      this.fieldJson.forEach(item => {
        this.$set(this.answer, item.field, this.dataJson[item.field] || "");
        if (item.com == 2) {
          this.$set(this.verify, item.field, {});
          this.$set(this.verify[item.field], 'right', false);
          this.$set(this.verify[item.field], 'code', '');
        }
      });
      this.answerLoad = true;
    }
  }
```

#### 绑定class/绑定style
```
<span :class="true?'class1':''"></span>
<span :class="[class1,class2]"></span>

<span :style="{color: 变量}"></span>
```
#### v-if/v-show
```
<span v-if="变量|表达式"></span>
<span v-else-if="变量|表达式"></span>
<span v-else="变量|表达式"></span>

<span v-show="变量|表达式"></span>

//v-if会动态插入dom   v-show一直插入dom只是切换css隐藏
```
#### v-for
```
<span v-for="(item,index) in array|obj" :key="item.id|index">
  {{item.name}}
</span>

//对数组某一项改值 arr.splice(index,0,value); ~~arr[index] = value;~~
```
#### @事件
```
<span @click="show(...参数,$event)"></span>

methods:{
  show(...参数,event){
    //event 原生事件对象
    //do something
  }
}
```

#### v-model输入绑定
```
1：v-model
<input v-model="name" placeholder="请输入">
<span>输入的值: {{ name }}</span>

2：拆解
<input :value="name" @input="changeValue" placeholder="请输入">
<span>输入的值: {{ name }}</span>
methods:{
  changeValue(value){
    this.value = value;
  }
}

//v-model = :value + @...事件|input/selected

```

## 组件开发

![组件树](https://cn.vuejs.org/images/components.png)
```
注册：
Vue.component('nos-code', {
  data: function () {
    return {
      code: '1234'
    }
  },
  template: '<span>code</span>'
});

使用：
<div>
  <nos-code></nos-code>
</div>
```
#### 单文件组件
```
<template>
  <div class="field-contain">
    <div
      class="field-label label-purple"
      :class="textClass"
      v-html="title"
    ></div>
    <el-input
      :disabled="!edit"
      :placeholder="placeholder"
      :value="value"
      @input="input"
      clearable
    ></el-input>
    <slot></slot>
    <slot name="tip"></slot>
  </div>
</template>

<script>
export default {
  name: "signInput",
  props: {
    title: {
      type: String,
      default: "文本输入"
    },
    textClass: {
      type: String,
      default: ""
    },
    type: {
      type: String,
      default: "text"
    },
    placeholder: {
      type: String,
      default: "请输入"
    },
    value: {
      type: String,
      default: ""
    },
    edit: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {};
  },
  methods: {
    input(value) {
      this.$emit("field-change", value);
      this.$root //跟组件实例
      this.$parent //父组件实例
    }
  }
};
</script>

<style scoped lang="scss"></style>

```
#### 使用
```
<template>
  <div class="container sign-form">
    <slot></slot>
    <div v-if="answerLoad">
      <div v-for="(field, index) in fieldJson" :key="index">    
        <sign-input
          ref="usernameInput"
          v-else-if="field.com == 5"
          :edit="isEdit"
          :title="field.title"
          :value="answer[field.field]"  //v-model="answer[field.field]"
          @field-change="changeValue($event, field.field)"
        >
            <span>{{remark}}</span>
            <span v-slot:tip>tipinfo</span>
        </sign-input>
      </div>
    </div>
  </div>
</template>

<script>
import signInput from "@/components/signField/text-input.vue";
export default {
  name: "signForm",
  components: {
    signInput
  },
  props: {
    fieldJson: {
      type: Array,
      default: function () {
        return [];
      }
    },
    dataJson: {
      type: Object,
      default: function () {
        return {};
      }
    },
    isEdit: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      remark:'哇哈哈',
      answer: {},
      verify: {},
      answerLoad: false
    };
  },
  watch: {
    fieldJson() {
      this.fieldJson.forEach(item => {
        this.$set(this.answer, item.field, this.dataJson[item.field] || "");
        if (item.com == 2) {
          this.$set(this.verify, item.field, {});
          this.$set(this.verify[item.field], 'right', false);
          this.$set(this.verify[item.field], 'code', '');
        }
      });
      this.answerLoad = true;
    }
  },
  methods: {
    submit() {      
      this.$refs.usernameInput //子组件实例
    },
    changeValue(src, field) {
      this.answer[field] = src;
    }
  }
};
</script>

<style lang="scss">
.sign-form {
  max-width: 800px;
  margin: 0 auto;
  padding: 30px 0;
}
</style>
```