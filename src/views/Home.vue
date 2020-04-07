<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <button v-on:click="click"/>
    <YQTreeSelector :data="tree" id-key="id"/>
    
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
    }
  },
  mounted() {
    let array = []
    for (var i = 1; i < 4; i++){
      let item = {
        id: i,
        title: "a",
        hasChild: true,
        expand: true
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
              isSelected: false

            }
          ]
        }]
      }
      let child1 = {
        id: i*201,
        title: "c",
        hasChild: false,
        isSelected: false
      }
      item.children = [child, child1]
      array.push(item)
    }
    this.tree = array;
  }
}
</script>
