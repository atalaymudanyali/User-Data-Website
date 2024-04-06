<template>
  <div>
    <table>
      <thead>
        <tr>
          <th>First Name</th>
          <th>Last Name</th>
          <th>Birthday (YYYYMMDD)</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(person, index) in personData" :key="index">
          <td><input type="text" v-model="person.firstname" @change="validatePerson(index)" /></td>
          <td><input type="text" v-model="person.lastname" @change="validatePerson(index)" /></td>
          <td><input type="text" v-model="person.birthday" @change="validatePerson(index)" /></td>
          <td><button @click="deletePerson(index)">Delete</button></td>
        </tr>
      </tbody>
    </table>
    <div v-if="errorMessage">{{ errorMessage }}</div>
    <div>
      <input type="text" v-model="newPerson.firstname" placeholder="First Name" />
      <input type="text" v-model="newPerson.lastname" placeholder="Last Name" />
      <input type="text" v-model="newPerson.birthday" placeholder="Birthday (YYYYMMDD)" />
      <button @click="addPerson">Add Person</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

class Person {
  firstname: string;
  lastname: string;
  birthday: string;

  constructor(firstname: string, lastname: string, birthday: string) {
    this.firstname = firstname;
    this.lastname = lastname;
    this.birthday = birthday;
  }
}

const personData = ref<Person[]>([]);
const newPerson = ref<Person>({ firstname: '', lastname: '', birthday: '' });
const errorMessage = ref('');

function addPerson() {
  if (validatePersonData(newPerson.value)) {
    personData.value.push(new Person(newPerson.value.firstname, newPerson.value.lastname, newPerson.value.birthday));
    newPerson.value = { firstname: '', lastname: '', birthday: '' };
  }
}

function deletePerson(index: number) {
  personData.value.splice(index, 1);
}

function validatePerson(index: number) {
  const person = personData.value[index];
  if (!validatePersonData(person)) {
    errorMessage.value = 'Invalid person data';
  } else {
    errorMessage.value = '';
  }
}

function validatePersonData(person: Person) {
  const { firstname, lastname, birthday } = person;
  if (!firstname || !lastname || !birthday) {
    errorMessage.value = 'All fields are required';
    return false;
  }

  const birthdayRegex = /^[12][0-9]{3}(0[1-9]|1[0-2])(0[1-9]|[12][0-9]|3[01])$/;
  if (!birthday.match(birthdayRegex)) {
    errorMessage.value = 'Invalid birthday format (YYYYMMDD)';
    return false;
  }

  const year = parseInt(birthday.substring(0, 4));
  const month = parseInt(birthday.substring(4, 6));
  const day = parseInt(birthday.substring(6, 8));
  const validDate = new Date(year, month - 1, day);

  if (validDate.getFullYear() !== year || validDate.getMonth() !== month - 1 || validDate.getDate() !== day) {
    errorMessage.value = 'Invalid birthday date';
    return false;
  }

  const minDate = new Date(1900, 0, 1);
  const maxDate = new Date(2099, 11, 31);
  if (validDate < minDate || validDate > maxDate) {
    errorMessage.value = 'Birthday should be between 19000101 and 20991231';
    return false;
  }

  return true;
}
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #dddddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #303030;
}
</style>
