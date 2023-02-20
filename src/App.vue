<template>
  <TodoList />
</template>

<script>
import TodoList from './components/TodoList.vue'

export default {
  name: 'App',
  components: {
    TodoList
  }
}
</script>

<style>
#app {
  font-family: 'Corbel';
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100vw;
  height: 100vh;
}
:root{
  --color-blue:rgb(38, 42, 65);
  --color-blue-light:rgb(60, 63, 84);
  --color-white:rgb(255, 255, 255);
  --color-green: rgb(54, 217, 216);
  --color-grey-light:rgb(229, 235, 240);
  --color-grey-light-transparent:rgb(229, 235, 240,0.95);
  --color-grey:rgb(152, 154, 165);
  --color-grey-dark:rgb(119, 121, 135);
}
html {
  font-size: 16px;
}

body{
  margin:0
}
p, h1, h2, h3, textarea, input, button {
  margin:0;
  padding:0;
}

input::placeholder{
  font-family: 'Corbel';
  color:var(--color-grey-dark);
}

@media screen and (max-width: 768px) {

html {
  font-size: 14px;
}
}

@media screen and (max-width: 480px) {

html {
  font-size: 10px;
}
}

/* width */
::-webkit-scrollbar {
  width: 5px;
}

/* Track */
::-webkit-scrollbar-track {
  background: #f1f1f1;
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: var(--color-blue-light);
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: var(--color-blue);
}
</style>
