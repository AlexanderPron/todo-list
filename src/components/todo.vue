<template>
<div class = 'page'>
  <h3> TODO List: </h3>
  <b-button class="add-task">Добавить задачу</b-button>
  <b-table small fixed striped hover :fields="fields" :items="items">
    <template v-slot:cell(Внести изменения)="row">
      <b-button size="sm" @click="row.toggleDetails" class="mr-2">Изменить</b-button>
    </template>
    <template v-slot:cell(Выбор)="row">
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
    const items = [
      {
        'Номер п/п': 1,
        'Задача': 'Освоить Vue',
        'Статус': false,
      },
      // {
      //   id: 2,
      //   describe: 'Пройти аттестацию модуля С',
      //   status: false,
      // },
      // {
      //   id: 3,
      //   describe: 'Выпить пивка в субботу',
      //   status: false,
      // },
    ];
    return {
      fields: ['Номер п/п', 'Задача', 'Статус', 'Внести изменения', 'Выбор'],
      items,
    };
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
