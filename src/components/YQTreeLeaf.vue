<template>
  <div>
    <ul>    
        <li>
            <span v-bind:style="{ 'margin-left':offset*level +'px'}" class="yq-tree-s-flag">
            <img v-if="data.selected" class="yq-tree-s-flag-img"  src="../assets/yq-tree-circle-selected.png">
            <img v-else  class="yq-tree-s-flag-img" src="../assets/yq-tree-circle-normal.png">
            </span>
            <span class="yq-tree-s-title">{{data.title}}</span>
            <span class="yq-tree-s-arrow" v-on:click="expandCell">
                <template v-if="data.hasChild">
                <img v-if="data.expand" class="yq-tree-s-arrow-img"  src="../assets/yq-tree-arrow-down.png">
                <img v-else class="yq-tree-s-arrow-img" src="../assets/yq-tree-arrow-right.png">
                </template>
            </span>
        </li>
    </ul>
    <YQTreeLeaf v-if="data.expand && data.hasChild" v-for="(item, i) in data.children" :data="item" :id-key="idKey" :key="i" :level="level+1"></YQTreeLeaf>
  </div>
</template>

<script>
export default {
  name: 'YQTreeLeaf',
  props: {
    data: {
        typle: Object,
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
    level: {
        type: Number,
        default: 0,
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
          treeData: [],
          treeInfo: []
      }
  },
  watch: {
    data() {
      this.treeData = this.data
      this.treeInfo = this.indexTreeData()
    }
  },
  computed: {
    
  },
  methods: {
    indexTreeData() {
      let index = 0

    },
    dataToRows(value, level, parent) {
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
    expandCell() {
      if (this.data.hasChild) {
        if ( this.data.children && this.data.children.length) {
          if (this.data.expand) {
            this.data.expand = false
          } else {
            this.data.expand = true
          }
        } else {
          console.error("当前cell没有提供子节点数据")
        }
      } else {
        console.error("没有子节点")
      }
    },
    clickCell() {
      if (this.loadData) {
        //TODO:如果没有数据先去下载
      } else {
        if (!this.data.hasChild) {
          if (this.data.isSelected) {
            this.data.isSelected = false
          } else {
            this.data.isSelected = true
          }
        } else {
          
        }
      }
    }, 
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
  border-bottom: 1px silver solid;
  justify-content: space-between;
  align-items: center;
  padding: 0% 12px;
  /* flex-direction: row; */
}

.flip-list-enter-active, .flip-list-leave-active {
  transition: opacity .3s ease;
}
.yq-tree-s-flag-img {
  width: 20px;
  height: 20px;
}
.yq-tree-s-arrow-img {
  width: 30px;
  height: 30px;
}
.yq-tree-s-flag {
  display: flex;
  max-width: 20px;
  max-height: 20px;
  
}
.yq-tree-s-title {
  margin-left: 8px;
  display: flex;
  flex-grow: 1;
  text-align: left;
}
.yq-tree-s-arrow {
  margin-left: 8px;
  display: flex;
  max-width: 30px;
  max-height: 30px;
}

</style>
