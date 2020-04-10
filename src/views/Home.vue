<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <button v-on:click="click"/>
    <YQTreeSelector ref="tree" :data="tree" id-key="id" :load-data="loadData" leaf-style="number" :on-select-changed="onSelectChanged"/>
    
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import YQTreeSelector from '@/components/YQTreeSelector.vue'

export default {
  name: 'Home',
  components: {
    HelloWorld,
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
      }, 1000 * 5);
    },
    onSelectChanged() {
      console.log(this.$refs["tree"].getSelectedNodes())
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
