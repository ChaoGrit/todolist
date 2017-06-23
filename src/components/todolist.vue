<template>
  <div id="todo-list">
    <section>
      <input class="todo-input" @keyup.enter="addTodo" v-model="whattodo" autofocus placeholder="what you want to do?">
      <span class="btn-group">
        <a v-for="item in btnGroup" :class="{active: select == item.status}" class="btn" href="javascript:;" @click="changeStatus(item)">{{item.title}}</a>
      </span>
      <a @click="removeDone" class="btn-delete" href="javascript:;">删除已完成</a>
    </section>
    <section>
      <div><strong>{{remaining}}</strong><span class="unit">{{ remaining | pluralize }}</span>left</div>
    </section>
    <section>
      <ul class="todo-list">
        <li v-for="item in filterList" :class="{editing:item == editItem}">
        <div class="view">
          <input type="checkbox" v-model="item.hasDone">
          <label :class={finished:item.hasDone} @dblclick="editTodo(item)">{{item.text}}</label>
          <a href="javascript:;" @click="removeItem(item)">删除</a>
        </div>
        <input class="edit" type="text" v-model="item.text" v-autofocus @blur="doneEdit(item)" @keyup.enter="doneEdit(item)" @keyup.esc="cancelEdit(item)"/>
        </li>
      </ul>
    </section>
  </div>
</template>

<script>
  var ListStorage = 'vue-todo-list';
  //Html5 本地缓存
  var todoStorage = {
      fetch: function() {
        var todolist = JSON.parse(localStorage.getItem(ListStorage) || '[]');
        todolist.forEach(function(item, index) {
          item.id = index;
        });
        todoStorage.totalId = todolist.length;
        return todolist;
      },
      save: function(todolist) {
        localStorage.setItem(ListStorage, JSON.stringify(todolist));
      }
  }
  //用于computed
  var filter = {
    all: function(list) {
      return list;
    },
    left: function(list) {
      return list.filter(function(item) {
        return !item.hasDone;
      });
    },
    done: function(list) {
      return list.filter(function(item) {
        return item.hasDone;
      });
    }
  }
  export default {
    name: 'todolist',
    data () {
      return {
        todolist: todoStorage.fetch(),
        btnGroup: [{
          title: '全部',
          status: 'all'
        }, {
          title: '未完成',
          status: 'left'
        }, {
          title: '已完成',
          status: 'done'
        }],
        whattodo: '',
        select: 'all',
        editItem: null
        }
    },

    methods: {
      addTodo: function() {
        //this指当前Vue这个实例
        if (this.whattodo && this.whattodo.trim()) {
          this.todolist.push({
            text: this.whattodo,
            hasDone: false,
            id: todoStorage.totalId++
          });
          this.whattodo = '';
        }
      },
      removeItem: function(currentItem) {
        // console.log(currentItem);//传入的当前item
        this.todolist.splice(this.todolist.indexOf(currentItem), 1);
      },
      removeDone: function() {
        this.todolist = filter.left(this.todolist);
      },
      changeStatus: function(currentItem) {
        switch (currentItem.status) {
          case 'all':
            this.select = 'all'
            break;
          case 'left':
            this.select = 'left'
            break;
          case 'done':
            this.select = 'done'
            break;
        }
      },
      editTodo: function(currentItem) {
        this.beforeEditCache = currentItem.text;
        this.editItem = currentItem;
      },
      doneEdit: function(currentItem) {
        if (!this.editItem) {
          return;
        }
        this.editItem = null;
        currentItem.text = currentItem.text.trim();
        if (!currentItem.text) {
          this.removeItem(currentItem);
        }
      },
      cancelEdit: function(currentItem) {
        this.editItem = null;
        currentItem.text = this.beforeEditCache;
      }
    },

    watch: {
      todolist: {
        handler: function(val) {
          todoStorage.save(val);
        },
        deep: true
      }
    },

    computed: {
      filterList: function() {
        return filter[this.select](this.todolist);
      },
      total: function() {
        return this.todolist.length;
      },
      remaining: function() {
        return filter.left(this.todolist).length;
      }
    },

    filters: {
      pluralize: function(value) {
        return value === 1 ? 'item' : 'items';
      }
    },

    directives: {
      autofocus: function(e) { //inserted是钩子函数
        e.focus();
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
a{
  text-decoration: none;
}
.todo-input{
  padding: 5px;
}
.btn-group{
  display: inline-block;
  font-size: 0;
}
.btn{
  padding: 5px 10px;
  color: #333;
  font-size: 12px;
  background-color: #eee;
  border-radius: 2px;
  border: 1px solid #eee;
}
.btn:hover,
.btn-delete:hover{
  opacity: 0.8;
}
.btn-delete{
  margin-left: 20px;
  padding: 5px 10px;
  color: #fff;
  font-size: 12px;
  background-color: #d9534f;
  border-radius: 2px;
  border: 1px solid #d9534f;
}
.finished{
  text-decoration: line-through;
}
.btn.active{
  border-color: #3380ff;
}
.btn.active:hover{
  opacity: 1;
}
.edit{
  display: none;
}
.editing{
  position: relative;
}
.editing .edit{
  position: absolute;
  top: 0;
  left: 0;
  width: 200px;
  display: block;
}
.editing .view{
  display: none;
}
.unit{
  margin-left: 5px;
  margin-right: 5px;
}
</style>
