# Тема 12: Практика 💻 Загрузка списка книг с сервера

В теоретической части мы разобрали, как отправлять HTTP-запросы, обрабатывать асинхронные операции и управлять состояниями загрузки/ошибки. Теперь давай применим это на практике: мы перестанем использовать "зашитый" в коде массив книг и будем загружать его с публичного API.

Для этого мы будем использовать **[JSONPlaceholder](https://jsonplaceholder.typicode.com/)** — бесплатный фейковый REST API, который отлично подходит для тестирования. Мы будем использовать его эндпоинт `/posts` и представлять каждый "пост" как "книгу".

## 🎯 Цель практики:

1.  Модифицировать хранилище `productStore.js` так, чтобы оно загружало список книг с API, а не хранило его статически.
2.  Добавить в `productStore.js` состояния для отслеживания загрузки (`isLoading`) и ошибок (`error`).
3.  Создать действие (`action`) `fetchBooks` для выполнения асинхронного запроса.
4.  В компоненте `HomePage.vue` вызывать это действие для загрузки данных.
5.  Отобразить в `HomePage.vue` индикатор загрузки и сообщение об ошибке в зависимости от состояния хранилища.

## 🛠️ Подготовка:

1.  Убедись, что твой проект, над которым мы работали в **Теме 11** (с настроенной Pinia), открыт в VS Code.
2.  Установи библиотеку **Axios** для выполнения HTTP-запросов:
    ```bash
    npm install axios
    ```
3.  Запусти сервер для разработки (`npm run serve`).

## 🚀 Шаги выполнения:

### Часть 1: Модификация `productStore` для загрузки данных

Вся логика, связанная с получением и хранением данных о продуктах (книгах), должна быть инкапсулирована в соответствующем хранилище.

#### Шаг 1: Добавление состояний `isLoading` и `error`

Открой файл `src/stores/productStore.js` и измени его `state`:
* Инициализируй `books` как пустой массив.
* Добавь свойства `isLoading` и `error`.

```javascript
// src/stores/productStore.js
import { defineStore } from 'pinia';

export const useProductStore = defineStore('products', {
  state: () => ({
    books: [], // <-- Теперь это пустой массив по умолчанию
    isLoading: false, // <-- Флаг загрузки
    error: null, // <-- Для хранения ошибок
  }),
  getters: {
    // ... геттер getBookById остается без изменений ...
    getBookById: (state) => (id) => {
      // Приводим ID к одному типу для корректного сравнения
      return state.books.find(book => book.id === Number(id));
    }
  },
  // Actions будут добавлены на следующем шаге
});
```

#### Шаг 2: Создание действия (action) `fetchBooks`

Теперь добавим асинхронное действие `fetchBooks` в `productStore.js` для загрузки данных с API.

```js
// src/stores/productStore.js
import { defineStore } from 'pinia';
import axios from 'axios'; // Импортируем axios

export const useProductStore = defineStore('products', {
  state: () => ({
    books: [],
    isLoading: false,
    error: null,
  }),
  getters: {
    // ... getBookById ...
  },
  actions: {
    // 👇 Наше новое асинхронное действие 👇
    async fetchBooks() {
      this.isLoading = true; // Устанавливаем флаг загрузки
      this.error = null;     // Сбрасываем предыдущие ошибки
      try {
        // Выполняем GET-запрос к JSONPlaceholder
        // _limit=12 ограничит количество "книг" до 12 для нашего примера
        const response = await axios.get('[https://jsonplaceholder.typicode.com/posts?_limit=12](https://jsonplaceholder.typicode.com/posts?_limit=12)');
        
        // Данные от API (посты) имеют другую структуру, чем наши книги.
        // Мы преобразуем (мапим) их в нужный нам формат.
        this.books = response.data.map(post => ({
          id: post.id,
          title: post.title.charAt(0).toUpperCase() + post.title.slice(1).split(' ')[0], // Делаем заголовок короче и с большой буквы
          author: `Пользователь ${post.userId}`,
          price: Math.floor(Math.random() * 1000) + 500, // Генерируем случайную цену
          coverImageUrl: `https://placehold.co/120x180?text=Книга+${post.id}`,
          inStock: Math.random() > 0.2, // Случайный статус наличия
          isBestseller: Math.random() > 0.85,
          description: post.body,
        }));

      } catch (err) {
        // Если произошла ошибка, сохраняем ее в state
        console.error('Ошибка загрузки книг:', err);
        this.error = err;
      } finally {
        // Вне зависимости от результата, снимаем флаг загрузки
        this.isLoading = false;
      }
    },
  },
});
```

* Мы используем `async/await` и `try...catch...finally` для управления асинхронным кодом и состояниями.
* **Важный момент:** Мы преобразуем данные, полученные от API, в структуру, которую ожидают наши компоненты (`BookCard`, `CartItem`). Это очень распространенная практика.

### Часть 2: Интеграция загрузки данных в `HomePage.vue`

#### Шаг 3: Вызов действия `fetchBooks` при создании компонента

Нам нужно запустить загрузку книг, когда пользователь заходит на главную страницу. Хук `created()` идеально для этого подходит.

1. Открой `src/views/HomePage.vue`.
2. В секции `<script>` импортируй `useProductStore`.
3. Добавь хук `created()` и вызови в нем действие `fetchBooks`.

```vue
// src/views/HomePage.vue - <script>
import BookCard from '../components/BookCard.vue';
import { useProductStore } from '../stores/productStore';
import { useCartStore } from '../stores/cartStore';

