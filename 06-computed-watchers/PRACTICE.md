# Тема 06: Практика 💻 Фильтрация списка книг с помощью `computed`
> <p align=center> 🛠️ Практика </p>

В теоретической части мы узнали, что вычисляемые свойства (`computed`) идеально подходят для создания производных данных, которые зависят от других реактивных свойств, и при этом кэшируются для производительности. Одним из классических примеров использования `computed` является фильтрация или сортировка списков.

Давай используем вычисляемое свойство, чтобы отфильтровать наш список книг на основе значения `searchTerm`, которое мы вводим в поле поиска из предыдущей практики!

<br>

## 🎯 Цель практики:

1.  Создать вычисляемое свойство (например, `filteredBooks`), которое будет возвращать отфильтрованный массив книг.
2.  Фильтрация должна происходить на основе значения, введенного в поле поиска (`searchTerm`), по названию книги (без учета регистра).
3.  Отобразить на странице именно этот отфильтрованный список книг.

<br>

## 🛠️ Подготовка:

1.  Убедись, что твой проект (например, `my-bookstore-app`), над которым мы работали в **Теме 05** (с `searchTerm` и `<select>` для сортировки), открыт в VS Code.
2.  Запусти сервер для разработки, если он еще не запущен:
    ```bash
    npm run serve
    ```
3.  Открой в браузере страницу твоего приложения (обычно `http://localhost:8080/`). Ты должен видеть поле для ввода поискового запроса и список всех книг.

<br>

## 🚀 Шаги выполнения:

### Шаг 1: Обзор текущего состояния

На данный момент у нас есть:
* Массив `books` в `data()`, содержащий все наши книги.
* Свойство `searchTerm` в `data()`, связанное с полем ввода через `v-model`.
* Список книг в шаблоне отображается путем итерации `v-for="book in books" ...`.

Сейчас ввод текста в поле `searchTerm` изменяет данные, но визуально список книг не меняется. Наша задача — это исправить.

### Шаг 2: Создаем вычисляемое свойство `filteredBooks`

В файле `src/App.vue`, в секции `<script>`, добавь новую опцию `computed` (на том же уровне, что `data` и `methods`). Внутри этой опции определим наше вычисляемое свойство `filteredBooks`.

```html
<script>
export default {
  name: 'App',
  data() {
    return {
      // ... твои существующие данные: pageTitle, totalLikes, books, searchTerm ...
      searchTerm: '',
      selectedSortCriterion: 'title', // из предыдущего задания
      books: [
        // ... твой массив книг ...
      ]
    };
  },
  // 👇 Добавляем секцию computed 👇
  computed: {
    filteredBooks() {
      // `this` ссылается на экземпляр компонента
      const term = this.searchTerm.trim().toLowerCase(); // Приводим поисковый запрос к нижнему регистру и убираем пробелы по краям

      // Если поисковый запрос пуст, возвращаем весь список книг
      if (!term) {
        return this.books;
      }

      // Фильтруем книги
      return this.books.filter(book => {
        // Приводим название книги к нижнему регистру и проверяем, содержит ли оно поисковый запрос
        return book.title.toLowerCase().includes(term);
      });
    }
  },
  methods: {
    // ... твои существующие методы ...
  }
}
</script>
```

Разбор `filteredBooks`:
* Это функция, но в шаблоне мы будем обращаться к ней как к свойству (`filteredBooks`, без скобок).
* `const term = this.searchTerm.trim().toLowerCase();`: Мы получаем текущее значение `searchTerm`, удаляем лишние пробелы по краям (`trim()`) и приводим его к нижнему регистру (`toLowerCase()`) для поиска без учета регистра.
* `if (!term)`: Если после обработки `term` пустой (например, пользователь ничего не ввел или ввел только пробелы), мы возвращаем оригинальный массив `this.books`.
* `this.books.filter(book => ...)`: Если `term` не пустой, мы используем стандартный метод массивов `filter` для создания нового массива, содержащего только те книги, которые удовлетворяют условию.
* `book.title.toLowerCase().includes(term)`: Условие фильтрации. Мы также приводим название книги к нижнему регистру и проверяем, содержит ли оно (`includes()`) наш обработанный поисковый запрос `term`.

### Шаг 3: Используем `filteredBooks` в шаблоне для отображения списка

Теперь в секции `<template>` найди директиву `v-for`, которая отвечает за отображение списка книг. Раньше она выглядела так: `v-for="book in books" ...`.

