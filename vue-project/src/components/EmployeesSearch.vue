<template>
    <div class="employees-search">
      <div class="selected-employees">
        <span v-for="employee in selectedEmployees" :key="employee.id" class="selected-employee">
          {{ `${employee["lastName"]} ${employee["firstName"]} ${employee["patronymic"]}`.trim() }}
          <button @click="removeEmployee(employee)">×</button>
        </span>
      </div>
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
        employees: [],
        selectedEmployees: [], // Массив для хранения выбранных сотрудников
        selectedEmployeesIds: []
      };
    },
    methods: {
      async searchEmployees() {
        if (this.query.length < 2) {
          this.employees = []; // Очистить список, если введено меньше 2 символов
          return;
        }
  
        try {
          const response = await axios.get(`https://localhost:7292/Employee/${this.query}`)
          this.employees = response.data; // Предполагается, что сервер возвращает массив сотрудников
        } catch (error) {
          console.error('Ошибка при поиске сотрудников:', error);
        }
      },
      selectEmployee(employee) {
        if (!this.selectedEmployees.some(e => e.id === employee.id)) {
          this.selectedEmployees.push(employee); // Добавить сотрудника в выбранные
          this.transferData();
        }
        this.query = ''; // Очистить поле ввода после выбора
        this.employees = []; // Очистить список после выбора
      },
      removeEmployee(employee) {
        this.selectedEmployees = this.selectedEmployees.filter(e => e.id !== employee.id); // Удалить сотрудника из выбранных
        this.transferData();
      },
      transferData() {
        this.selectedEmployees.forEach((emp) => 
        {
          this.selectedEmployeesIds.push(emp["id"])
        });

        this.$emit('update:modelValue',
          this.selectedEmployeesIds);
      }
    },
  };
  </script>
  
  <style scoped>
  .employees-search {
    position: relative;
  }
  
  .selected-employees {
    display: flex;
    flex-wrap: wrap;
    margin-bottom: 10px;
  }
  
  .selected-employee {
    background-color: #e0e0e0;
    border-radius: 4px;
    padding: 5px;
    margin-right: 5px;
  }
  
  .selected-employee button {
    background: none;
    border: none;
    color: red;
    cursor: pointer;
    margin-left: 5px;
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
  