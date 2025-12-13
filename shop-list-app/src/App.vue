<script setup>
import { ref, onMounted, watch } from 'vue'

// Локальный стейт списка
const items = ref([])
const newItem = ref('')

const addItem = () => {
  const value = newItem.value.trim()
  if (value) {
    items.value.push({ text: value, bought: false })
    newItem.value = ''
  }
}
const removeItem = (index) => {
  items.value.splice(index, 1)
}
const toggleBought = (index) => {
  items.value[index].bought = !items.value[index].bought
}
const clearAll = () => {
  items.value = []
}

onMounted(() => {
  const saved = localStorage.getItem('shoplist-items')
  if (saved) {
    try {
      items.value = JSON.parse(saved)
    } catch {}
  }
})

watch(items, (val) => {
  localStorage.setItem('shoplist-items', JSON.stringify(val))
}, { deep: true })

</script>

<template>
  <div class="shop-list-app">
    <header>
      <img alt="ShopList logo" class="logo" src="./assets/logo.svg" width="80" height="80" />
      <h1>Список покупок</h1>
    </header>

    <form @submit.prevent="addItem" class="add-form">
      <input v-model="newItem" type="text" placeholder="Добавьте товар..." autofocus />
      <button type="submit">Добавить</button>
    </form>
    <ul class="shop-list">
      <li v-for="(item, i) in items" :key="i">
        <label>
          <input type="checkbox" v-model="item.bought" />
          <span :class="{ bought: item.bought, notBought: !item.bought }">{{ item.text }}</span>
        </label>
        <button @click="removeItem(i)" title="Удалить">✕</button>
      </li>
    </ul>
    <button v-if="items.length" @click="clearAll" class="clear-btn">Очистить всё</button>
  </div>
</template>

<style scoped>
.shop-list-app {
  max-width: 430px; /* увеличена ширина */
  margin: 30px auto;
  background: #fff;
  border-radius: 15px;
  box-shadow: 0 2px 18px #0001;
  padding: 2rem 1.2rem 2.5rem 1.2rem;
  box-sizing: border-box;
}
header {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  margin-bottom: 1.5rem;
}
h1 {
  font-size: 1.7rem;
  color: #333;
}
.add-form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.2rem;
  width: 100%;
}
.add-form input[type="text"] {
  flex: 1;
  min-width: 0;
  padding: 0.5em 0.7em;
  border-radius: 6px;
  border: 1px solid #c8e6c9;
  outline: none;
  box-sizing: border-box;
}
.add-form button {
  background: #42b883;
  color: #fff;
  border: none;
  border-radius: 6px;
  font-size: 1em;
  padding: 0 1.1em;
  cursor: pointer;
  transition: 0.15s background;
  min-width: 90px;
  white-space: nowrap;
  min-width: 0;
}
.add-form button:hover {
  background: #36946c;
}
.shop-list {
  list-style: none;
  margin: 0;
  padding: 0;
  background: #f5f5f5; /* Added background for the list */
}
.shop-list li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0.4em 0;
  gap: 0.7em;
  border-bottom: 1px solid #eee;
}
.shop-list li:last-child {
  border-bottom: none;
}
.shop-list input[type='checkbox'] {
  margin-right: 0.5em;
  accent-color: #42b883;
}
.shop-list .bought {
  text-decoration: line-through;
  color: #aaa;
  font-style: italic;
}
.shop-list .notBought {
  color: #222;
  background: none;
}
.shop-list button {
  background: none;
  border: none;
  color: #888;
  font-size: 1.3em;
  cursor: pointer;
  padding: 0 0.1em;
}
.shop-list button:hover {
  color: #e74c3c;
}
.clear-btn {
  display: block;
  margin: 1.2em auto 0 auto;
  padding: 0.45em 1.3em;
  background: #e74c3c;
  color: #fff;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1em;
}
.clear-btn:hover {
  background: #ba3222;
}
@media (max-width: 480px) {
  .shop-list-app {
    padding: 1.1rem 0.4rem 1.4rem 0.4rem;
    max-width: 100vw;
  }
  .add-form button {
    padding: 0 0.7em;
  }
}
</style>
