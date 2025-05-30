# Тема 04: Практика 💻 Обработка кликов и счетчик "Лайков"

В теоретической части мы изучили, как использовать директиву `v-on` (или `@`) для прослушивания DOM-событий, как вызывать методы-обработчики и использовать модификаторы. Теперь давай оживим наш каталог книг, добавив реакцию на клики!

## 🎯 Цель практики:

1.  Для каждой книги в списке добавить кнопку "Подробнее".
2.  При клике на кнопку "Подробнее" для конкретной книги, выводить в консоль браузера её `id` или `title`.
3.  Создать общий счетчик "Понравилось" на странице.
4.  Для каждой книги добавить кнопку "👍 Нравится", клик по которой будет увеличивать общий счетчик "Понравилось".

## 🛠️ Подготовка:

1.  Убедись, что твой проект (например, `my-bookstore-app`), над которым мы работали в **Теме 03** (со списком книг и условным отображением наличия), открыт в VS Code.
2.  Запусти сервер для разработки, если он еще не запущен:
    ```bash
    npm run serve
    ```
3.  Открой в браузере страницу твоего приложения (обычно `http://localhost:8080/`) и консоль разработчика (обычно F12, вкладка "Console").

## 🚀 Шаги выполнения:

### Шаг 1: Добавляем кнопку "Подробнее" к каждой книге

В файле `src/App.vue`, найди цикл `v-for` в секции `<template>`, который отображает карточки книг (`<div class="book-card">`). Внутри каждой карточки, после информации о книге, добавь кнопку:

```html
      <p v-if="!book.inStock" class="out-of-stock">Нет в наличии</p>
      <p v-else class="in-stock">В наличии</p>
      
      <button>Подробнее</button>
    </div>
```

Сохрани файл. Кнопка "Подробнее" должна появиться на каждой карточке книги. Пока она ничего не делает.

### Шаг 2: Создаем метод для отображения информации о книге

Теперь в секции `<script>` файла `App.vue` нам нужно добавить объект `methods` и в нем определить метод, который будет вызываться при клике.

```vue
<script>
export default {
  name: 'App',
  data() {
    return {
      pageTitle: 'Наш Книжный Каталог',
      books: [
        // ... твои книги из предыдущей практики ...
      ]
      // totalLikes: 0 // Скоро добавим
    };
  },
  // 👇 Добавляем секцию methods 👇
  methods: {
    showBookDetails(book) {
      // `book` - это объект книги, который мы передадим при клике
      console.log('Выбрана книга:');
      console.log('ID:', book.id);
      console.log('Название:', book.title);
      console.log('Автор:', book.author);
      // Ты можешь вывести весь объект: console.log(book);
    }
    // incrementTotalLikes() // Скоро добавим
  }
}
</script>
```

**Разбор:**
* Мы добавили новую опцию `methods` в наш `export default`. Она является "соседом" для `data()`.
* Внутри `methods` мы определили функцию `showBookDetails(book)`. Эта функция ожидает получить объект `book` в качестве аргумента и выводит его `id` и `title` в консоль.

### Шаг 3: Привязываем событие `@click` к кнопке "Подробнее"

Вернемся в `<template>` и изменим нашу кнопку "Подробнее", чтобы она вызывала метод `showBookDetails` при клике, передавая ему текущую книгу из цикла `v-for`:

```html
  <button @click="showBookDetails(book)">Подробнее</button>
</div>
```

**Разбор:**
* `@click="showBookDetails(book)"`: Мы говорим Vue: "При событии `click` на этой кнопке, вызови метод `showBookDetails` из нашего компонента и передай ему в качестве аргумента текущий объект `book` из итерации `v-for`."

### Шаг 4: Проверяем вывод в консоль

Сохрани файл `App.vue`. Теперь открой консоль разработчика в браузере.
Кликни на кнопку "Подробнее" у разных книг. В консоли должны появляться сообщения с информацией о той книге, на кнопку которой ты нажал.

*(Скриншот консоли браузера с выводом информации о книге после клика)*

### Шаг 5: Добавляем данные для счетчика "Понравилось"

В `data()` нашего компонента добавим новое свойство для хранения общего количества "лайков":

```js
// ... внутри data()
return {
  pageTitle: 'Наш Книжный Каталог',
  books: [
    // ... массив книг ...
  ],
  totalLikes: 0 // <--- Наш новый счетчик
};
// ...
```

Теперь отобразим этот счетчик где-нибудь на странице, например, под основным заголовком `pageTitle`:

