<template>
    <div class="bg-slate-800 text-slate-200 h-full pb-20">
      <div class="flex flex-col space-y-5">

        <div class="flex mx-auto flex-col items-center w-96 space-y-2 pt-2">
            <h1 class="text-center text-3xl">To-do app</h1>
            <label v-if="inputValue" class="text-2xl" for="input">New task: {{ inputValue }}</label>
            <span v-if="disabled" class="text-red-800">Input field cannot be empty!</span>
            <input @keyup.enter="addNewTask()" v-model.trim="inputValue" type="text" id="input" class="text-black p-4 focus:outline-none rounded-sm text-2xl" :class="{ shake: disabled }" placeholder="Do the laundry...">
            <button @click="addNewTask()" class="p-2 text-2xl bg-orange-800 hover:bg-orange-900 active:bg-orange-950 rounded-sm transition">Add new task</button>
        </div>

        <div v-for="item in tasksArray" :key="item.id" class="flex flex-col mx-auto items-start w-72 pt-2">

            <div class="flex items-center space-x-5" v-if="!item.isEditing">
                <input @click="item.isDone = !item.isDone"  :checked="item.isDone" type="checkbox" :id="item.id" class="w-7 h-7 checked:ring-black">
                <label :for="item.id" class="text-2xl"  :class="item.isDone ? 'line-through italic text-gray-700' : ''">{{ item.title }}</label>
            </div>


            <input v-else v-model.trim="newTitle" type="text" class="text-black p-4 focus:outline-none rounded-sm text-2xl" @keyup.enter="changeTitle(item.id,item.isEditing)">
            <div class="flex pt-5">
                <button v-if="!item.isEditing" @click="item.isEditing = !item.isEditing" class="bg-green-800 hover:bg-green-900 active:bg-green-950 transition p-1 rounded-sm mr-5 text-xl">Edit</button>
                <button v-else @click="changeTitle(item.id,item.isEditing)" class="bg-green-800 hover:bg-green-900 active:bg-green-950 transition p-1 rounded-sm mr-5 text-xl">Confirm</button>
                <button v-if="!item.isEditing" @click="deleteTask(item.id)" class="bg-red-800 hover:bg-red-900 active:bg-red-950 transition p-1 rounded-sm text-xl"> Delete</button>
                <button v-else @click="item.isEditing = !item.isEditing"  class="bg-red-800 hover:bg-red-900 active:bg-red-950 transition p-1 rounded-sm text-xl"> Cancel</button>
            </div>
        </div>

        <div v-if="tasksArray.length == 0" class="flex items-center flex-col justify-center mx-auto">
            <p class="text-2xl"> No new tasks!  😭</p>
            <img class="max-w-[20rem]" src="../assets/sadcat.webp" alt="sad kitten">
        </div>


        <div v-else-if="tasksArray.length !== 0 && allTasksDone" class="flex items-center flex-col justify-center mx-auto">
            <p class="text-2xl">All tasks are done! 🥳</p>
            <img class="max-w-[20rem]" src="../assets/coolcat.webp" alt="sad kitten">
        </div>

        <div v-else class="flex items-center flex-col justify-center mx-auto">
            <p class="text-2xl text-gray-700">Not all tasks are done!</p>
        </div>



      </div>
    </div>
  </template>
  
  <script setup>
  import {ref, onMounted, computed} from "vue"

  //get items from LocalStorage
  onMounted(() => {
     const items = localStorage.getItem('tasks')
     if(items) {
        const parsedItems = JSON.parse(items);
        tasksArray.value.push(...parsedItems)
        console.log(tasksArray.value)
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

  //is all tasks done?
  const allTasksDone = computed(() => {
    return tasksArray.value.every((item) => {
        return item.isDone === true;
    })
  })
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