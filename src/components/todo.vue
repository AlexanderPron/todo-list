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

  <b-table small fixed striped hover :fields="fields" :items="items">
    <template v-slot:cell(edit)="row">
      <b-button size="sm" @click="row.toggleDetails" class="mr-2">Изменить</b-button>
    </template>
    <template v-slot:cell(select)="row">
      <b-form-checkbox v-model="row.detailsShowing" @change="row.toggleDetails"></b-form-checkbox>
    </template>
  </b-table>
  <div class="done-btn-div">
    <b-button class="done-btn">Завершить выбранные</b-button>
    <b-button class="delete-btn">Удалить выбранные</b-button>
  </div>
</div>
</template>
<script>
import 'bootstrap/dist/css/bootstrap.css';

export default {
  name: 'todo.vue',
  data() {
    const newTask = '';
    let items = [];
    if (localStorage.getItem('tasks')) {
      items = () => {
        const taskList = JSON.parse(localStorage.getItem('tasks'));
        return taskList;
      };
    }

    return {
      fields: [
        {
          key: 'id',
          label: 'Номер п/п',
        },
        {
          key: 'describe',
          label: 'Задача',
        },
        {
          key: 'status',
          label: 'Статус',
        },
        {
          key: 'edit',
          label: 'Внести изменения',
        },
        {
          key: 'select',
          label: 'Выбор',
        },
      ],
      items,
      newTask,
    };
  },
  methods: {
    checkFormValidity() {
      const valid = this.$refs.form.checkValidity();
      return valid;
    },
    handleOk(e) {
      // Prevent modal from closing
      e.preventDefault();
      // Trigger submit handler
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
        const newElement = { id: newID, describe: this.newTask, status: false };
        tasksInLS.push(newElement);
      } else {
        this.newID = 1;
        tasksInLS = [{ id: this.newID, describe: this.newTask, status: false }];
      }
      localStorage.setItem('tasks', JSON.stringify(tasksInLS));
      // Hide the modal manually
      this.$nextTick(() => {
        this.$bvModal.hide('add-new-task');
      });
    },
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

</style>
