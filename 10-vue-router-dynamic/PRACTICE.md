# Тема 10: Практика 💻 Динамические маршруты и детальная страница книги

В теоретической части мы изучили динамические сегменты в маршрутах, как получать параметры маршрута и как использовать навигационные хуки. Теперь мы применим эти знания, чтобы создать полноценную страницу с детальной информацией для каждой книги в нашем каталоге.

**Важно:** Эта практика включает в себя небольшой рефакторинг управления состоянием (массивом `books`), чтобы сделать его доступным для разных страниц. Мы переместим основной массив `books` в `App.vue`.

## 🎯 Цель практики:

1.  **Модифицировать маршрут** для `BookDetailPage.vue` так, чтобы он принимал динамический параметр `:id` книги.
2.  **Настроить навигацию** из `HomePage.vue` (с компонента `BookCard.vue`) на `BookDetailPage.vue` так, чтобы передавался `id` выбранной книги.
3.  В компоненте `BookDetailPage.vue` **получить `id` книги** из параметров маршрута (через props).
4.  **Отобразить на `BookDetailPage.vue` полную информацию** о книге, найденной по этому `id` из общего списка книг (который будет доступен через props от `App.vue`).

## 🛠️ Подготовка:

1.  Убедись, что твой проект, над которым мы работали в **Теме 09** (с базовой маршрутизацией и компонентами-страницами), открыт в VS Code.
2.  Убедись, что сервер разработки (`npm run serve`) запущен.

## Часть 1: Рефакторинг управления данными (массив `books`)

Чтобы `BookDetailPage.vue` мог получить доступ к данным о конкретной книге, нам нужен общий источник этих данных. Перенесем массив `books` в `App.vue`.

### Шаг 1: Перенос массива `books` в `App.vue`

1.  Открой `src/views/HomePage.vue`.
2.  **Скопируй** определение массива `books` из `data()` этого компонента.
3.  **Удали** его из `data()` компонента `HomePage.vue`.
4.  Открой `src/App.vue`.
5.  **Вставь** скопированный массив `books` в `data()` компонента `App.vue`. (Он у нас там уже был как `allBooksForCartPage`, можно его переименовать или использовать). Давай переименуем `allBooksForCartPage` в `books` для единообразия.

    ```vue
    // src/App.vue - <script>
    export default {
      name: 'App',
      data() {
        return {
          cartItems: [],
          totalLikes: 0,
          books: [ // <--- Теперь основной массив книг здесь
            { id: 1, title: 'Vue.js: Путь к Мастерству', /* ... другие поля ... */ },
            { id: 2, title: 'JavaScript для Профессионалов', /* ... */ },
            { id: 3, title: 'HTML и CSS: Современные Техники', /* ... */ }
          ]
        };
      },
      methods: {
        handleBookAddedToCart(bookId) {
          // ... (логика добавления в корзину)
          if (!this.cartItems.includes(bookId)) this.cartItems.push(bookId);
        },
        incrementTotalLikes() { // Этот метод может быть вызван из HomePage через событие
          this.totalLikes++;
        }
      }
    }
    </script>
    ```

### Шаг 2: Передача `books` как prop в `HomePage.vue` и другие страницы

1.  В `src/App.vue`, в секции `<template>`, модифицируй `<router-view>`, чтобы он передавал массив `books` в качестве пропа компонентам страниц:
    ```vue
    <router-view 
      :books="books" :cartItemsIds="cartItems" @update-cart="handleBookAddedToCart"
      @update-total-likes="incrementTotalLikes" 
    />
    ```
