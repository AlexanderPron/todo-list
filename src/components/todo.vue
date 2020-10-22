<template>
<div class = 'page'>
  <h3> TODO List: </h3>
  <b-button v-b-modal.add-new-task class="add-task">Добавить задачу</b-button>

  <b-modal
      id="add-new-task"
      ref="modal"
      title="Добавление новой задачи"
      @ok="handleOk"
    >
      <form ref="form" @submit.stop.prevent="handleSubmit">
        <b-form-group
          label="Что запланируем?"
          label-for="task-input"
          invalid-feedback="Надо что-то ввести"
        >
          <b-form-input
            id="task-input"
            v-model="newTask"
            required
          ></b-form-input>
        </b-form-group>
      </form>
    </b-modal>

    <b-modal
      id="edit-task"
      ref="modal"
      title="Изменение задачи"
      @ok="handleEditTaskOk"
    >
      <form ref="form" @submit.stop.prevent="handleEditTaskSubmit">
        <b-form-group
          label="Измените задачу"
          label-for="edit-task-input"
          invalid-feedback="Надо что-то ввести"
        >
          <b-form-input
            id="edit-task-input"
            v-model="editTask"
            required
          ></b-form-input>
        </b-form-group>
      </form>
    </b-modal>

  <b-table
    small
    striped
    :fields="fields"
    :items="items"
    ref="selectableTable"
    selectable
    :select-mode="selectMode"
    @row-selected="onRowSelected"
    responsive="sm">
    <template v-slot:cell(edit)="row">
      <b-button v-b-modal.edit-task size="sm" @click="changeTask(row.item)" class="mr-2">Изменить
      </b-button>
    </template>
    <template v-slot:cell(selected)="{ rowSelected }">
      <template v-if="rowSelected">
        <span aria-hidden="true">&check;</span>
      </template>
      <template v-else>
        <span aria-hidden="true">&nbsp;</span>
      </template>
    </template>
  </b-table>
  <div class="done-btn-div">
    <b-button @click.stop.prevent="doneSelectedTasks(selected)" class="done-btn">
      Завершить выбранные
    </b-button>
    <b-button @click.stop.prevent="deleteSelectedTasks(selected)" class="delete-btn">
      Удалить выбранные
    </b-button>
  </div>
</div>
</template>
<script>
import 'bootstrap/dist/css/bootstrap.css';

const inProgress = 'В процессе';
const done = 'Выполнено';
export default {
  name: 'todo.vue',
  data() {
    const newTask = '';
    const editTask = '';
    const items = [];
    return {
      fields: [
        {
          key: 'describe',
          label: 'Задача',
        },
        {
          key: 'status',
          label: 'Статус',
          thClass: 'tdStatusClass',
        },
        {
          key: 'edit',
          label: '',
          thClass: 'tdEditClass',
        },
        {
          key: 'selected',
          label: '',
          thClass: 'tdSelectClass',
        },
      ],
      selectMode: 'multi',
      selected: [],
      items,
      newTask,
      editTask,
    };
  },
  methods: {
    showTodos() {
      if (localStorage.getItem('tasks')) {
        this.items = () => {
          const taskList = JSON.parse(localStorage.getItem('tasks'));
          return taskList;
        };
      }
    },
    onRowSelected(items) {
      this.selected = items;
    },
    checkFormValidity() {
      const valid = this.$refs.form.checkValidity();
      return valid;
    },
    handleOk() {
      this.handleSubmit();
    },
    handleSubmit() {
      // Exit when the form isn't valid
      if (!this.checkFormValidity()) {
        return;
      }
      let tasksInLS = [];
      if (localStorage.getItem('tasks') !== null) {
        tasksInLS = JSON.parse(localStorage.getItem('tasks'));
        const newID = tasksInLS.length + 1;
        const newElement = { id: newID, describe: this.newTask, status: inProgress };
        tasksInLS.push(newElement);
      } else {
        this.newID = 1;
        tasksInLS = [{ id: this.newID, describe: this.newTask, status: inProgress }];
      }
      localStorage.setItem('tasks', JSON.stringify(tasksInLS));
      this.newTask = '';
      this.showTodos();
      this.$nextTick(() => {
        this.$bvModal.hide('add-new-task');
      });
    },
    handleEditTaskOk() {
      this.handleEditTaskSubmit();
    },
    handleEditTaskSubmit() {
      if (!this.checkFormValidity()) {
        return;
      }
      const updatedTask = JSON.parse(localStorage.getItem('oldTask'));
      updatedTask.describe = this.editTask;
      const tasksInLS = JSON.parse(localStorage.getItem('tasks'));
      tasksInLS.forEach((task) => {
        if (task.id === updatedTask.id) {
          task.describe = updatedTask.describe; // eslint-disable-line no-param-reassign
        }
      });
      localStorage.setItem('tasks', JSON.stringify(tasksInLS));
      localStorage.removeItem('oldTask');
      this.showTodos();
    },
    changeTask(record) {
      this.editTask = record.describe;
      localStorage.setItem('oldTask', JSON.stringify(record));
    },
    doneSelectedTasks(selected) {
      const tasksInLS = JSON.parse(localStorage.getItem('tasks'));
      selected.forEach((selectedTask) => {
        tasksInLS.forEach((task) => {
          if (task.id === selectedTask.id) {
            task.status = done; // eslint-disable-line no-param-reassign
          }
        });
      });
      localStorage.setItem('tasks', JSON.stringify(tasksInLS));
      this.showTodos();
    },
    deleteSelectedTasks(selected) {
      let tasksInLS = JSON.parse(localStorage.getItem('tasks'));
      selected.forEach((selectedTask) => {
        tasksInLS = tasksInLS.filter((item) => item.id !== selectedTask.id);
      });
      let i = 0;
      tasksInLS.forEach((task) => {
        i += 1;
        task.id = i; // eslint-disable-line no-param-reassign
      });
      localStorage.setItem('tasks', JSON.stringify(tasksInLS));
      this.showTodos();
    },
  },
  created() {
    this.showTodos();
  },
};
</script>
<style scoped>
h3 {
    text-align: center;
    color: aqua;
}
.page {
  width: 60%;
  margin: 0 auto;
}
.add-task{
  padding: 3px;
  margin-bottom: 5px;
  font-size: 0.8rem;
}
.done-btn-div{
  display: flex;
  justify-content: flex-end;
}
.done-btn, .delete-btn {
  margin: 0 0 0 10px;
}
.tdStatusClass{
  background-color: blue;
}
.tdEditClass{
  background-color: red;
}
.tdSelectClass{
  background-color: yellow;
}
</style>
