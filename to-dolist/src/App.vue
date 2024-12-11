<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');
const input_content = ref('');
const input_category = ref(null);

const todos_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt));

watch(name, (newVal) => {
    localStorage.setItem('name', newVal);
});

watch(todos, (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal));
}, {
    deep: true
});

const addTodo = () => {
    if (input_content.value.trim() === '' || input_category.value === null) {
        return;
    }

    todos.value.push({
        content: input_content.value,
        category: input_category.value,
        done: false,
        editable: false,
        createdAt: new Date().getTime()
    });

    input_content.value = '';
    input_category.value = null;
};

const removeTodo = (todo) => {
    todos.value = todos.value.filter((t) => t !== todo);
};

onMounted(() => {
    name.value = localStorage.getItem('name') || '';
    todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<template>
    <main class="app">
        <section class="greeting">
            <h2 class="title">
                Welcome, <input type="text" id="name" placeholder="Enter your name" v-model="name" />
            </h2>
        </section>

        <section class="create-todo">
            <h3>Create a New Task</h3>

            <form id="new-todo-form" @submit.prevent="addTodo">
                <input 
                    type="text" 
                    name="content" 
                    id="content" 
                    placeholder="What needs to be done?" 
                    v-model="input_content" />

                <div class="options">
                    <label>
                        <input type="radio" name="category" value="work" v-model="input_category" />
                        <span class="bubble work"></span>
                        Work
                    </label>
                    <label>
                        <input type="radio" name="category" value="personal" v-model="input_category" />
                        <span class="bubble personal"></span>
                        Personal
                    </label>
                </div>

                <button type="submit">Add Task</button>
            </form>
        </section>

        <section class="todo-list">
            <h3>Your Tasks</h3>
            <div class="list">
                <div v-for="todo in todos_asc" :key="todo.createdAt" class="todo-item" :class="{ done: todo.done, [todo.category]: true }">
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span class="bubble" :class="todo.category"></span>
                    </label>
                    <div class="content">
                        <input type="text" v-model="todo.content" />
                    </div>
                    <button class="delete" @click="removeTodo(todo)">Delete</button>
                </div>
            </div>
        </section>
    </main>
</template>
