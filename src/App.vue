<template>
  <div id="app">
    <div class="todo-container">
      <div class="todo-wrap">
        <MyHeader @addTodo="addTodo"></MyHeader>

        <List :todoList="todoList"></List>

        <MyFooter :todoList="todoList" @checkAllTodo="checkAllTodo" @clearAllTodo="clearAllTodo"></MyFooter>
      </div>
    </div>
  </div>
</template>

<script>
import pubsub from 'pubsub-js'
import List from './components/List.vue'
import MyFooter from './components/MyFooter.vue'
import MyHeader from './components/MyHeader.vue'

export default {
  components: { MyHeader, List, MyFooter },
  name: 'App',
  data() {
    return {
      todoList: JSON.parse(localStorage.getItem('todoList')) || []
    }
  },
  methods: {
    // 添加一个todo 的回调   (增)
    addTodo(todo) {
      console.log(todo)
      this.todoList.unshift(todo)
    },
    // 勾选或取消勾选一个todo (查&改)
    checkTodo(id) {
      this.todoList.forEach(todo => {
        if (todo.id === id) {
          todo.done = !todo.done
        }
      })
    },
    // 删除一个todo    (删)
    deleteTodo(_, id) {
      this.todoList = this.todoList.filter(todo => todo.id !== id)
    },
    // 更新一个todo   (查&改)
    updateTodo(_, data) {
      let { id, title } = data
      this.todoList.forEach(todo => {
        if (todo.id === id) {
          todo.title = title
        }
      })
    },
    // 全选或取消全选  (查&改)
    checkAllTodo(done) {
      this.todoList.forEach(todo => {
        todo.done = done
      })
    },
    // 删除已选的item
    clearAllTodo() {
      this.todoList = this.todoList.filter(todo => todo.done === false)
    }
  },
  // 监听 todoList ---本地存储
  watch: {
    todoList: {
      handler(value) {
        localStorage.setItem('todoList', JSON.stringify(value))
      },
      deep: true
    }
  },
  // item组件功能的 (消息订阅)
  mounted() {
    // 回调函数可以直接写在里面, 但要写出箭头函数形式
    this.$bus.$on('checkTodo', this.checkTodo)
    this.pubId_delete = pubsub.subscribe('deleteTodo', this.deleteTodo)
    this.pubId_update = pubsub.subscribe('updateTodo', this.updateTodo)
  },
  // 消息订阅的 取消订阅
  beforeDestroy() {
    this.$bus.$off('checkTodo')
    pubsub.unsubsribe(this.pubId_delete)
    pubsub.unsubsribe(this.pubId_update)
  }
}
</script>

<style>
/*base*/
body {
  background: #fff;
}

.btn {
  display: inline-block;
  padding: 4px 12px;
  margin-bottom: 0;
  font-size: 14px;
  line-height: 20px;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
  box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.2), 0 1px 2px rgba(0, 0, 0, 0.05);
  border-radius: 4px;
}

.btn-danger {
  color: #fff;
  background-color: #da4f49;
  border: 1px solid #bd362f;
}

.btn-edit {
  color: #fff;
  background-color: lightgreen;
  border: 1px solid green;
  margin-right: 5px;
}

.btn-danger:hover {
  color: #fff;
  background-color: #bd362f;
}

.btn-edit:hover {
  color: #fff;
  background-color: green;
}

.btn:focus {
  outline: none;
}

.todo-container {
  width: 600px;
  margin: 0 auto;
}
.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
</style>
