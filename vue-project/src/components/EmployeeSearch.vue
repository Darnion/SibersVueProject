<template>
    <div class="employee-search">
      <input
        class="col-sm-8"
        type="text"
        v-model="query"
        @input="searchEmployees"
        placeholder="Введите имя сотрудника"
      />
      <ul v-if="employees.length > 0" class="dropdown">
        <li v-for="employee in employees" :key="employee.id" @click="selectEmployee(employee)">
          {{ `${employee["lastName"]} ${employee["firstName"]} ${employee["patronymic"]}`.trim() }}
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        query: '',
        selectedEmployee: '',
        employees: [],
      };
    },
    methods: {
      async searchEmployees() {
        this.selectedEmployee = '';
        this.transferData();

        if (this.query.length < 2) {
          this.employees = []; // Очистить список, если введено меньше 2 символов
          return;
        }
  
        try {
          const response = await axios.get(`https://localhost:7292/Employee/${this.query}`);
          this.employees = response.data; // Предполагается, что сервер возвращает массив сотрудников
        } catch (error) {
          console.error('Ошибка при поиске сотрудников:', error);
        }
      },
      selectEmployee(employee) {
        this.query = `${employee["lastName"]} ${employee["firstName"]} ${employee["patronymic"]}`.trim(); // Установить выбранного сотрудника в поле ввода
        this.selectedEmployee = employee["id"];
        this.transferData();
        this.employees = []; // Очистить список после выбора
      },
      transferData() {
        this.$emit('update:modelValue',
          this.selectedEmployee);
      }
    },
  };
  </script>
  
  <style scoped>
  .employee-search {
    position: relative;
  }
  
  .dropdown {
    border: 1px solid #ccc;
    border-radius: 4px;
    max-height: 150px;
    overflow-y: auto;
    position: absolute;
    width: 100%;
    background-color: white;
    z-index: 1000;
  }
  
  .dropdown li {
    padding: 10px;
    cursor: pointer;
  }
  
  .dropdown li:hover {
    background-color: #f0f0f0;
  }
  </style>
  