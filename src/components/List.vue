<template>
    <div class="bg-slate-800 text-slate-200">
      <div>

        <div class="flex mx-auto flex-col items-center w-52 space-y-2 pt-2">
            <h1 class="text-center text-xl">To-do app</h1>
            <label for="input">New task: {{ inputValue }}</label>
            <span v-if="disabled" class="text-red-800">Input cannot be empty!</span>
            <input @keyup.enter="addNewTask()" v-model.trim="inputValue" type="text" id="input" class="text-black p-4 focus:outline-none rounded-sm" :class="{ shake: disabled }">
            <button @click="addNewTask()" class="p-2 bg-orange-800 hover:bg-orange-900 active:bg-orange-950 rounded-sm transition">Add new task</button>
        </div>

        <div v-for="item in tasksArray" :key="item.id" class="flex mx-auto flex-col items-center w-52 space-y-2 pt-2">

            <div v-if="!item.isEditing">
                <input @click="item.isDone = !item.isDone"  :checked="item.isDone" type="checkbox" :id="item.id">
                <label :for="item.id"  :class="item.isDone ? 'line-through italic text-gray-700' : ''">{{ item.title }}</label>
            </div>


            <input v-else v-model.trim="newTitle" type="text" class="text-black" @keyup.enter="changeTitle(item.id,item.isEditing)">
            <button v-if="!item.isEditing" @click="item.isEditing = !item.isEditing" class="bg-green-800">Edit</button>
            <button v-else @click="changeTitle(item.id,item.isEditing)" class="bg-green-800">OK</button>
            <button v-if="!item.isEditing" @click="deleteTask(item.id)" class="bg-red-800"> Delete</button>
            <button v-else @click="item.isEditing = !item.isEditing"  class="bg-red-800"> Cancel</button>
        </div>

      </div>
    </div>
  </template>
  
  <script setup>
  import {ref, onMounted} from "vue"

  //get items from LocalStorage
  onMounted(() => {
     const items = localStorage.getItem('tasks')
     if(items) {
        const parsedItems = JSON.parse(items);
        tasksArray.value.push(...parsedItems)
     }
  })   
  
  const inputValue = ref('')
  const tasksArray = ref([]);
  
  function addNewTask() {
    if(inputValue.value === '') {
        warnDisabled()
    } else {
        const task = {
            id: crypto.randomUUID(),
            isDone: false,
            isEditing: false,
            title: inputValue.value,
        }
        tasksArray.value.push(task)
        inputValue.value = '';
    }

  }
  
  function deleteTask(id) {
    tasksArray.value.forEach((item, index) => {
      if(item.id === id) {
        tasksArray.value.splice(index, 1)
      }
    })
  }
  
  const newTitle = ref('')
  function changeTitle(id, isEditing) {
    tasksArray.value.forEach((item) => {
        if(item.id === id) {
            item.title = newTitle.value;
            newTitle.value = '';
            item.isEditing = !isEditing;
        }
    })
  }

  //set items to LocalStorage right before the page refresh
  window.addEventListener("beforeunload", () => {
    localStorage.setItem("tasks", JSON.stringify(tasksArray.value));
  });
  
  //animation if input is empty
  const disabled = ref(false)

  function warnDisabled() {
    disabled.value = true
    setTimeout(() => {
        disabled.value = false
    }, 1500)
  }
  
  </script>

  <style scoped>
  .shake {
    animation: shake 0.82s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
    transform: translate3d(0, 0, 0);
    }

    @keyframes shake {
    10%,
    90% {
        transform: translate3d(-1px, 0, 0);
    }

    20%,
    80% {
        transform: translate3d(2px, 0, 0);
    }

    30%,
    50%,
    70% {
        transform: translate3d(-4px, 0, 0);
    }

    40%,
    60% {
        transform: translate3d(4px, 0, 0);
    }
    }
  </style>