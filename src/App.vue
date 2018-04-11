<template>
    <div id="app">
      <h2>Vue-todolist</h2>
      <div class="container">
        <input 
          class="inp"
          type="text" 
          placeholder="what's your task?"
          autofocus="true" 
          @keyup.enter="addtodoList(todoText)"
          v-model="todoText" />
        <ul class="list">
          <li v-for="item in todoList">
            <input 
            type="checkbox"
            v-on:click="handleClick(item)"
            v-bind:checked="item.isChecked">
            <span v-bind:class="{active: item.isChecked}" >{{ item.content }}</span>
            <button v-on:click="btnClick(item)">删除任务</button>
          </li>
        </ul>
        <div class="control">
          <input 
            v-model="isAllChecked"
            @click="handleSelectAll"
            v-bind:checked="checked"
            type="checkbox">
          <span>{{ text }}</span>
          <button class="clear" @click="clearDone">清除已完成</button>
        </div>
      </div>
      <div class="footer"><a href="https://github.com/laoxiehhh/vue-todolist">github地址</a></div>
    </div>
</template>

<script>
export default {
  name: 'app',
  data() {
    return {
      todoText: '',//输入的字符串
      todoList: [],//存储任务的数组
      isAllChecked: false,
      checked: false//添加任务时，将全选已完成的checkbox取消
    }
  },
  computed: {
    text() {
      var done = this.todoList.filter((ele) => ele.isChecked).length;
      return `${done || 0} closed / ${this.todoList.length}`;
    }
  },
  created() {
    this.todoList = JSON.parse(window.localStorage.getItem('vue-todo')) || [];
  },
  methods: {
    updateStorage(key, value) {
      window.localStorage.setItem(key, JSON.stringify(value));
    },
    addtodoList(val) {
      this.todoList.push({
        content: val,
        isChecked: false
      });
      this.updateStorage('vue-todo', this.todoList);
      this.todoText = '';
      this.isAllChecked = false;
      this.checked = false;
    },
    handleClick(item) {
      item.isChecked = !item.isChecked;
      this.updateStorage('vue-todo', this.todoList);
    },
    btnClick(item) {
      let index = this.todoList.indexOf(item);
      this.todoList.splice(index, 1);
      this.updateStorage('vue-todo', this.todoList);
    },
    changeTodoState(isChecked, changeAll = false) {
      this.isAllChecked = isChecked;
      if (changeAll) {
        this.todoList.forEach(ele => {
          ele.isChecked = isChecked;
        });
        this.updateStorage('vue-todo', this.todoList);
      }
    },
    handleSelectAll(e) {
      this.changeTodoState(e.target.checked, true);
    },
    clearDone() {
      var key = this.todoList.some(ele => ele.isChecked);
      if (!key) {
        alert('没有已完成的任务！！');
      } else {
        this.todoList = this.todoList.filter(ele => !ele.isChecked);
        this.updateStorage('vue-todo', this.todoList);
      }
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin-top: 60px;
}
h2 {
  text-align: center;
}
.container {
  width: 400px;
  margin: 0 auto;
}
.inp {
  width: 100%;
  height: 30px;
  font-size: 18px;
}
.list {
  width: 100%;
  
}
.list li {
  list-style: none;
  border-bottom: 1px solid #ccc;
  margin-top: 10px;
  padding-bottom: 5px;
}
.list li button, .clear {
  float: right;
  width: 24%;
  height: 20px;
  border: none;
  background-color: #fff;
  border-radius: 4px;
  cursor: pointer;
  line-height: 20px;
}
.list li button:hover, .clear:hover{
  background: #FF6A6A;
  color: #fff; 
}
.active {
  text-decoration: line-through;
}
.control {
  width: 100%;
  margin-top: 10px;
}
.footer a {
  display: block;
  color: #2c3e50;
  text-decoration: none;
  width: 200px;
  margin: 100px auto;
  font-size: 24px;
  cursor: pointer;
  text-align: center;
}
</style>
