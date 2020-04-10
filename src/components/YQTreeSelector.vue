<template>
  <div class="yq-tree-selector">
    <YQTreeLeaf
      v-for="(item, i) in treeData"
      :data="item"
      id-key="id"
      :key="i"
      :level="0"
      :load-data="loadData"
      :leaf-style="leafStyle"
      :leaf-info="leafInfo"
    ></YQTreeLeaf>
  </div>
</template>

<script>
import YQTreeLeaf from "./YQTreeLeaf";

export default {
  name: "YQTreeSelector",
  components: {
    YQTreeLeaf
  },
  props: {
    data: {
      typle: Array,
      required: true
    },
    leafStyle: {
      typle: String,
      default: "step",
      validator: function(value) {
        return ["step", "number"].indexOf(value) !== -1;
      }
    },
    level: {
      type: Number,
      default: 0
    },
    offset: {
      type: Number,
      default: 30
    },
    idKey: {
      type: String,
      default: "id"
    },
    loadData: {
      typle: Function
    },
    onSelectChanged: {
      typle: Function
    }
  },
  data: function() {
    return {
      treeData: [],
      leavesInfo: []
    };
  },
  watch: {
    data: {
      deep: false,
      handler() {
        this.resetData();
      }
    }
  },
  computed: {},
  methods: {
    resetData() {
      this.flatTree = this.indexTreeData(this.data);
      this.treeData = this.data;
    },
    leafInfo(index) {
      return this.flatTree[index];
    },
    indexTreeData(data) {
      let index = 0;
      const items = [];
      function flattenLeaves(leaf, i, level, parent) {
        leaf.yq_tree_index = index++;
        let node = { leaf: leaf, index: leaf.yq_tree_index, level: level };
        items[leaf.yq_tree_index] = node;
        if (parent) {
          node.parent = parent.leaf.yq_tree_index;
          node.position = parent.position.concat([i]);
        } else {
          node.position = [i];
        }
        if (leaf.hasChild) {
          if (leaf.children && leaf.children.length > 0) {
            leaf.children.forEach((element, i) => {
              flattenLeaves(element, i, level + 1, node);
            });
            //记录子节点是否已经全部加载
            node.expanded = leaf.children.every(element => {
              return items[element.yq_tree_index].expanded;
            });
            let descendant = 1;
            leaf.children.forEach(element => {
              descendant = descendant + items[element.yq_tree_index].descendant;
            });
            node.descendant = descendant;
          } else {
            node.expanded = false;
            node.descendant = 1;
          }
        } else {
          node.expanded = true;
          node.descendant = 1;
        }
      }
      data.forEach((element, i) => {
        flattenLeaves(element, i, 0);
      });
      return items;
    },

    handleSelect(value) {
      let leafInfo = this.flatTree[value.yq_tree_index];
      if (!leafInfo) {
        return;
      }
      let leaf = leafInfo.leaf;
      //TODO:优化代码 判断太复杂
      if (leaf.selected) {
        if (leaf.hasChild) {
          //可以
          if (leafInfo.expanded) {
            //选中所有孩子
            this.selectInward(leaf.yq_tree_index, false);
            //祖辈节点检查
            this.selectOutward(leaf.yq_tree_index);
            if (this.onSelectChanged) {
              this.onSelectChanged(leaf);
            }
          } else {
            let err = "当前节点未加载完数据";
            console.error(err);
            this.$emit("on-error", err);
          }
        } else {
          //选中然后往上
          this.$set(leaf, "selected", false);
          this.selectOutward(leaf.yq_tree_index);
          if (this.onSelectChanged) {
            this.onSelectChanged(leaf);
          }
        }
      } else {
        //先去校验数据是否都加载
        if (leaf.hasChild) {
          //可以
          if (leafInfo.expanded) {
            //选中所有孩子
            this.selectInward(leaf.yq_tree_index, true);
            //祖辈节点检查
            this.selectOutward(leaf.yq_tree_index);
            if (this.onSelectChanged) {
              this.onSelectChanged(leaf);
            }
          } else {
            let err = "当前节点未加载完数据";
            console.error(err);
            this.$emit("on-error", err);
          }
        } else {
          this.$set(leaf, "selected", true);
          this.selectOutward(leaf.yq_tree_index);
          if (this.onSelectChanged) {
            this.onSelectChanged(leaf);
          }
        }
      }
    },
    selectInward(index, selected) {
      const count = this.flatTree[index].descendant;
      for (let i = index; i < index + count; i++) {
        let item = this.flatTree[i].leaf;
        this.$set(item, "selected", selected);
      }
    },
    selectOutward(index) {
      let parentIndex = this.flatTree[index].parent;
      while (parentIndex > -1) {
        let parentInfo = this.flatTree[parentIndex];
        let parent = parentInfo.leaf;
        let allSelected = true;
        for (let i = 0; i < parent.children.length; i++) {
          if (parent.children[i].selected) {
            continue;
          } else {
            allSelected = false;
            break;
          }
        }
        if (allSelected) {
          this.$set(parent, "selected", true);
        } else {
          //MARK:这里其实可以优化一步，如果当前节点已经为选中了，那就没必要再去监测上一层了
          this.$set(parent, "selected", false);
        }
        parentIndex = parentInfo.parent;
      }
    },
    handleExpand(value) {
      let leafInfo = this.flatTree[value.yq_tree_index];
      if (!leafInfo) {
        return;
      }
      let leaf = leafInfo.leaf;
      if (leaf.expand) {
        this.$set(leaf, "expand", false);
      } else {
        this.$set(leaf, "expand", true);
      }
    },
    getSelectedNodes() {
      return this.flatTree.filter(element => {
        let leaf = element.leaf;
        return leaf.selected && !leaf.hasChild;
      }).map(element => element.leaf);
    }
  },
  created() {
    this.resetData();
  },
  mounted() {
    this.$on("on-selected", this.handleSelect);
    this.$on("on-expanded", this.handleExpand);
    this.$on("children-changed", function(item) {
      this.resetData();
    });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
