## v-model笔记

v-model只是个单纯的语法糖，默认调用组件的value属性和input事件

```html
<div id="app">
     <input type="text" :value="message" @input="message = $event.target.value">
        {{message}}
</div>
    <script>
        var vm = new Vue({
            el: '#app',
            data: {
                message: ''
            }
        })
    </script>
```



自定义组件的v-model

```vue
<template>
  <div>
    <input
      v-bind:value="myvalue"
      v-on:input="$emit('hahainput', $event.target.value)"
    />
  </div>
</template>

<script>
export default {
  model: {
    prop: "myvalue",
    event: "hahainput"
  },
  props: {
    myvalue: String
  }
};
</script>
```

```vue
<modelTest v-model="lovingVue"></modelTest>
<modelTest :myvalue="lovingVue" @hahainput="lovingVue = $event.target.value"></modelTest>
<input type="text" :value="lovingVue" @input="lovingVue = $event.target.value">
```

