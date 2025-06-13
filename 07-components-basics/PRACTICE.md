# Тема 07: Практика 💻 Создаем компонент `BookCard`
> <p align=center> 🛠️ Практика </p>

В теоретической части мы узнали, что такое компоненты, как их создавать, регистрировать и как передавать в них данные с помощью входных параметров (props). Пришло время применить эти знания и отрефакторить наш каталог книг, вынеся отображение одной книги в отдельный компонент `BookCard.vue`.

<br>

## 🎯 Цель практики:

1.  Создать новый однофайловый компонент `BookCard.vue`.
2.  Определить в `BookCard.vue` входные параметры (props) для приема данных о книге (например, объект `book` или отдельные его свойства).
3.  Перенести HTML-разметку (и стили, если применимо) для отображения одной книги из `App.vue` в шаблон компонента `BookCard.vue`.
4.  Зарегистрировать компонент `BookCard.vue` локально в `App.vue`.
5.  Использовать компонент `<BookCard>` в `App.vue` внутри цикла `v-for` для отображения списка книг, передавая данные о каждой книге через props.

<br>

## 🛠️ Подготовка:

1.  Убедись, что твой проект (например, `my-bookstore-app`), над которым мы работали в **Теме 06** (с фильтрацией списка книг), открыт в VS Code.
2.  Запусти сервер для разработки, если он еще не запущен:
    ```bash
    npm run serve
    ```
3.  Открой в браузере страницу твоего приложения (обычно `http://localhost:8080/`).

<br>

## 🚀 Шаги выполнения:

### Шаг 1: Создание файла компонента `BookCard.vue`

1.  Внутри папки `src` твоего проекта создай новую папку с именем `components`. Это стандартное место для хранения переиспользуемых компонентов.
    *(Если такой папки еще нет).*
2.  Внутри папки `src/components` создай новый файл с именем `BookCard.vue`.
3.  Добавь в `BookCard.vue` базовую структуру однофайлового компонента:

    ```html
    // src/components/BookCard.vue
    <template>
      <div>
        </div>
    </template>

    <script>
    export default {
      name: 'BookCard'
      // props будут определены здесь
    }
    </script>

    <style scoped>
    /* Стили для этого компонента будут здесь */
    /* `scoped` гарантирует, что стили не повлияют на другие компоненты */
    </style>
    ```

### Шаг 2: Определение `props` в `BookCard.vue`

Наш компонент `BookCard` должен получать информацию о книге от родительского компонента (`App.vue`). Для этого мы определим `props`. Самый простой способ передать всю информацию о книге — это передать целиком объект книги.

Добавь опцию `props` в секцию `<script>` компонента `BookCard.vue`:

```js
// src/components/BookCard.vue
<script>
export default {
  name: 'BookCard',
  props: {
    book: { // Мы ожидаем получить объект с именем 'book'
      type: Object, // Указываем, что это должен быть объект
      required: true // Делаем этот prop обязательным
    }
  }
  // В будущем здесь могут быть свои data, methods, computed для BookCard
}
</script>
```

### Шаг 3: Перенос разметки одной книги в `BookCard.vue`

1. Открой файл `src/App.vue`.
<br>
2. Найди в его секции `<template>` блок кода внутри `v-for`, который отвечает за отображение одной книги (вероятно, это `div` с классом `book-card`).
<br>
3. **Скопируй** эту разметку (содержимое одного `book-card`, без самого `v-for`) и вставь её в секцию `<template>` файла `BookCard.vue`.
<br>
4. Теперь **адаптируй** эту разметку в `BookCard.vue` так, чтобы она использовала данные из пропа `book` (который мы определили на Шаге 2). Вместо `book_item.title` (или как у тебя называлась переменная цикла) теперь будет `book.title`, вместо `book_item.author` -> `book.author`, и так далее.

```html
// src/components/BookCard.vue - <template>
<template>
  <div class="book-card"> <img :src="book.coverImageUrl" :alt="'Обложка книги ' + book.title" class="book-cover">
    <h3>{{ book.title }} <span v-if="book.isBestseller" class="bestseller-badge">🔥 Бестселлер!</span></h3>
    <p><strong>Автор:</strong> {{ book.author }}</p>
    <p><strong>Цена:</strong> {{ book.price }} руб.</p>
    <p v-if="!book.inStock" class="out-of-stock">Нет в наличии</p>
    <p v-else class="in-stock">В наличии</p>

    <div class="book-actions">
      <button @click="handleDetailsClick">Подробнее</button>
      <button @click="handleLikeClick">👍 Нравится</button>
      <button @click="handleFavoriteClick">❤️ Избранное</button>
    </div>
  </div>
</template>
```

