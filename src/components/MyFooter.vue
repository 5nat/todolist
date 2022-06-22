<template>
  <div class="todo-footer" v-show="total">
    <label>
      <input type="checkbox" :checked="isAll" @change="checkAll" />
      <!-- <input type="checkbox" v-model="isAll" /> -->
    </label>
    <span>
      <span>已完成{{ doneTodo }}</span> / 全部{{ total }}
    </span>
    <button class="btn btn-danger" @click="clearAll">清除已完成任务</button>
  </div>
</template>

<script>
export default {
  name: 'MyFooter',
  props: ['todoList'],
  computed: {
    total() {
      return this.todoList.length
    },
    // 可以换成 reduce 数据统计
    doneTodo() {
      return this.todoList.filter(todo => todo.done === true).length
    },
    isAll() {
      return this.doneTodo === this.total && this.total > 0
    }
  },
  methods: {
    // 全选按钮的回调
    checkAll(e) {
      this.$emit('checkAllTodo', e.target.checked)
    },
    // 删除已选按钮的回调
    clearAll() {
      if (confirm('确定清除吗？')) {
        this.$emit('clearAllTodo')
      }
    }
  }
}
</script>

<style scoped>
/*footer*/
.todo-footer {
  height: 40px;
  line-height: 40px;
  padding-left: 6px;
  margin-top: 5px;
}

.todo-footer label {
  display: inline-block;
  margin-right: 20px;
  cursor: pointer;
}

.todo-footer label input {
  position: relative;
  top: -1px;
  vertical-align: middle;
  margin-right: 5px;
}

.todo-footer button {
  float: right;
  margin-top: 5px;
}
</style>
