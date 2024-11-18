<template>
    <div class="wizard-form">
        <ProgressBar :currentStep="step" />
        <div class="row">
        <div class="col-sm-8 mx-auto">
            <form>
            <div v-show="step === 1" class="stepOne">
                <div class="form-group mb-3">
                <label for="title">Название проекта</label>
                <input v-model="titleInput" type="text" class="form-control" id="title">
                </div>

                <div class="form-group mb-3">
                <label for="startDate">Дата начала проекта</label>
                <input v-model="startDateInput" type="date" @change="updateMinEndDate" onkeydown="return false" class="form-control" id="startDate">
                </div>

                <div class="form-group mb-3">
                <label for="endDate">Дата окончания проекта</label>
                <input v-model="endDateInput" type="date" :min="startDateInput" onkeydown="return false" class="form-control" id="endDate">
                </div>

                <div class="form-group mb-3">
                <label for="priority">Приоритет проекта</label>
                <input v-model="priorityInput" type="number" min="0" max="20" onkeydown="return false" class="form-control" id="priority">
                </div>

                <button :disabled="!isStepOneCompleted" @click="nextStep" type="button" class="btn btn-primary float-end">Следующий шаг</button>
            </div>

            <div v-show="step === 2" class="stepTwo">
                <div class="form-group mb-3">
                <label for="customerCompany">Компания-заказчик</label>
                <input v-model="customerCompanyInput" type="text" class="form-control" id="customerCompany">
                </div>

                <div class="form-group mb-3">
                <label for="contractorCompany">Компания-исполнитель</label>
                <input v-model="contractorCompanyInput" type="text" class="form-control" id="contractorCompany">
                </div>

                <button @click="prevStep" type="button" class="btn btn-primary float-start">Предыдущий шаг</button>

                <button :disabled="!isStepTwoCompleted" @click="nextStep" type="button" class="btn btn-primary float-end">Следующий шаг</button>
            </div>

            <div v-show="step === 3" class="stepThree">
                <div class="form-group mb-3">
                <label class="mb-2">Руководитель проекта</label>
                <EmployeeSearch v-model="directorInput" class="mb-3" id="employeeSearch" />
                </div>

                <button @click="prevStep" type="button" class="btn btn-primary float-start">Предыдущий шаг</button>

                <button :disabled="!isStepThreeCompleted" @click="nextStep" type="button" class="btn btn-primary float-end">Следующий шаг</button>
            </div>

            <div v-show="step === 4" class="stepFour">
                <div class="form-group mb-3">
                <label>Сотрудники проекта</label>
                <EmployeesSearch v-model="workersInput" class="mb-3" id="employeesSearch"/>
                </div>

                <button @click="prevStep" type="button" class="btn btn-primary float-start">Предыдущий шаг</button>

                <button @click="nextStep" type="button" class="btn btn-primary float-end">Следующий шаг</button>
            </div>

            <div v-show="step === 5" class="stepFive">
                <FileUploader class="mb-3" />

                <button @click="prevStep" type="button" class="btn btn-primary float-start">Предыдущий шаг</button>

                <button @click="submit" type="button" class="btn btn-primary float-end">Завершить</button>
            </div>
            </form>
        </div>
        </div>
    </div>
</template>

<script>
import FileUploader from './HTML5FileUploader.vue';
import EmployeeSearch from './EmployeeSearch.vue';
import EmployeesSearch from './EmployeesSearch.vue';
import ProgressBar  from './ProgressBar.vue';
import axios from 'axios';

export default {
  data() {
    return {
      step: 1,
      titleInput: '',
      startDateInput: '',
      endDateInput: '',
      priorityInput: 0,
      customerCompanyInput: '',
      contractorCompanyInput: '',
      directorInput: '',
      workersInput: [],
    }
  },
  computed: {
    isStepOneCompleted() {
      return this.titleInput && this.startDateInput && this.endDateInput
    },
    isStepTwoCompleted() {
      return this.customerCompanyInput && this.contractorCompanyInput
    },
    isStepThreeCompleted() {
      return this.directorInput
    },
  },
  methods: {
    updateMinEndDate(){
      if (this.endDateInput < this.startDateInput){
        this.endDateInput = '';
    }
    },
    nextStep() {
      this.step++
    },
    prevStep() {
      this.step--
    },
    async submit() {
      var custComp = '';
      var contComp = '';

      await axios.get(`https://localhost:7292/Company/${this.customerCompanyInput}`)
        .then(response => {
          custComp = response.data["id"]
        })
        .catch(error => {
          console.log(error.response);
          axios.post("https://localhost:7292/Company",
          {
            title: this.customerCompanyInput,
          })
          .then(response => {
            custComp = response.data["id"]
          });
        });

      await axios.get(`https://localhost:7292/Company/${this.contractorCompanyInput}`)
        .then(response => {
          contComp = response.data["id"]
        })
        .catch(error => {
          console.log(error.response);
          axios.post("https://localhost:7292/Company",
          {
            title: this.contractorCompanyInput,
          })
          .then(response => {
            contComp = response.data["id"]
          });
        });

      await axios.post("https://localhost:7292/Project",
        {
          title: this.titleInput,
          customerCompanyId: custComp,
          contractorCompanyId: contComp,
          workers: this.workersInput,
          directorId: this.directorInput,
          startDate: this.startDateInput,
          endDate: this.endDateInput,
          priority: this.priorityInput
        }
      )
      .then(response => {
        alert(`Проект ${response.data["title"]} успешно создан`)
      })
      .catch(error => {
        console.log(error.response);
        alert("Возникла ошибка");
      });
    }
  },
  components: {
    FileUploader,
    EmployeeSearch,
    EmployeesSearch,
    ProgressBar
  }
}
</script>

<style>

</style>