2.  В `src/views/HomePage.vue`, в секции `<script>`, объяви `books` как prop и удали его из локального `data()` (мы это сделали на Шаге 1, но убедись). `filteredBooks` теперь будет работать с `this.books` (который теперь prop).
    ```vue
    // src/views/HomePage.vue - <script>
    export default {
      name: 'HomePage',
      components: { BookCard },
      props: {
        books: { // <--- Получаем массив книг как prop
          type: Array,
          required: true
        },
        // initialTotalLikes и onAddToCart были примерами проброса,
        // Теперь App.vue напрямую слушает @update-cart и @update-total-likes от router-view,
        // а HomePage должен будет $emit эти события, если хочет обновить состояние в App.vue.
        // Или HomePage сам управляет totalLikes (если он только для HomePage)
        // Для addToCart: BookCard -> HomePage -> App.vue (через $emit)
      },
      data() {
        return {
          pageTitle: 'Наш Книжный Каталог',
          searchTerm: '',
          selectedSortCriterion: 'title',
          // books: [] // <--- Удалено из data
          // totalLikes: 0, // Если управляется из App.vue, получаем через prop или локально
        };
      },
      // ... computed (filteredBooks будет использовать this.books из props), methods ...
      methods: {
          handleBookAddedToCartEvent(bookId) {
              this.$emit('update-cart', bookId); // Генерируем событие для App.vue
          },
          handleBookViewDetailsEvent(bookId) { // Этот метод мы создали в прошлом TASK.md
              console.log(`(HomePage) Переход на детали для книги с ID: ${bookId}.`);
              this.$router.push({ name: 'BookDetail', params: { id: bookId } });
          }
          // ... другие методы, если есть
      }
    }
    </script>
    ```
    * В `BookCard` (внутри `HomePage`), при клике на "В корзину", `BookCard` генерирует событие `@add-to-cart`, которое слушает `HomePage`. `HomePage` в своем методе `handleBookAddedToCartEvent` теперь должен сгенерировать событие `@update-cart` для `App.vue`.
    * В `HomePage.vue` измени `v-for` для `BookCard`:
        ```html
        <BookCard
            v-for="bookItem in filteredBooks" 
            :key="bookItem.id"
            :book="bookItem"
            @add-to-cart="handleBookAddedToCartEvent" 
            @view-details="handleBookViewDetailsEvent" > ... </BookCard>
        ```

### Шаг 3: Настройка `props: true` для маршрута `BookDetail`

1.  Открой `src/router/index.js`.
2.  Убедись, что для маршрута `BookDetail` установлена опция `props: true`. Это позволит передавать параметры маршрута (наш `:id`) как props в компонент `BookDetailPage.vue`.
    ```javascript
    // src/router/index.js
    // ...
    {
      path: '/book/:id',
      name: 'BookDetail',
      component: BookDetailPage,
      props: true // <--- Убедись, что это здесь
    },
    // ...
    ```

## Часть 2: Навигация с ID и отображение деталей книги

### Шаг 4: Модификация навигации в `HomePage.vue` (метод `handleBookViewDetailsEvent`)

В `src/views/HomePage.vue`, в методе, который обрабатывает событие `view-details` от `BookCard.vue` (мы назвали его `handleBookViewDetailsEvent` на Шаге 2), измени вызов `this.$router.push()` так, чтобы он передавал `id` книги в качестве параметра маршрута:

```javascript
// src/views/HomePage.vue - methods
// ...
handleBookViewDetailsEvent(bookId) {
  console.log(`(HomePage) Навигация на детали для книги с ID: ${bookId}`);
  this.$router.push({ name: 'BookDetail', params: { id: bookId } });
}
// ...
```

* Компонент `BookCard.vue` уже должен генерировать событие `view-details` с `book.id` (из предыдущего TASK.md).

### Шаг 5: Отображение `id` и информации о книге в `BookDetailPage.vue`

1. Открой `src/views/BookDetailPage.vue`.
2. **Объяви `props`:**
  * `id`: будет получен из параметра URL благодаря `props: true` в маршруте.
  * `books`: массив всех книг, переданный из `App.vue` через `<router-view>`.

  ```vue
  // src/views/BookDetailPage.vue - <script>
  export default {
    name: 'BookDetailPage',
    props: {
      id: { // Параметр из URL
        type: [String, Number], // ID может быть строкой или числом
        required: true
      },
      books: { // Массив всех книг от App.vue
        type: Array,
        required: true
      }
    },
    computed: {
      selectedBook() {
        // ID из URL приходит как строка, ID в объектах книг могут быть числами
        // Поэтому приводим this.id к тому же типу, что и book.id для корректного сравнения
        const numericId = parseInt(this.id); // или String(book.id) === this.id
        return this.books.find(book => book.id === numericId);
      }
    },
    // Если нужно реагировать на изменение ID (например, ссылки "следующая/предыдущая книга" на этой же странице)
    // watch: {
    //   id(newId) {
    //     // Логика перезагрузки/поиска книги, если она не обрабатывается через computed
    //     console.log('ID книги изменился на:', newId);
    //   }
    // }
  }
  </script>
  ```

