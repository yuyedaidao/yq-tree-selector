<template>
  <div class="yq-tree-selector">
    <YQTreeLeaf
      v-for="(item, i) in treeData"
      :data="item"
      id-key="id"
      :key="i"
      :level="0"
      :load-data="loadData"
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
    }
  },
  data: function() {
    return {
      treeData: this.data,
      leavesInfo: []
    };
  },
  watch: {
    data: {
      deep: false,
      handler() {
        this.treeData = this.data;
        this.flatTree = this.indexTreeData();
        console.log("didi");
      }
    }
  },
  computed: {},
  methods: {
    indexTreeData() {
      let index = 0;
      const items = [];
      function flattenLeaves(leaf, i, level, parent) {
        leaf.yq_tree_index = index++;
        let node = { leaf: leaf, index: leaf.yq_tree_index, level: level };
        items[leaf.yq_tree_index] = node;
        if (parent) {
          console.log("parent", parent);
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
      this.treeData.forEach((element, i) => {
        flattenLeaves(element, i, 0);
      });
      items.forEach(element => {
        console.log(element.index, element.expanded, element.descendant);
      });
      return items;
    },

    handleSelect(value) {
      let leafInfo = this.flatTree[value.yq_tree_index];
      if (!leafInfo) {
        return;
      }
      let leaf = leafInfo.leaf;
      if (leaf.selected) {
        if (leaf.hasChild) {
          //可以
          if (leafInfo.expanded) {
            //选中所有孩子
            for (
              let i = leafInfo.index;
              i < leafInfo.index + leafInfo.descendant;
              i++
            ) {
              let item = this.flatTree[i].leaf;
              this.$set(item, "selected", false);
            }
          } else {
            let err = "当前节点未加载完数据";
            console.error(err);
            this.$emit("on-error", err);
          }
        } else {
          //选中然后往上
          this.$set(leaf, "selected", false);
          //TODO:往上
        }
      } else {
        //先去校验数据是否都加载
        if (leaf.hasChild) {
          //可以
          if (leafInfo.expanded) {
            //选中所有孩子
            for (
              let i = leafInfo.index;
              i < leafInfo.index + leafInfo.descendant;
              i++
            ) {
              let item = this.flatTree[i].leaf;
              this.$set(item, "selected", true);
            }
          } else {
            let err = "当前节点未加载完数据";
            console.error(err);
            this.$emit("on-error", err);
          }
        } else {
          //选中然后往上
          this.$set(leaf, "selected", true);
          //TODO:往上
        }
      }
      if (leaf.hasChild) {
      } else {
        if (leaf.parent) {
        }
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
    }
  },

  created() {
    this.treeData = this.data;
    this.flatTree = this.indexTreeData();
  },

  mounted() {
    this.$on("on-selected", this.handleSelect);
    this.$on("on-expanded", this.handleExpand);
    this.$on("children-changed", function(item) {
      this.treeData = this.data;
      this.flatTree = this.indexTreeData();
    });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
