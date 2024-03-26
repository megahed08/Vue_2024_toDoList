<script setup>
  import { ref, onMounted, computed, watch } from 'vue'

  const toDos = ref([])
  const name = ref('')

  const input_content = ref('')
  const input_category = ref(null)

  const toDos_asc = computed(()=> toDos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  }))

  const addToDo = () => {
    if (input_category.value.trim() === '' || input_category.value === null){
      return
    }

    toDos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime()
    })

    input_content.value = ''
    input_category.value = null
  }

  const removeToDo = toDo => {
    toDos.value = toDos.value.filter(t => t !== toDo)
  }

  watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
  })

  watch(toDos, (newVal) => {
    localStorage.setItem('toDos', JSON.stringify(newVal))
  },{deep: true})

  onMounted(() =>{
    name.value = localStorage.getItem('name') || ''
  })
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hi, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-toDo">

      <form @submit.prevent="addToDo">
        <h4>What is on your toDo list?</h4>
        <input type="text" placeholder="e.g. buy grocery" v-model="input_content"/>

        <h4>Pick a category</h4>
        
        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input type="radio" name="category" id="category1" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

        </div>

        <input type="submit" value="Add toDo"/>
      </form>
    </section>

    <section class="toDo-list">
      <h4>Your ToDo List</h4>
      <div class="list">
        <div v-for="toDo in toDos_asc" :class="`toDo-item ${toDo.done && 'done'}}`">
          
          <label>
            <input type="checkbox" v-model="toDo.done" />
            <span :class="`bubble ${toDo.category}`"></span>
          </label>

          <div class="toDo-content">
            <input type="text" v-model="toDo.content"/>
          </div>

          <div class="actions">
            <button class="delete" @click="removeToDo(toDo)">Delete</button>
          </div>

        </div>
      </div>
    </section>
  </main>
   
</template>