**Примечание по кнопкам:** В `BookCard.vue` мы пока не можем напрямую вызывать методы `showBookDetails` и `incrementTotalLikes` из `App.vue`. Для этого нам понадобятся пользовательские события (тема 08). Пока что можно создать "заглушки" для этих методов в `BookCard.vue` или просто оставить кнопки без функционала (или с простым `alert`).

Для этой практики давайте создадим простые методы-заглушки в `BookCard.vue`, чтобы не было ошибок:

```js
// src/components/BookCard.vue - <script>
// ...
// export default {
//   props: { book: { type: Object, required: true }},
    methods: {
      handleDetailsClick() {
        console.log('Клик "Подробнее" на компоненте BookCard для:', this.book.title);
        alert('Функционал "Подробнее" будет реализован через события компонента.');
      },
      handleLikeClick() {
        console.log('Клик "Нравится" на компоненте BookCard для:', this.book.title);
        alert('Функционал "Нравится" будет реализован через события компонента.');
      },
      handleFavoriteClick() {
        alert('Книга "' + this.book.title + '" (из компонента) добавлена в избранное!');
      }
    }
// }
// ...
```

### Шаг 4: Перенос стилей (рекомендуется)

Чтобы компонент `BookCard` был действительно самодостаточным, перенеси стили, относящиеся к `.book-card` и его внутренним элементам (например, `.book-cover`, `.out-of-stock`, `.bestseller-badge`, `.book-actions button`), из `<style>` файла `App.vue` в секцию `<style scoped>` файла `BookCard.vue`.

Если в `App.vue` у тебя были общие стили для всех карточек, теперь они будут инкапсулированы в `BookCard.vue`. Убедись, что в `App.vue` не осталось стилей, специфичных только для одной карточки.

```vue
// src/components/BookCard.vue - <style scoped>
<style scoped>
.book-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  width: 220px;
  box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: white; /* Добавим фон, чтобы карточки выделялись */
}
.book-cover {
  width: 100px;
  height: 150px;
  object-fit: cover;
  margin-bottom: 10px;
  border: 1px solid #eee;
}
h3 {
  margin: 10px 0 5px;
  font-size: 1.1em;
  line-height: 1.2;
  min-height: 2.4em; /* Для выравнивания высоты заголовков */
  text-align: center;
}
p {
  margin: 5px 0;
  font-size: 0.9em;
  text-align: center;
}
.out-of-stock { color: red; font-weight: bold; }
.in-stock { color: green; }
.bestseller-badge {
  background-color: #ffc107;
  color: #333;
  padding: 2px 6px;
  border-radius: 4px;
  font-size: 0.75em;
  font-weight: bold;
  margin-left: 8px;
  vertical-align: middle;
}
.book-actions {
  margin-top: 10px;
  display: flex;
  gap: 10px;
}
.book-actions button {
  padding: 8px 12px;
  border: 1px solid #ccc;
  background-color: #f0f0f0;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.85em; /* Немного уменьшим шрифт кнопок */
}
.book-actions button:hover {
  background-color: #e0e0e0;
}
</style>
```

### Шаг 5: Регистрация и использование `BookCard` в `App.vue`

Теперь вернемся в `src/App.vue`.

1. **Импортируй компонент `BookCard` в секции `<script>`:**

```vue
// src/App.vue - <script>
import BookCard from './components/BookCard.vue'; // Убедись, что путь правильный

export default {
  name: 'App',
  // 👇 Добавляем опцию components для локальной регистрации 👇
  components: {
    BookCard // Теперь <BookCard> (или <book-card>) можно использовать в шаблоне App.vue
  },
  data() {
    // ... твои данные ...
  },
  computed: {
    // ... твои вычисляемые свойства (filteredBooks) ...
  },
  methods: {
    // Методы showBookDetails, incrementTotalLikes и др. ПОКА ОСТАЮТСЯ ЗДЕСЬ.
    // Мы НЕ будем их вызывать напрямую из BookCard в этой практике.
    // Это будет темой следующего занятия (пользовательские события).
  }
  // ... watch ...
}
</script>
```

2. **Измени цикл `v-for` в `<template>` файла `App.vue`:**

