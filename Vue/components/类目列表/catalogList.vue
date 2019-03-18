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
          >
            {{ element[labelName] }}
          </li>
        </transition-group>
      </draggable>
      <div v-show="editing" class="draggable-edit-box list-group">
        <div
          v-for="(element, index) in list"
          :key="element[valueName]"
          class="list-group-item"
        >
          <div>
            <el-input v-if="element.editing" v-model="inputValue" size="mini" />
            <p v-else>{{ element[labelName] }}</p>
            &nbsp;&nbsp;
            <div class="mini-btn-box">
              <el-button v-if="!element.editing" type="primary" size="mini" icon="el-icon-edit" circle @click.stop="toEdit(index)" />
              <el-button v-else type="success" size="mini" icon="el-icon-check" circle @click.stop="onSave({ id: element[valueName], label: element[labelName], index })" />
              <el-button type="danger" size="mini" icon="el-icon-delete" circle @click.stop="onDelete({ id: element[valueName], label: element[labelName] })" />
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="list-group-item">
      <el-button type="info" size="mini" @click.stop="$emit('onAdd')">添加类目</el-button>
      <el-button type="success" size="mini" plain @click.stop="toggleEditStatus">{{ !editing ? '编辑' : '取消' }}</el-button>
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
    },
    needTips: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      list: [],
      editing: false,
      drag: false,
      inputValue: ''
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
      this.editing = false
      this.inputValue = ''
      this.resetListEditStatus()
    },
    onEnd() {
      this.drag = false
      this.$emit('onMoveEnd', this.list)
    },
    onSave({
      id,
      label,
      index
    }) {
      const cb = () => {
        this.$emit('onSave', {
          id,
          name: this.inputValue
        })
      }
      if (this.needTips) {
        this.$confirm(`将会保存此名称(${this.inputValue}), 是否继续?`, '提示', {
          type: 'warning'
        }).then(() => {
          cb()
        }).catch(() => {
          this.setStatus({
            index,
            editing: false
          })
        })
      } else {
        cb()
      }
    },
    onDelete({
      id,
      label
    }) {
      this.resetListEditStatus()
      const cb = () => {
        this.$emit('onDelete', {
          id
        })
      }
      if (this.needTips) {
        this.$confirm(`将会删除此名称(${label}), 是否继续?`, '提示', {
          type: 'warning'
        }).then(() => {
          cb()
        }).catch(() => {})
      } else {
        cb()
      }
    },
    toEdit(index) {
      this.inputValue = this.list[index][this.labelName]
      this.resetListEditStatus()
      this.setStatus({
        index,
        editing: true
      })
    },
    // 重置状态
    resetListEditStatus() {
      const arr = [...this.list]
      for (let i = 0; i < arr.length; i++) {
        arr[i].editing = false
      }
      this.list = arr
    },
    setStatus({
      index,
      editing = true
    }) {
      this.$set(this.list, index, {
        ...this.list[index],
        ...{
          editing
        }
      })
    },
    toggleEditStatus() {
      this.editing = !this.editing
    }
  }
}
</script>

<style lang="scss" scoped>
.catalog-list-components {
  position: relative;
  .draggable-edit-box {
    background-color: #FFF;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    .list-group-item {
      cursor: auto;
      > div {
        display: flex;
        justify-content: space-between;
        width: 100%;
        > .el-input {
          flex: 1;
        }
        > p {
          line-height: 30px;
          flex: 1;
        }
        > .mini-btn-box {
        }
      }
    }
  }
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
