<h1 align=center> 🛠️ Практика — Тема 2. Шаблоны, директивы, реактивность </h1>

---

## 🎯 Цель: Создать простую форму добавления задач и отобразить их на экране

---
<br>

## ✅ Шаг 1: Подготовим шаблон

1. Открой `App.vue`.
2. Очисти всё внутри `<template>`, `<script>` и `<style>`, чтобы начать с чистого листа.
3. Добавь базовую структуру:

```vue
<template>
  <div class="container">
    <h1>Мой менеджер задач</h1>

    <input
      v-model="newTask"
      type="text"
      placeholder="Введите задачу"
    />
    <button @click="addTask">Добавить</button>

    <ul>
      <li v-for="task in tasks" :key="task.id">
        {{ task.title }}
      </li>
    </ul>
  </div>
</template>
```

---
<br>

## ✅ Шаг 2: Опишем данные и методы

Добавим реактивные переменные и логику кнопки:

```js
<script>
export default {
  data() {
    return {
      newTask: '', // введённый текст
      tasks: []    // список задач
    }
  },
  methods: {
    addTask() {
      if (this.newTask.trim() === '') return

      this.tasks.push({
        id: Date.now(),         // уникальный id
        title: this.newTask     // текст задачи
      })

      this.newTask = '' // очистить поле
    }
  }
}
</script>
```

---
<br>

## ✅ Шаг 3: Добавим стили (по желанию)

```css
<style>
.container {
  max-width: 500px;
  margin: 40px auto;
  font-family: sans-serif;
}

input {
  padding: 8px;
  width: 70%;
  margin-right: 10px;
}

button {
  padding: 8px 16px;
}

ul {
  margin-top: 20px;
  padding-left: 20px;
}
</style>
```

---
<br>

## 🧪 Что должно получиться
* Ты можешь ввести текст в поле
* Нажать кнопку
* Задача появится в списке ниже
* Поле ввода очищается
  
---
<br>

## 📌 Объяснение

| Что делает | Как работает |
|---|---|
| `v-model` | Связывает поле ввода с переменной `newTask` |
| `@click` | Запускает метод `addTask` при нажатии кнопки |
| `v-for` | Перебирает массив `tasks` и выводит элементы |
| `:key` | Уникальный id для каждой задачи |

---
<br>

## ❗ Советы

* Убедись, что ты пишешь `v-model`, а не `vmodel`, и `@click`, а не `v-click`
* В `addTask()` можно добавить защиту от пустой строки (`.trim()`)

---
<br>

## ✅ Ты научился:

* Работать с шаблонами Vue
* Использовать `v-model` для ввода
* Создавать список с `v-for`
* Обрабатывать клики через `@click`
* Использовать реактивные данные

---

## 🗺️ Куда дальше

#### [🏡 На главную](../README.md)
<br>

#### [⬅️ Назад к теории](./THEORY.md)
#### [➡️ Перейти к заданиям](./TASK.md)