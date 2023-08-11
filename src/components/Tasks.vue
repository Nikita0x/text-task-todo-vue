<template>
  <div class="bg-slate-800">
    <div class="flex flex-col justify-center text-slate-200">
      <h1>To-do app</h1>
      <label for="input">New task: {{ inputValue }}</label>
      <input @keyup.enter="addNewTask()" v-model="inputValue" type="text" id="input" class="text-black">
      <button @click="addNewTask()" class="p-2 bg-slate-800 hover:bg-slate-900 active:bg-slate-950 transition">Add new task</button>
      <div v-for="item in tasksArray" :key="item.id">
        <div class="border p-2">

          <div v-if="!isEditing" class="flex space-x-3 pb-4">
            <input :id="item.id" @change="item.isDone = !item.isDone" :checked="item.isDone" type="checkbox" class="w-[70px]">
            <label :for="item.id"  :class="item.isDone ? 'line-through italic text-gray-700' : ''">{{ item.title }}</label>
          </div>

          <div v-else class="flex space-x-3 pb-4">
            <input :id="item.id" @change="item.isDone = !item.isDone" :checked="item.isDone" type="checkbox" class="w-[70px]">
            <button> OK</button>
          </div>

          <!-- delete button -->
          <button @click="deleteTask(item.id)" class=" ml-4 bg-red-800 hover:bg-red-900 active:bg-red-950 p-1 rounded-sm transition">Delete</button>

          <!-- edit button -->
          <button @click="editTitle(item.title)" class=" ml-4 bg-green-800 hover:bg-green-900 active:bg-green-950 p-1 rounded-sm transition">Edit</button>

          <p>is Done? {{ item.isDone }}</p>
          <p>{{ item.id }}</p>
        </div>

      </div>
    </div>
  </div>
</template>

<script setup>
import {ref} from "vue"

const inputValue = ref('')
const tasksArray = ref([]);

function addNewTask() {
  const task = {
    id: crypto.randomUUID(),
    isDone: false,
    title: inputValue.value,
}
  tasksArray.value.push(task)
  inputValue.value = '';

  console.log(tasksArray.value)
}

const isDone = ref(false)

function deleteTask(id) {
  tasksArray.value.forEach((item, index) => {
    if(item.id === id) {
      tasksArray.value.splice(index, 1)
    }
  })
}

const isEditing = ref(false)

function editTitle(title) {
  isEditing.value = true;

}

</script>