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
          label-for="task-input"
          invalid-feedback="Надо что-то ввести"
        >
          <b-form-input
            id="task-input"
            v-model="newTask"
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
</div>
</template>
<script>
import 'bootstrap/dist/css/bootstrap.css';

export default {
  name: 'todo.vue',
  data() {
    const items = () => {
      let taskList = [];
      if (localStorage.getItem('tasks')) {
        taskList = JSON.parse(localStorage.getItem('tasks'));
      }
      return taskList;
    };
    const addTaskModal = {
      newTask: '',
      nameState: null,
    };
    const fields = [
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
    ];
    return {
      fields,
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
      // e.preventDefault();
      // Trigger submit handler
      this.handleSubmit();
    },
    handleSubmit() {
      // Exit when the form isn't valid
      if (!this.checkFormValidity()) {
        return;
      }
      this.newID = this.items.length + 1;
      console.log(this.data().items);
      this.taskList.push({ id: this.newID, describe: this.newTask, status: false });
      localStorage.setItem('tasks', JSON.stringify(this.taskList));
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
