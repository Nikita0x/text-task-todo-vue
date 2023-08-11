<template>
  <div class="bg-slate-800">
    <div class="flex flex-col justify-center text-slate-200">
      <h1>To-do app</h1>
      <label for="input">New task: {{ inputValue }}</label>
      <input @keyup.enter="addNewTask()" v-model="inputValue" type="text" id="input" class="text-black">
      <button @click="addNewTask()" class="p-2 bg-slate-800 hover:bg-slate-900 active:bg-slate-950 transition">Add new task</button>
      <div v-for="item in tasksArray" :key="item.id">
        <div class="border p-2">


          <!-- Editing mode: off -->
          <div v-if="!isEditing" class="flex space-x-3 pb-4">
            <input :id="item.id" @change="item.isDone = !item.isDone" :checked="item.isDone" type="checkbox" class="w-[70px]">
            <label :for="item.id"  :class="item.isDone ? 'line-through italic text-gray-700' : ''">{{ item.title }}</label>

          <!-- delete button -->
          <button @click="deleteTask(item.id)" class=" ml-4 bg-red-800 hover:bg-red-900 active:bg-red-950 p-1 rounded-sm transition">Delete</button>

          <!-- edit button -->
          <button @click="item.isEditing = !item.isEditing" class=" ml-4 bg-green-800 hover:bg-green-900 active:bg-green-950 p-1 rounded-sm transition">Edit</button>
          <p>isEditing? {{ item.isEditing }}</p>
          </div>

          <!-- Editing mode: on -->
          <div v-else class="flex flex-col space-x-3 pb-4">
            <div>
              <input @keyup.enter="editTitle(item.title, item.id)" v-model="newTitle" :id="item.id" @change="item.isDone = !item.isDone" :checked="item.isDone" type="text" class="w-[20rem] text-black">
            </div>
            <div class="flex">
              <button @click="item.isEditing = !item.isEditing" class=" ml-4 bg-red-800 hover:bg-red-900 active:bg-red-950 p-1 rounded-sm transition">Cancel</button>
              <button @click="editTitle(item.title, item.id)" class=" ml-4 bg-green-800 hover:bg-green-900 active:bg-green-950 p-1 rounded-sm transition"> OK</button>
              <p>isEditing? {{ item.isEditing }}</p>
            </div>  
          </div>
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
    isEditing: false,
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
const newTitle = ref('')
function editTitle(title, id) {
  tasksArray.value.forEach((item) => {
    if(item.id === id) {
      item.title = newTitle.value;
    }
  })
  isEditing.value = false;
}

</script>