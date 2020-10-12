<template>
<div class = 'page'>
  <h3> TODO List: </h3>
  <b-button v-b-modal.add-new-task class="add-task">Добавить задачу</b-button>

  <b-modal
      id="add-new-task"
      ref="modal"
      title="Добавление новой задачи"
      @show="resetModal"
      @hidden="resetModal"
      @ok="handleOk"
    >
      <form ref="form" @submit.stop.prevent="handleSubmit">
        <b-form-group
          :state="nameState"
          label="Что запланируем?"
          label-for="name-input"
          invalid-feedback="Надо что-то ввести"
        >
          <b-form-input
            id="task-input"
            v-model="name"
            :state="nameState"
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
  <!-- <div v-for='(todo,key) in todo_list' :key='key'>
      <p>{{ todo.id }} | {{ todo.describe }} | {{ todo.status }} </p>
  </div> -->
</div>
</template>
<script>
import 'bootstrap/dist/css/bootstrap.css';

export default {
  name: 'todo.vue',
  data() {
    // const test = [
    //   {
    //     id: 1,
    //     describe: 'Освоить Vue',
    //     status: false,
    //   },
    //   {
    //     id: 2,
    //     describe: 'Пройти аттестацию модуля С',
    //     status: false,
    //   },
    //   {
    //     id: 3,
    //     describe: 'Выпить пивка в субботу',
    //     status: false,
    //   },
    // ];

    // localStorage.setItem('tasks', JSON.stringify(test));
    const items = () => {
      const taskList = JSON.parse(localStorage.getItem('tasks'));
      return taskList;
    };
    const addTaskModal = {
      newTask: '',
      nameState: null,
    };
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
      addTaskModal,
    };
  },
  methods: {
    checkFormValidity() {
      const valid = this.$refs.form.checkValidity();
      this.nameState = valid;
      return valid;
    },
    resetModal() {
      this.newTask = '';
      this.nameState = null;
    },
    // handleOk(bvModalEvt) {
    handleOk() {
      // Prevent modal from closing
      // bvModalEvt.preventDefault();
      // Trigger submit handler
      this.handleSubmit();
    },
    handleSubmit() {
      // Exit when the form isn't valid
      if (!this.checkFormValidity()) {
        return;
      }
      // Push the name to submitted names
      // this.submittedNames.push(this.name);
      const tasksInLS = JSON.parse(localStorage.getItem('tasks'));
      this.newID = tasksInLS.length + 1;
      tasksInLS.push({ id: this.newID, describe: this.newTask, status: false });
      console.log(tasksInLS);
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
