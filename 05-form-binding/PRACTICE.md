# Тема 05: Практика 💻 Связываем текстовое поле с `v-model`

> <p align=center> 🛠️ Практика </p>

В теоретической части мы подробно разобрали директиву `v-model` для создания двусторонней связи между элементами форм и данными компонента. Теперь давай применим это на практике, добавив на нашу страницу поле для поиска (пока без самой логики поиска, только связывание).

<br>

## 🎯 Цель практики:

1.  Добавить на страницу текстовое поле ввода (`<input type="text">`).
2.  Создать в `data()` компонента `App.vue` новое свойство для хранения поискового запроса (например, `searchTerm`).
3.  Связать текстовое поле с этим свойством с помощью директивы `v-model`.
4.  Отобразить значение этого свойства на странице, чтобы наглядно продемонстрировать двустороннее связывание.

<br>

## 🛠️ Подготовка:

1.  Убедись, что твой проект (например, `my-bookstore-app`), над которым мы работали в **Теме 04** (со списком книг и обработчиками событий), открыт в VS Code.
2.  Запусти сервер для разработки, если он еще не запущен:
    ```bash
    npm run serve
    ```
3.  Открой в браузере страницу твоего приложения (обычно `http://localhost:8080/`).

<br>

## 🚀 Шаги выполнения:

### Шаг 1: Добавляем новое свойство в `data()`

В файле `src/App.vue`, в секции `<script>`, найди функцию `data()` и добавь в возвращаемый объект новое свойство для хранения текста, который будет вводить пользователь. Назовем его `searchTerm` и инициализируем пустой строкой.

```vue
<script>
export default {
  name: 'App',
  data() {
    return {
      pageTitle: 'Наш Книжный Каталог',
      totalLikes: 0,
      books: [
        // ... твой массив книг ...
      ],
      searchTerm: '' // <-- Наше новое свойство для поискового запроса
    };
  },
  methods: {
    // ... твои методы ...
  }
}
</script>
```

### Шаг 2: Добавляем поле ввода `<input>` в шаблон

Теперь в секции `<template>` файла `App.vue` добавим сам элемент `<input>`. Хорошее место для него — перед списком книг, под общим заголовком и счетчиком лайков.

```vue
<template>
  <div id="book-catalog-app">
    <h1>{{ pageTitle }}</h1>
    <p>❤️ Всего понравилось: {{ totalLikes }}</p>

    <div class="search-bar">
      <input type="text" placeholder="Поиск по названию...">
    </div>

    <div class="book-list">
      </div>
  </div>
</template>
```

Мы также обернули `input` в `div` с классом `search-bar` для возможной будущей стилизации.

### Шаг 3: Связываем поле ввода с данными с помощью `v-model`

Теперь самое главное: используем директиву `v-model` на нашем `<input>`, чтобы связать его со свойством `searchTerm` из `data()`.

Измени строку с `<input>` следующим образом:

```html
      <input type="text" placeholder="Поиск по названию..." v-model="searchTerm">
```

**Разбор:**
* `v-model="searchTerm"`: Эта директива автоматически:
  * Привязывает значение атрибута `value` этого поля ввода к свойству `this.searchTerm`.
  * При каждом вводе символа в это поле (событие `input`), обновляет свойство `this.searchTerm` текущим значением поля.

### Шаг 4: Отображаем значение `searchTerm` для демонстрации

Чтобы наглядно увидеть, что двустороннее связывание работает, давай выведем текущее значение `searchTerm` прямо под полем ввода.

```html
    <div class="search-bar">
      <input type="text" placeholder="Поиск по названию..." v-model="searchTerm">
      <p>Вы ищете: <strong>{{ searchTerm }}</strong></p>
    </div>
```

Теперь, когда ты будешь печатать в поле ввода, текст под ним будет мгновенно обновляться!

### Шаг 5: Проверьте результат в браузере

Сохрани файл `App.vue`.
* На странице должно появиться поле ввода и текст "Вы ищете: ".
* Начни вводить что-нибудь в текстовое поле. Ты должен сразу же видеть, как введенный текст отображается после "Вы ищете: ". Это и есть демонстрация того, как `v-model` обновляет свойство `searchTerm` в `data()`, а интерполяция `{{ searchTerm }}` тут же отображает это измененное значение.

<br>

## 📝 Итоговый примерный код (измененные части `App.vue`):

**Секция `<template>` (добавленные/измененные части):**
```vue
<template>
  <div id="book-catalog-app">
    <h1>{{ pageTitle }}</h1>
    <p>❤️ Всего понравилось: {{ totalLikes }}</p>

    <div class="search-bar">
      <label for="searchInput">Поиск: </label>
      <input type="text" id="searchInput" placeholder="Введите название книги..." v-model="searchTerm">
      <p>Текущий поисковый запрос: <strong>"{{ searchTerm }}"</strong></p>
    </div>
    <hr>

    <div class="book-list">
      </div>
  </div>
</template>
```

**Секция `<script>` (добавленное свойство в data):**

```vue
<script>
export default {
  name: 'App',
  data() {
    return {
      pageTitle: 'Наш Книжный Каталог',
      totalLikes: 0,
      books: [
        { id: 1, title: 'Vue.js: Путь к Мастерству', author: 'Елена Разработчикова', price: 1500, coverImageUrl: '[https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial](https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial)', inStock: true, isBestseller: true },
        { id: 2, title: 'JavaScript для Профессионалов', author: 'Иван Кодер', price: 1250, coverImageUrl: '[https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial](https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial)', inStock: false, isBestseller: false },
        { id: 3, title: 'HTML и CSS: Современные Техники', author: 'Ольга Верстальщица', price: 990, coverImageUrl: '[https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial](https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial)', inStock: true, isBestseller: false }
      ],
      searchTerm: '' // Наше свойство для v-model
    };
  },
  methods: {
    showBookDetails(book) {
      console.log(`--- Детализация книги ID: ${book.id} ---`);
      console.log('Название:', book.title);
      // ... остальная часть метода
    },
    incrementTotalLikes() {
      this.totalLikes++;
      console.log('Общий счетчик лайков:', this.totalLikes);
    }
  }
}
</script>
```

**Простые стили для search-bar (можно добавить в `<style>`):**

```css
.search-bar {
  margin-bottom: 20px;
  padding: 10px;
  background-color: #f9f9f9;
  border-radius: 5px;
}
.search-bar input[type="text"] {
  padding: 8px;
  margin-right: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  min-width: 250px;
}
.search-bar p {
  margin-top: 10px;
  font-style: italic;
  color: #555;
}
hr {
  margin: 20px 0;
}
```

<br>

## 🤔 Что мы сделали:
* Добавили новое свойство (`searchTerm`) в `data` для хранения пользовательского ввода.
* Вставили в шаблон элемент `<input type="text">`.
* Использовали директиву `v-model` для создания двусторонней связи между этим полем ввода и свойством `searchTerm`.
* Убедились в работе двустороннего связывания, отображая значение `searchTerm` на странице.

Это основа для создания интерактивных форм. В следующих темах мы сможем использовать `searchTerm` для реальной фильтрации нашего списка книг!

В `TASK.MD` ты сможешь поэкспериментировать с другими элементами форм и модификаторами `v-model`.

---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](./THEORY.md) | [Вперед ➡️](./TASK.md)** 

</div>