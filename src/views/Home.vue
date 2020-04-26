<!--
 * @Author: your name
 * @Date: 2020-04-20 15:39:49
 * @LastEditTime: 2020-04-26 10:21:15
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \yq-tree-selector\src\views\Home.vue
 -->
<template>
  <div>
    <YQTreeSelector ref="tree" :data="tree" id-key="id" :load-data="loadData" :on-select-changed="onSelectChanged" @on-error='handleError'/>
  </div>
</template>

<script>
import YQTreeSelector from '@/index'

export default {
  name: 'Home',
  components: {
    YQTreeSelector
  },
  data() {
    return {
      tree: [
        {
          id: 0,
          title: "a",
          hasChild: false
        }]
    }
  },
  methods: {
    click() {      
      this.tree[0].title = Math.random() + ""
      console.log(this.tree)
    },
    loadData(item, callback) {
      console.log(item)
      setTimeout(() => {
        callback([{
              id: 2853,
              title: "f",
              hasChild: false,
              expand: true,
            },{
              id: 2813,
              title: "f",
              hasChild: false,
              expand: true,
            }])
      }, 1000 * 2.5);
    },
    onSelectChanged() {
      console.log(this.$refs["tree"].getSelectedNodes())
    },
    handleError(err) {
      console.log('err+',err)
    }
  },
  mounted() {
    let array = []
    for (var i = 1; i < 4; i++){
      let item = {
        id: i,
        title: "a",
        hasChild: true,
        expand: true,
        selected: false
      }
      let child = {
        id: i*100,
        title: "b",
        hasChild: true,
        expand:true,
        children: [{
          id: i*103+i,
          title: "d",
          hasChild: true,
          expand: true,
          children: [
            {
              id: i+i * 283,
              title: "e",
              hasChild: false,
              expand: true,
            }
          ]
        }]
      }
      let child1 = {
        id: i*201,
        title: "c",
        hasChild: true,
        selected: false,
        expand: true
      }
      item.children = [child, child1]
      array.push(item)
    }
    this.tree = array;
  }
}
</script>
