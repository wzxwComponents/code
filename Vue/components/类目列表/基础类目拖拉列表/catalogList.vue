<template>
  <div class="catalog-list-components">
    <div class="draggable-box">
      <draggable
        :list="list"
        v-bind="dragOptions"
        class="list-group"
        tag="ul"
        @start="drag = true"
        @end="onEnd"
      >
        <transition-group :name="!drag ? 'flip-list' : null" type="transition">
          <li
            v-for="element in list"
            :key="element[valueName]"
            class="list-group-item"
            @click.stop="$emit('onClick', element)"
          >
            {{ element[labelName] }}
          </li>
        </transition-group>
      </draggable>
    </div>
    <div class="list-group-item">
      <el-button type="info" size="mini" @click.stop="$emit('onAdd')">添加类目</el-button>
      <el-button type="success" size="mini" plain @click.stop="$emit('onEdit')">编辑</el-button>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'
export default {
  components: {
    draggable
  },
  props: {
    valueName: {
      type: String,
      default: 'categoryId'
    },
    labelName: {
      type: String,
      default: 'categoryName'
    }
  },
  data() {
    return {
      list: [],
      drag: false
    }
  },
  computed: {
    dragOptions() {
      return {
        animation: 200,
        group: 'description',
        disabled: false,
        ghostClass: 'ghost'
      }
    }
  },
  methods: {
    init(list) {
      this.list = list
    },
    onEnd() {
      this.drag = false
      this.$emit('onMoveEnd', this.list)
    }
  }
}
</script>

<style lang="scss" scoped>
.catalog-list-components {
  position: relative;
  .flip-list-move {
    transition: transform 0.5s;
  }
  .no-move {
    transition: transform 0s;
  }
  .ghost {
    opacity: 0.5;
    background: #F1F1F1;
  }
  .list-group-item {
    cursor: move;
  }
  .list-group-item i {
    cursor: pointer;
  }
  .list-group {
    display: flex;
    flex-direction: column;
    margin-top: 0;
    padding-left: 0;
    margin-bottom: 0;
    font-size: 12px;
  }
  .list-group-item:first-child {
    border-top-left-radius: .25rem;
    border-top-right-radius: .25rem;
  }
  .list-group-item {
    position: relative;
    margin-bottom: -1px;
    background-color: #fff;
    border: 1px solid rgba(0,0,0,.125);
    min-height: 40px;
    display: flex;
    align-items: center;
    padding: 0 20px;
  }
}
</style>

<style lang="scss">
.catalog-list-components .list-group-item {
  input {
    padding: 0 5px!important;
  }
}
</style>