3. **Обнови `<template>`** для отображения информации о `selectedBook`:

```vue
// src/views/BookDetailPage.vue - <template>
<template>
  <div class="book-detail-page">
    <div v-if="selectedBook">
      <h1>{{ selectedBook.title }}</h1>
      <img 
        v-if="selectedBook.coverImageUrl" 
        :src="selectedBook.coverImageUrl" 
        :alt="'Обложка ' + selectedBook.title" 
        class="book-detail-cover"
      >
      <p><strong>Автор:</strong> {{ selectedBook.author }}</p>
      <p><strong>Цена:</strong> {{ selectedBook.price }} руб.</p>
      <p><strong>В наличии:</strong> {{ selectedBook.inStock ? 'Да' : 'Нет' }}</p>
      <p v-if="selectedBook.isBestseller" style="color: orange; font-weight: bold;">🔥 Бестселлер!</p>
      <p><strong>Описание:</strong> {{ selectedBook.description || 'Описание отсутствует.' }}</p>
    </div>
    <div v-else>
      <h1>Книга не найдена</h1>
      <p>Извините, книга с ID "{{ id }}" не найдена в нашем каталоге.</p>
    </div>
    <router-link :to="{ name: 'Home' }">Назад в каталог</router-link>
  </div>
</template>
```

4. **(Опционально) Добавь стили для `BookDetailPage.vue`:**

```vue
// src/views/BookDetailPage.vue - <style scoped>
<style scoped>
.book-detail-page {
  padding: 20px;
  max-width: 700px;
  margin: 0 auto;
  text-align: center;
}
.book-detail-cover {
  max-width: 200px;
  height: auto;
  margin: 20px auto;
  border: 1px solid #eee;
  box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
}
.book-detail-page h1 {
  margin-bottom: 20px;
}
.book-detail-page p {
  margin: 10px 0;
  line-height: 1.6;
}
</style>
```

### Шаг 6: Проверка динамической навигации и отображения

1. Сохрани все измененные файлы (`App.vue`, `HomePage.vue`, `BookDetailPage.vue`, `router/index.js`).
2. **Проверь работу:**
  * На `HomePage.vue` кликни на кнопку "Подробнее" (или на всю карточку, если реализовано) у разных книг.
  * Убедись, что URL в браузере изменяется на `/book/1`, `/book/2` и т.д., в зависимости от `id` выбранной книги.
  * Убедись, что на странице `BookDetailPage.vue` отображается ID и корректная информация о выбранной книге.
  * Попробуй вручную ввести в адресную строку URL с ID несуществующей книги (например, `/book/999`) и посмотри, как отобразится сообщение "Книга не найдена".
  * Проверь, что ссылка "Назад в каталог" на `BookDetailPage.vue` работает.

## 📝 Итоговый код (ключевые моменты):

* `src/router/index.js`: Маршрут для `BookDetail` должен быть `/book/:id` и иметь `props: true`.
* `src/App.vue`: `data()` содержит массив `books`. `<router-view>` передает `:books="books"` (и другие необходимые props/слушатели) отображаемым компонентам.
* `src/views/HomePage.vue`: Принимает `books` как prop. Метод `handleBookViewDetailsEvent` использует `this.$router.push({ name: 'BookDetail', params: { id: bookId } })`.
* `src/components/BookCard.vue`: Генерирует событие `@view-details` с `book.id`.
* `src/views/BookDetailPage.vue`: Принимает `id` и `books` как props. Имеет вычисляемое свойство `selectedBook` для поиска и отображения данных книги.

## 🤔 Что мы сделали:

* Сделали маршрут `/book/:id` **динамическим**, чтобы он мог принимать `id` книги.
* Настроили **передачу `id` книги** при навигации с `HomePage.vue` на `BookDetailPage.vue`.
* Научили `BookDetailPage.vue` **получать** `id` из URL (через `props: true` в маршруте) и **отображать информацию о соответствующей книге**, используя общий массив `books`, переданный из `App.vue`.
* Реорганизовали хранение основного массива `books`, переместив его в `App.vue` для обеспечения доступа к нему из разных компонентов-страниц.

Теперь у тебя есть полноценная навигация на страницы с детальной информацией! В `TASK.MD` ты сможешь попрактиковаться с навигационными хуками или другими аспектами Vue Router.