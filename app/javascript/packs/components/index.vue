<template>
  <div>
    <!-- 新規作成部分 -->
    <div class="row">
      <div class="col s10 m11">
        <input v-model="newTask"
               class="form-control"
               placeholder="Add your task!!">
      </div>
      <div class="col s2 m1">
        <div v-on:click="createTask"
             class="btn-floating waves-effect waves-light red">
          <i class="material-icons">add</i>
        </div>
      </div>
    </div>
    <!-- リスト表示部分 -->
    <div>
      <ul class="collection">
        <li v-for="task in tasks"
            v-if="!task.is_done"
            v-bind:id="'row_task_' + task.id"
            class="collection-item">
          <input type="checkbox" v-bind:id="'task_' + task.id">
          <label v-bind:for="'task_' + task.id">{{ task.name }}</label>
        </li>
      </ul>
    </div>
    <!-- 完了済みタスク表示ボタン -->
    <div class="btn" v-on:click="displayFinishedTasks">Display finished tasks</div>
    <!-- 完了済みタスク一覧 -->
    <div id="finished-tasks" class="display_none">
      <ul class="collection">
        <li v-for="task in tasks"
            v-if="task.is_done"
            v-bind:id="'row_task_' + task.id"
            class="collection-item">
          <input type="checkbox" v-bind:id="'task_' + task.id" checked="checked" />
          <label v-bind:for="'task_' + task.id" class="line-through">{{ task.name }}</label>
        </li>
      </ul>
    </div>
  </div>
</template>


<script>

  import axios from 'axios'

  export default {
    data: function() {
      return {
        tasks: [],
        newTask: ''
      }
    },
    mounted: function() {
      this.fetchTasks()
    },
    methods: {
      fetchTasks: function() {
        axios.get('/api/tasks').then((response) => {
          for(var i = 0; i < response.data.tasks.length; i++) {
            this.tasks.push(response.data.tasks[i])
          }
        }, (error) => {
          console.log(error)
        })
      },
      displayFinishedTasks: function() {
        document.querySelector('#finished-tasks').classList.toggle('display_none')
      },
      createTask: function() {
        if (!this.newTask) return;

        axios.post('/api/tasks', { task: { name: this.newTask } }).then((response) => {
          this.tasks.unshift(response.data.task)
          this.newTask = ''
        }, (error) => {
          console.log(error)
        })
      }
    }
  }

</script>


<style scoped>
  [v-cloak] {
    display: none;
  }
  .display_none {
    display: none;
  }
  .line-through {
    text-decoration: line-through;
  }
</style>