Удали старую разметку для отображения книги внутри цикла (ты её уже перенес в `BookCard.vue`). Вместо этого используй свой новый компонент `<BookCard>`. Не забудь передать объект `book` в качестве пропа и сохранить `:key`.

```vue
<div class="book-list">
  <BookCard
    v-for="currentBook in filteredBooks"
    :key="currentBook.id"
    :book="currentBook"
  />
</div>
```

### Шаг 6: Проверьте результат в браузере

Сохрани оба файла (`App.vue` и `BookCard.vue`).

* Открой приложение в браузере. Визуально страница должна выглядеть так же, как и до рефакторинга (или очень похоже, если ты немного изменил стили).
* Список книг должен по-прежнему фильтроваться при вводе в поле поиска.
* **Функциональность кнопок "Подробнее" и "Нравится":** Если ты создал методы-заглушки в `BookCard.vue`, то при клике на эти кнопки ты увидишь `alert` или сообщения в консоли от `BookCard.vue`. Глобальный счетчик лайков в `App.vue` обновляться **не будет**, и детализация в консоли от `App.vue` тоже. Это нормально для данного этапа! Мы исправим это в следующей теме, изучив пользовательские события. Кнопка "❤️ Избранное" с простым `alert` из `BookCard` должна работать.

<br>

## 📝 Итоговый код (основные части):

**`src/components/BookCard.vue` (полностью):**

```html
<template>
  <div class="book-card">
    <img :src="book.coverImageUrl" :alt="'Обложка книги ' + book.title" class="book-cover">
    <h3>{{ book.title }} <span v-if="book.isBestseller" class="bestseller-badge">🔥 Бестселлер!</span></h3>
    <p><strong>Автор:</strong> {{ book.author }}</p>
    <p><strong>Цена:</strong> {{ book.price }} руб.</p>
    <p v-if="!book.inStock" class="out-of-stock">Нет в наличии</p>
    <p v-else class="in-stock">В наличии</p>
    <div class="book-actions">
      <button @click="handleDetailsClick">Подробнее</button>
      <button @click="handleLikeClick">👍 Нравится</button>
      <button @click="handleFavoriteClick">❤️ Избранное</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BookCard',
  props: {
    book: {
      type: Object,
      required: true
    }
  },
  methods: {
    handleDetailsClick() {
      console.log('(BookCard) Клик "Подробнее" для:', this.book.title);
      alert('Детализация для "' + this.book.title + '" будет доступна позже.');
    },
    handleLikeClick() {
      console.log('(BookCard) Клик "Нравится" для:', this.book.title);
      alert('Функция "Нравится" для "' + this.book.title + '" будет обработана в App.vue.');
    },
    handleFavoriteClick() {
      alert('Книга "' + this.book.title + '" (из компонента BookCard) добавлена в избранное!');
    }
  }
}
</script>

<style scoped>
.book-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  width: 220px;
  box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: white;
}
.book-cover {
  width: 100px;
  height: 150px;
  object-fit: cover;
  margin-bottom: 10px;
  border: 1px solid #eee;
}
h3 { margin: 10px 0 5px; font-size: 1.1em; line-height: 1.2; min-height: 2.4em; text-align: center; }
p { margin: 5px 0; font-size: 0.9em; text-align: center; }
.out-of-stock { color: red; font-weight: bold; }
.in-stock { color: green; }
.bestseller-badge { background-color: #ffc107; color: #333; padding: 2px 6px; border-radius: 4px; font-size: 0.75em; font-weight: bold; margin-left: 8px; vertical-align: middle; }
.book-actions { margin-top: 10px; display: flex; gap: 10px; }
.book-actions button { padding: 8px 10px; border: 1px solid #ccc; background-color: #f0f0f0; border-radius: 4px; cursor: pointer; font-size: 0.8em; }
.book-actions button:hover { background-color: #e0e0e0; }
</style>
```

**`src/App.vue` (ключевые изменения):**