Измени её так, чтобы она итерировалась по нашему новому вычисляемому свойству `filteredBooks`:

```html
    <div v-for="book in filteredBooks" :key="book.id" class="book-card">
      <img :src="book.coverImageUrl" :alt="'Обложка книги ' + book.title" class="book-cover">
      <h3>{{ book.title }}</h3>
      </div>
```

Всё остальное внутри цикла (`:key`, отображение `book.title`, `book.author` и т.д.) остается без изменений, так как `filteredBooks` возвращает массив объектов с той же структурой, что и `books`.

### Шаг 4: Проверьте результат в браузере!

Сохрани файл `App.vue`.
1.  **Начальное состояние:** Сначала, когда поле поиска пустое, ты должен видеть все книги из твоего массива `books`.
2.  **Ввод текста:** Начни вводить текст в поле поиска (которое связано с `searchTerm`). Список книг на странице должен **динамически обновляться**, показывая только те книги, в названии которых есть введенные символы (без учета регистра).
3.  **Очистка поля:** Если удалить весь текст из поля поиска, снова должен отобразиться полный список книг.

> 🪄 Это и есть магия вычисляемых свойств! Они реактивно пересчитываются, когда их зависимости (`searchTerm` или `books` в данном случае) изменяются, и делают это эффективно благодаря кэшированию.

<br>

## 📝 Итоговый примерный код (ключевые измененные части `App.vue`):

**Секция `<script>` (добавлена опция `computed`):**

<script>
export default {
  name: 'App',
  data() {
    return {
      pageTitle: 'Наш Книжный Каталог',
      totalLikes: 0,
      searchTerm: '',
      selectedSortCriterion: 'title',
      books: [
        { id: 1, title: 'Vue.js: Путь к Мастерству', author: 'Елена Разработчикова', price: 1500, /*...*/ inStock: true, isBestseller: true },
        { id: 2, title: 'JavaScript для Профессионалов', author: 'Иван Кодер', price: 1250, /*...*/ inStock: false, isBestseller: false },
        { id: 3, title: 'HTML и CSS: Современные Техники', author: 'Ольга Верстальщица', price: 990, /*...*/ inStock: true, isBestseller: false }
      ]
    };
  },
  computed: {
    filteredBooks() {
      const term = this.searchTerm.trim().toLowerCase();
      if (!term) {
        return this.books;
      }
      return this.books.filter(book => 
        book.title.toLowerCase().includes(term)
      );
    }
  },
  methods: {
    // ... твои методы showBookDetails, incrementTotalLikes ...
  }
}
</script>

**Секция `<template>` (изменена строка с `v-for`):**

``` html
<template>
  <div id="book-catalog-app">
    <div class="search-bar">
      <label for="searchInput">Поиск: </label>
      <input type="text" id="searchInput" placeholder="Введите название книги..." v-model="searchTerm">
      <p>Текущий поисковый запрос: <strong>"{{ searchTerm }}"</strong></p>
    </div>
    <hr>

    <div class="book-list">
      <div v-for="book in filteredBooks" :key="book.id" class="book-card">
        <img :src="book.coverImageUrl" :alt="'Обложка книги ' + book.title" class="book-cover">
        <h3>{{ book.title }}</h3>
        <p><strong>Автор:</strong> {{ book.author }}</p>
        <p><strong>Цена:</strong> {{ book.price }} руб.</p>
        <p v-if="!book.inStock" class="out-of-stock">Нет в наличии</p>
        <p v-else class="in-stock">В наличии</p>
        <div class="book-actions">
          <button @click="showBookDetails(book)">Подробнее</button>
          <button @click="incrementTotalLikes">👍 Нравится</button>
        </div>
      </div>
    </div>
  </div>
</template>
```
<br>

## 🤔 Что мы сделали:

* Создали **вычисляемое свойство `filteredBooks`**, которое возвращает новый массив книг на основе `this.books` и `this.searchTerm`.
* Использовали это вычисляемое свойство в директиве `v-for` для отображения отфильтрованного списка.
* Убедились, что фильтрация происходит реактивно и без учета регистра.

Вычисляемые свойства — это очень мощный инструмент для работы с производными данными во Vue.

> 😊 В `TASK.MD` мы попробуем еще немного поработать с `computed` и сравним их с `watch`.

---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](./THEORY.md) | [Вперед ➡️](./TASK.md)** 

</div>