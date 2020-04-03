<template>
  <div class="yq-tree-selector">
    <ul>
      <li v-for="item in dataArray" :key="item[idKey]">
        <span v-bind:style="{ 'margin-left':offset*item.yq_tree_s_level +'px'}"><img class="yq-tree-s-flag" src="../assets/yq-tree-circle-normal.png"></span><span class="yq-tree-s-title">{{item.title}}</span>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'YQTreeSelector',
  props: {
    data: {
        typle: Array,
        required: true,
    },
    leafStyle: {
        typle: String,
        default: "step",
        validator: function(value) {
            return ["step", "number"].indexOf(value) !== -1
        }
    },
    offset: {
      type: Number,
      default: 30,
    },
    idKey: {
      type: String,
      default: "id"
    },
    loadData: {
        typle: Function,
    }
  },
  data: function() {
      return {
          async: this.loadData !== null,
      }
  },
  computed: {
    dataArray() {
      let array = []
      this.dataToRows(this.data, 0).forEach( element => {
        array.push(element)
      })
      return array
    }
  },
  methods: {
    dataToRows(value, level) {
      let array =  []
      value.forEach(element => {
        element.yq_tree_s_level = level
        array.push(element)
        if (element.expand && element.hasChild && element.children) {
          this.dataToRows(element.children, level + 1).forEach( element => {
            array.push(element)
          })
        }
      })
      return array
    },
    
    selectChange: (item) => {

    }
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
ul {
  list-style-type: none;
  padding: 0;
}
li {
  /* display: inline-block; */
  height: 44px;
  display: flex;
  background-color: bisque;
  border-bottom: 1px cadetblue solid;
  /* justify-content: space-around; */
  align-items: center;
  /* flex-direction: row; */
}
.yq-tree-s-flag {
  width: 20px;
  height: 20px;
}
.yq-tree-s-title {
  margin-left: 8px;
}

</style>