```html
<template>
  <div id="book-catalog-app">
    <h1>{{ pageTitle }}</h1>
    <p>❤️ Всего понравилось: {{ totalLikes }}</p> <div class="search-bar">
      <label for="searchInput">Поиск: </label>
      <input type="text" id="searchInput" placeholder="Введите название книги..." v-model="searchTerm">
      <p>Текущий поисковый запрос: <strong>"{{ searchTerm }}"</strong> (Найдено: {{ foundBooksCount }})</p>
    </div>
    
    <div class="sort-criteria-selector" style="margin-bottom: 20px;">
      <label for="sortSelect">Сортировать по: </label>
      <select id="sortSelect" v-model="selectedSortCriterion">
        <option value="title">Названию</option>
        <option value="author">Автору</option>
        <option value="priceAsc">Цене (сначала дешевые)</option>
        <option value="priceDesc">Цене (сначала дорогие)</option>
      </select>
      <p>Текущий критерий: <strong>{{ selectedSortCriterion }}</strong></p>
    </div>
    <hr>

    <div class="book-list">
      <BookCard
        v-for="bookItem in filteredBooks" 
        :key="bookItem.id"
        :book="bookItem"
      />
      <p v-if="searchTerm && !filteredBooks.length" class="no-results">
        По вашему запросу "{{ searchTerm }}" ничего не найдено.
      </p>
    </div>
  </div>
</template>

<script>
import BookCard from './components/BookCard.vue'; // 1. Импорт

export default {
  name: 'App',
  components: { // 2. Локальная регистрация
    BookCard
  },
  data() {
    return {
      pageTitle: 'Наш Книжный Каталог',
      totalLikes: 0, // Этот счетчик пока не будет обновляться от BookCard
      searchTerm: '',
      selectedSortCriterion: 'title',
      books: [
        { id: 1, title: 'Vue.js: Путь к Мастерству', author: 'Елена Разработчикова', price: 1500, coverImageUrl: '[https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial](https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial)', inStock: true, isBestseller: true },
        { id: 2, title: 'JavaScript для Профессионалов', author: 'Иван Кодер', price: 1250, coverImageUrl: '[https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial](https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial)', inStock: false, isBestseller: false },
        { id: 3, title: 'HTML и CSS: Современные Техники', author: 'Ольга Верстальщица', price: 990, coverImageUrl: '[https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial](https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial)', inStock: true, isBestseller: false }
      ]
    };
  },
  computed: {
    filteredBooks() {
      // ... (код из предыдущей практики) ...
      const term = this.searchTerm.trim().toLowerCase();
      if (!term) return this.books;
      return this.books.filter(book => book.title.toLowerCase().includes(term));
    },
    totalBooksInCatalog() { return this.books.length; },
    foundBooksCount() { return this.filteredBooks.length; }
  },
  methods: {
    // Эти методы теперь НЕ вызываются напрямую из BookCard
    // showBookDetails(book) { /* ... */ },
    // incrementTotalLikes() { /* ... */ }
  },
  watch: {
    searchTerm(newValue) {
      console.log('Пользователь ищет (watch в App.vue):', newValue);
    }
  }
}
</script>

<style>
/* Общие стили для App.vue можно оставить здесь */
/* Стили для .book-card и его содержимого теперь в BookCard.vue */
#book-catalog-app { /* ... */ }
.book-list { /* ... */ }
/* ... другие общие стили ... */
.search-bar, .sort-criteria-selector { margin-bottom: 15px; padding: 10px; background-color: #f9f9f9; border-radius: 5px; }
.search-bar input[type="text"], .sort-criteria-selector select { padding: 8px; margin-left: 5px; border: 1px solid #ccc; border-radius: 4px; }
.search-bar p, .sort-criteria-selector p { margin-top: 5px; font-size: 0.9em; color: #555; }
.no-results {
  width: 100%;
  text-align: center;
  font-style: italic;
  color: #777;
  padding: 20px;
}
hr { margin: 20px 0; }
</style>
```

<br>

## 🤔 Что мы сделали:

* Создали отдельный, переиспользуемый компонент `BookCard.vue`.
* Определили в нем `props` для получения данных о книге.
* Перенесли разметку и стили для одной книги в этот компонент, сделав его самодостаточным.
* Зарегистрировали `BookCard` в `App.vue` и использовали его в цикле `v-for`, передавая данные через props.
* Временно "отключили" или сделали заглушками методы кнопок внутри `BookCard`, так как для их полноценной работы с `App.vue` потребуется механизм событий.

Это огромный шаг к созданию более структурированных и поддерживаемых Vue-приложений! Ты научился декомпозировать интерфейс на компоненты.

> В `TASK.MD` ты сможешь попрактиковаться в передаче props и, возможно, создать еще один простой компонент.

---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](./THEORY.md) | [Вперед ➡️](./TASK.md)** 

</div>