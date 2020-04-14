<template>
  <div class="yq-tree-cell">
    <div v-on:click="clickCell">
      <div v-if="leafStyle === 'number'" class="yq-tree-cell-number">{{leafNubmer}}</div>
      <div class="yq-tree-cell-item">
        <span v-bind:style="{ 'margin-left':leafOffset*level +'px'}" class="yq-tree-s-flag">
          <Icon v-if="isSelected" type="ios-checkmark-circle" />
          <Icon v-else type="ios-checkmark-circle-outline" />
        </span>
        <span class="yq-tree-s-title">{{data.title}}</span>
        <span v-if="data.loading">
          <Spin></Spin>
        </span>
        <span v-else-if="data.hasChild" class="yq-tree-s-arrow" v-on:click.stop="expandCell">
          <Icon v-if="data.expand" type="ios-arrow-down" />
          <Icon v-else type="ios-arrow-forward" />
        </span>
      </div>
    </div>
    <YQTreeLeaf
      v-if="shouldShowLeaves"
      v-for="(item, i) in data.children"
      :data="item"
      :id-key="idKey"
      :key="i"
      :level="level+1"
      :load-data="loadData"
      :leaf-style="leafStyle"
      :leaf-info="leafInfo"
    ></YQTreeLeaf>
  </div>
</template>

<script>
import "iview/dist/styles/iview.css";
import { Icon, Spin } from "iview";
export default {
  name: "YQTreeLeaf",
  components: {
    Icon,
    Spin
  },
  props: {
    data: {
      typle: Object,
      required: true
    },
    leafStyle: {
      typle: String,
      default: "step",
      validator: function(value) {
        return ["step", "number"].indexOf(value) !== -1;
      }
    },
    offset: {
      type: Number,
      default: 30
    },
    level: {
      type: Number,
      default: 0
    },
    idKey: {
      type: String,
      default: "id"
    },
    loadData: {
      typle: Function
    },
    leafInfo: {
      typle: Function
    }
  },
  data: function() {
    return {
      leafOffset: 0
    };
  },
  created() {
    if (this.leafStyle === "number") {
      this.leafOffset = 0
    } else {
      this.leafOffset = this.offset
    }
  },
  watch: {},
  computed: {
    isSelected() {
      return "selected" in this.data && this.data.selected;
    },
    shouldShowLeaves() {
      return this.data.expand && this.data.hasChild;
    },
    leafNubmer() {
      let info = this.leafInfo(this.data.yq_tree_index)
      if(info) {
        return info.position.map(element => {
          return (element + 1) + ""
        }).join("-")
      } else {
        return ""
      }
    }
  },
  methods: {
    expandCell() {
      if (this.data.hasChild) {
        if (this.data.children && this.data.children.length) {
          this.dispatch("on-expanded", this.data);
        } else {
          if (this.loadData) {
            this.loadLeaves();
          } else {
            console.error("数据错误，请提供loadData方法或修正hasChild属性");
          }
        }
      } else {
        console.error("没有子节点");
      }
    },
    clickCell() {
      if (this.data.hasChild) {
        if (this.data.children && this.data.children.length > 0) {
          this.dispatch("on-selected", this.data);
        } else {
          if (this.loadData) {
            this.loadLeaves();
          } else {
            console.error("数据错误，请提供loadData方法或修正hasChild属性");
          }
        }
      } else {
        this.dispatch("on-selected", this.data);
      }
    },
    loadLeaves() {
      if (this.data.loading) {
        return;
      }
      this.$set(this.data, "loading", true);
      if (this.loadData) {
        this.loadData(this.data, children => {
          this.$set(this.data, "loading", false);
          this.$set(this.data, "children", children);
          this.dispatch("children-changed", this.data);
        });
      } else {
        console.error("请提供loadData方法或修正hasChild属性");
      }
    },
    dispatch(event, params) {
      let target = "YQTreeSelector";
      let parent = this.$parent;
      let name = parent.$options.name;

      while (parent && name !== target) {
        parent = parent.$parent;
        name = parent.$options.name;
      }
      if (parent) {
        parent.$emit.apply(parent, [event].concat(params));
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.yq-tree-cell {
  list-style-type: none;
  padding: 0;
}
.yq-tree-cell-number {
  /* display: inline-block; */
  display: flex;
  padding: 0% 12px;
}
.yq-tree-cell-item {
  /* display: inline-block; */
  height: 44px;
  display: flex;
  /* background-color: bisque; */
  border-bottom: 1px silver solid;
  justify-content: space-between;
  align-items: center;
  padding: 0% 12px;
  /* flex-direction: row; */
}

.yq-tree-s-title {
  margin-left: 8px;
  display: flex;
  flex-grow: 1;
  text-align: left;
}
</style>