export default {
  name: 'HomePage',
  components: { BookCard },
  data() {
    // ... data ...
  },
  computed: {
    // ... allBooks, filteredBooks ...
    // 👇 Добавляем computed свойства для isLoading и error 👇
    isLoading() {
      return useProductStore().isLoading;
    },
    error() {
      return useProductStore().error;
    }
  },
  methods: {
    // ... handleAddToCart ...
  },
  created() {
    // Вызываем действие для загрузки книг при создании компонента
    const productStore = useProductStore();
    productStore.fetchBooks();
  },
  // ... watch ...
}
</script>
```

#### Шаг 4: Отображение состояний загрузки и ошибки

Теперь в шаблоне `HomePage.vue` используем `isLoading` и `error` для отображения соответствующего интерфейса.

1. Открой `<template>` файла `src/views/HomePage.vue`.
2. Оберни существующий список книг (`<div class="book-list">...</div>`) в `v-if/v-else-if/v-else`.

```vue
<template>
  <div class="home-page">
    <hr>

    <div v-if="isLoading" class="loader">
      <p>⏳ Загружаем книги...</p>
    </div>

    <div v-else-if="error" class="error-message">
      <h2>Упс! Произошла ошибка.</h2>
      <p>{{ error.message }}</p>
      <button @click="retryFetch">Попробовать снова</button>
    </div>

    <div v-else class="book-list">
      <BookCard
        v-for="bookItem in filteredBooks" 
        :key="bookItem.id"
        :book="bookItem"
        @add-to-cart="handleAddToCart"
      />
      <p v-if="searchTerm && !filteredBooks.length" class="no-results">
        По вашему запросу "{{ searchTerm }}" ничего не найдено.
      </p>
    </div>
  </div>
</template>
```

3. **(Опционально) Добавь метод `retryFetch`** в `methods` компонента `HomePage.vue`, чтобы кнопка "Попробовать снова" работала:

```js
    // src/views/HomePage.vue - methods
    methods: {
      // ... handleAddToCart ...
      retryFetch() {
        const productStore = useProductStore();
        productStore.fetchBooks();
      }
    }
```

#### Шаг 5: Проверка функциональности

1. Сохрани все файлы (`productStore.js` и `HomePage.vue`).
2. Открой приложение в браузере. Ты должен увидеть:
  * На короткое время сообщение "⏳ Загружаем книги...".
  * Затем появится список из 12 книг, загруженных с JSONPlaceholder, отформатированных под наш `BookCard`.
3. Проверка ошибки:
  * В файле `src/stores/productStore.js`, временно измени URL в `axios.get()` на неверный (например, `.../postss`).
  * Сохрани файл и обнови страницу в браузере.
  * Теперь ты должен увидеть сообщение об ошибке и кнопку "Попробовать снова".
  * Верни URL в исходное состояние, сохрани файл и нажми кнопку "Попробовать снова" — список книг должен загрузиться.

## 📝 Итоговый код (ключевые части):

* `src/stores/productStore.js`: Теперь содержит `state` с `isLoading` и `error`, а также `action` `fetchBooks` с логикой запроса к API и преобразования данных.
* `src/views/HomePage.vue`:
  * В `created()` вызывает `productStore.fetchBooks()`.
  * В `computed` получает `isLoading` и `error` из хранилища.
  * В `<template>` использует `v-if`/`v-else-if`/`v-else` для отображения загрузчика, сообщения об ошибке или списка книг.

## 🤔 Что мы сделали:

* Перенесли источник данных из статического массива в приложении на внешний API.
* Инкапсулировали всю логику получения и хранения данных в хранилище Pinia (`productStore`).
* Реализовали обработку состояний загрузки и ошибки, чтобы предоставить пользователю понятную обратную связь.
* Преобразовали ("замапили") данные, полученные от API, в формат, удобный для нашего приложения.

Это огромный шаг к созданию настоящих, динамичных веб-приложений! В `TASK.MD` ты сможешь попрактиковаться в обработке ошибок и добавлении кнопки для обновления данных.