```html
<template>
  <div id="book-catalog-app">
    <h1>{{ pageTitle }}</h1>
    <p>❤️ Всего понравилось: {{ totalLikes }}</p> <div class="book-list">
```

Сохрани и проверь – на странице должен появиться текст "❤️ Всего понравилось: 0".

### Шаг 6: Добавляем кнопку "Нравится" и метод для увеличения счетчика

1.  **Добавь кнопку "Нравится"** в каждую карточку книги, например, рядом с кнопкой "Подробнее":
    ```html
          <button @click="showBookDetails(book)">Подробнее</button>
          <button>👍 Нравится</button> </div>
    ```

2.  **Создай метод `incrementTotalLikes`** в объекте `methods`:
    ```javascript
    // ... в methods:
    methods: {
      showBookDetails(book) {
        // ... код метода ...
      },
      incrementTotalLikes() {
        this.totalLikes++; // Увеличиваем значение totalLikes на 1
        // `this` ссылается на экземпляр компонента,
        // поэтому мы можем получить доступ к `this.totalLikes`
        console.log('Общий счетчик лайков:', this.totalLikes); // Для отладки
      }
    }
    // ...
    ```

### Шаг 7: Привязываем событие `@click` к кнопке "Нравится"

Теперь свяжем клик по новой кнопке с нашим методом `incrementTotalLikes`:

```html
      <button @click="showBookDetails(book)">Подробнее</button>
      <button @click="incrementTotalLikes">👍 Нравится</button>
    </div>
```

Обрати внимание, что здесь мы не передаем аргументы в `incrementTotalLikes`, так как метод просто увеличивает общий счетчик.

### Шаг 8: Проверяем работу счетчика

Сохрани файл `App.vue`.
Кликай на кнопки "👍 Нравится" у разных книг. Значение "❤️ Всего понравилось: X" на странице должно увеличиваться с каждым кликом. Также в консоли ты будешь видеть текущее значение счетчика.

## 📝 Итоговый примерный код App.vue:

```vue
<template>
  <div id="book-catalog-app">
    <h1>{{ pageTitle }}</h1>
    <p>❤️ Всего понравилось: {{ totalLikes }}</p>
    <div class="book-list">
      <div v-for="book in books" :key="book.id" class="book-card">
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
      ]
    };
  },
  methods: {
    showBookDetails(book) {
      console.log(`--- Детализация книги ID: ${book.id} ---`);
      console.log('Название:', book.title);
      console.log('Автор:', book.author);
      console.log('Цена:', book.price);
      console.log('В наличии:', book.inStock ? 'Да' : 'Нет');
      console.log('Бестселлер:', book.isBestseller ? 'Да' : 'Нет');
    },
    incrementTotalLikes() {
      this.totalLikes++;
      console.log('Общий счетчик лайков:', this.totalLikes);
    }
  }
}
</script>

<style>
/* Стили из предыдущей практики можно оставить или адаптировать */
#book-catalog-app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
  padding: 20px;
}
.book-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
}
.book-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  width: 220px; /* Немного увеличил для кнопок */
  box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
}
.book-cover {
  width: 100px;
  height: 150px;
  object-fit: cover;
  margin-bottom: 10px;
  border: 1px solid #eee;
}
h1 { color: #42b983; }
h3 { margin: 10px 0 5px; font-size: 1.1em; line-height: 1.2; min-height: 2.4em; }
p { margin: 5px 0; font-size: 0.9em; }
.out-of-stock { color: red; font-weight: bold; }
.in-stock { color: green; }

.book-actions { /* Контейнер для кнопок */
  margin-top: 10px;
  display: flex;
  gap: 10px; /* Пробел между кнопками */
}

.book-actions button {
  padding: 8px 12px;
  border: 1px solid #ccc;
  background-color: #f0f0f0;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9em;
}
.book-actions button:hover {
  background-color: #e0e0e0;
}
</style>
```

## 🤔 Что мы сделали:
* Добавили кнопки к каждому элементу списка, созданному с помощью `v-for`.
* Научились вызывать методы компонента при клике на кнопку, используя `@click`.
* Передавали данные (объект `book`) из цикла `v-for` в метод-обработчик.
* Создали и обновляли свойство в `data()` (счетчик `totalLikes`) из метода-обработчика, и увидели, как это изменение реактивно отображается в шаблоне.

Это важные строительные блоки для создания интерактивных приложений! В `TASK.MD` тебя ждут задания для самостоятельного закрепления и экспериментов с модификаторами.