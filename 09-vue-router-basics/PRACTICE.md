# Тема 09: Практика 💻 Настраиваем Vue Router и создаем страницы

В теоретической части мы познакомились с Vue Router и его основными компонентами: `<router-link>` и `<router-view>`. Теперь пришло время интегрировать маршрутизацию в наше приложение, чтобы превратить его в многостраничное (SPA). Мы создадим несколько "страниц" и настроим переходы между ними.

**Важно:** Эта практика предполагает значительный рефакторинг `App.vue`. Его основная роль изменится: он станет "каркасом" приложения с навигацией и местом для отображения контента текущей страницы. Большая часть логики отображения списка книг и управления им переедет в новый компонент `HomePage.vue`.

## 🎯 Цель практики:

1.  Установить и настроить Vue Router в проекте (если это не было сделано ранее).
2.  Создать три новых компонента-страницы:
    * `HomePage.vue` (для отображения каталога книг, поиска, фильтрации).
    * `BookDetailPage.vue` (пока будет заглушкой для детальной информации о книге).
    * `CartPage.vue` (для отображения содержимого корзины).
3.  Определить маршруты для этих страниц.
4.  Модифицировать `App.vue` для использования `<router-link>` (для навигационного меню) и `<router-view>` (для отображения компонента текущей страницы).
5.  Перенести соответствующую логику и разметку из `App.vue` в `HomePage.vue`.
6.  Обеспечить базовую передачу данных для отображения корзины в `CartPage.vue` (через props от `App.vue`).

## 🛠️ Подготовка:

1.  Убедись, что твой проект, над которым мы работали в **Теме 08**, открыт в VS Code.
2.  Запусти сервер для разработки (`npm run serve`).
3.  Открой в браузере страницу приложения и консоль разработчика.

## 🚀 Шаги выполнения:

### Шаг 1: Установка и настройка Vue Router

* **Если Vue Router НЕ был добавлен при создании проекта Vue CLI:**
    1.  **Установи Vue Router:**
        ```bash
        npm install vue-router@4
        # или yarn add vue-router@4
        ```
    2.  **Создай файл конфигурации маршрутизатора:**
        * Создай папку `src/router`.
        * Внутри `src/router` создай файл `index.js`.
    3.  **Наполни `src/router/index.js` базовой конфигурацией** (пока с пустым массивом `routes`):
        ```javascript
        // src/router/index.js
        import { createRouter, createWebHistory } from 'vue-router';

        const routes = [
          // Маршруты будут добавлены здесь на Шаге 3
        ];

        const router = createRouter({
          history: createWebHistory(import.meta.env.BASE_URL),
          routes,
        });

        export default router;
        ```
    4.  **Подключи маршрутизатор в `src/main.js`:**
        ```javascript
        // src/main.js
        import { createApp } from 'vue'
        import App from './App.vue'
        import router from './router' // <--- Импортируй маршрутизатор

        const app = createApp(App)
        app.use(router) // <--- Используй маршрутизатор
        app.mount('#app')
        ```
* **Если Vue Router УЖЕ БЫЛ добавлен Vue CLI:**
    * Убедись, что у тебя есть папка `src/router` с файлом `index.js` (или аналогичным) и что он подключен в `src/main.js`. Ты будешь редактировать существующий файл `src/router/index.js`.

### Шаг 2: Создание компонентов-страниц

1.  Создай папку `src/views`, если её еще нет. В ней мы будем хранить компоненты, представляющие целые страницы.
2.  Внутри `src/views` создай три файла:
    * **`HomePage.vue`:**
        ```vue
        <template>
          <div class="home-page">
            <h1>{{ pageTitle }}</h1>
            <p>❤️ Всего понравилось: {{ totalLikes }}</p>
            
            <div class="controls-container">
              <div class="search-bar">
                <label for="searchInput">Поиск: </label>
                <input type="text" id="searchInput" placeholder="Введите название книги..." v-model="searchTerm">
                <p>Запрос: <strong>"{{ searchTerm }}"</strong> (Найдено: {{ foundBooksCount }})</p>
              </div>
              
              <div class="sort-criteria-selector">
                <label for="sortSelect">Сортировать по: </label>
                <select id="sortSelect" v-model="selectedSortCriterion">
                  <option value="title">Названию</option>
                  <option value="author">Автору</option>
                  </select>
              </div>
            </div>
            <hr>
            <div class="book-list">
              <BookCard
                v-for="bookItem in filteredBooks" 
                :key="bookItem.id"
                :book="bookItem"
                @add-to-cart="handleBookAddedToCartEvent" 
              >
                <span v-if="bookItem.price < 1300" class="discount-badge">🔥 Супер Цена!</span>
              </BookCard>
              <p v-if="searchTerm && !filteredBooks.length" class="no-results">
                По вашему запросу "{{ searchTerm }}" ничего не найдено.
              </p>
            </div>
          </div>
        </template>

        <script>
        import BookCard from '../components/BookCard.vue'; // Путь к BookCard

        export default {
          name: 'HomePage',
          components: {
            BookCard
          },
          // Вся data, computed, methods, watch для каталога переедут сюда из App.vue
          data() {
            return {
              pageTitle: 'Наш Книжный Каталог',
              totalLikes: 0, // Управляется из App.vue
              searchTerm: '',
              selectedSortCriterion: 'title',
              books: [ // Массив книг
                { id: 1, title: 'Vue.js: Путь к Мастерству', author: 'Елена Разработчикова', price: 1500, coverImageUrl: '[https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial](https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial)', inStock: true, isBestseller: true },
                { id: 2, title: 'JavaScript для Профессионалов', author: 'Иван Кодер', price: 1250, coverImageUrl: '[https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial](https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial)', inStock: false, isBestseller: false },
                { id: 3, title: 'HTML и CSS: Современные Техники', author: 'Ольга Верстальщица', price: 990, coverImageUrl: '[https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial](https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial)', inStock: true, isBestseller: false }
              ],
              // cartItems: [] // Это свойство теперь будет в App.vue
            };
          },
          props: { // Для получения cartItems и totalLikes от App.vue
            initialTotalLikes: { type: Number, default: 0 },
            onAddToCart: { type: Function, required: true } // Функция для обновления корзины в App.vue
          },
          created() {
            this.totalLikes = this.initialTotalLikes; // Инициализация локального totalLikes
          },
          computed: {
            filteredBooks() {
              const term = this.searchTerm.trim().toLowerCase();
              if (!term) return this.books;
              return this.books.filter(book => book.title.toLowerCase().includes(term));
            },
            foundBooksCount() { return this.filteredBooks.length; }
          },
          methods: {
            handleBookAddedToCartEvent(bookId) {
              // Вызываем prop-функцию, переданную из App.vue
              this.onAddToCart(bookId); 
            },
            // Локальный метод для обновления totalLikes, если он управляется здесь
            // или если он тоже передается как prop-функция от App.vue
             incrementTotalLikesHomePage() {
                this.totalLikes++; // Обновляем локальный totalLikes
                // Если totalLikes управляется в App.vue, нужно будет пробросить событие $emit('updateTotalLikes')
                // Для упрощения, пока оставим локальное управление или проброс через prop-функцию
             }
             // Методы showBookDetails и другие, специфичные для карточки, теперь в BookCard.vue
          },
          watch: {
            searchTerm(newValue) {
              console.log('(HomePage) Пользователь ищет:', newValue);
            }
          }
        }
        </script>

        <style scoped>
        .home-page { padding: 20px; }
        .controls-container { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 20px; gap: 20px; flex-wrap: wrap; }
        .search-bar, .sort-criteria-selector { padding: 10px; background-color: #f9f9f9; border-radius: 5px; }
        .search-bar input[type="text"], .sort-criteria-selector select { padding: 8px; margin-left: 5px; border: 1px solid #ccc; border-radius: 4px; }
        .search-bar p, .sort-criteria-selector p { margin-top: 5px; font-size: 0.9em; color: #555; }
        .book-list { display: flex; flex-wrap: wrap; justify-content: center; gap: 20px; }
        .no-results { width: 100%; text-align: center; font-style: italic; color: #777; padding: 20px; }
        hr { margin: 20px 0; }
        /* Стили для .discount-badge и другие могут быть здесь или глобально */
        .discount-badge { color: #dc3545; font-weight: bold; padding: 2px 5px; border: 1px solid #dc3545; border-radius: 4px; display: inline-block; }
        </style>
        ```
    * **`BookDetailPage.vue`:**
        ```vue
        <template>
          <div class="book-detail-page">
            <h1>Детальная информация о книге</h1>
            <p>Здесь будет подробное описание книги с ID: {{ $route.params.id || 'не указан' }}.</p>
            </div>
        </template>
        <script>
        export default {
          name: 'BookDetailPage'
        }
        </script>
        <style scoped>
        .book-detail-page { padding: 20px; text-align: center;}
        </style>
        ```
    * **`CartPage.vue`:**
        ```vue
        <template>
          <div class="cart-page">
            <h1>🛒 Ваша Корзина</h1>
            <div v-if="!detailedCartItems || detailedCartItems.length === 0">
              <p>Ваша корзина пока пуста.</p>
            </div>
            <div v-else class="cart-items-list-container">
              <CartItem 
                v-for="cartBookItem in detailedCartItems" 
                :key="cartBookItem.id" 
                :item="cartBookItem" 
              />
              <p class="cart-total">Всего товаров: {{ detailedCartItems.length }}</p>
            </div>
          </div>
        </template>

        <script>
        import CartItem from '../components/CartItem.vue'; // Путь к CartItem

        export default {
          name: 'CartPage',
          components: {
            CartItem
          },
          props: {
            cartItemsIds: { // Получаем массив ID из App.vue
              type: Array,
              default: () => []
            },
            allBooks: { // Получаем весь список книг из App.vue для поиска деталей
              type: Array,
              default: () => []
            }
          },
          computed: {
            detailedCartItems() {
              if (!this.allBooks || !this.cartItemsIds) return [];
              return this.cartItemsIds.map(itemId => {
                return this.allBooks.find(book => book.id === itemId);
              }).filter(book => book !== undefined);
            }
          }
        }
        </script>
        <style scoped>
        .cart-page { padding: 20px; max-width: 600px; margin: 0 auto; }
        .cart-items-list-container { border: 1px solid #eee; padding: 15px; border-radius: 5px; }
        .cart-total { text-align: right; margin-top: 15px; font-weight: bold; }
        </style>
        ```

### Шаг 3: Определение маршрутов в `src/router/index.js`

1.  Открой `src/router/index.js`.
2.  Импортируй созданные тобой компоненты-страницы.
3.  Заполни массив `routes`:

    ```javascript
    // src/router/index.js
    import { createRouter, createWebHistory } from 'vue-router';
    import HomePage from '../views/HomePage.vue';
    import BookDetailPage from '../views/BookDetailPage.vue';
    import CartPage from '../views/CartPage.vue';
    import NotFoundPage from '../views/NotFoundPage.vue'; // Предполагаем, что вы создадите его

    // Создайте NotFoundPage.vue в src/views, если еще нет:
    // <template><div><h1>404 - Страница не найдена</h1><p>Извините, такой страницы не существует.</p> <router-link to="/">На главную</router-link></div></template>
    // <script>export default { name: 'NotFoundPage' }</script>

    const routes = [
      {
        path: '/',
        name: 'Home',
        component: HomePage
      },
      {
        path: '/book/:id', // Динамический сегмент :id для следующей темы
        name: 'BookDetail',
        component: BookDetailPage,
        props: true // Позволяет передавать параметры маршрута как props в компонент
      },
      {
        path: '/cart',
        name: 'Cart',
        component: CartPage
      },
      {
        path: '/:pathMatch(.*)*', // Для всех остальных путей
        name: 'NotFound',
        component: NotFoundPage
      }
    ];

    const router = createRouter({
      history: createWebHistory(import.meta.env.BASE_URL),
      routes,
    });

    export default router;
    ```
    * Для `/book/:id` мы уже заложили динамический сегмент `:id`, который понадобится в следующей теме. `props: true` позволит передавать `:id` как prop в `BookDetailPage`.

### Шаг 4: Модификация `App.vue` для использования маршрутизации

`App.vue` теперь станет главным "каркасом" приложения.

1.  **Обнови `<template>` в `App.vue`:**
    Удали из него всю разметку, связанную со списком книг, поиском, корзиной и т.д. Оставь или добавь навигационное меню с `<router-link>` и `<router-view>` для отображения контента страниц.

    ```vue
    // src/App.vue - <template>
    <template>
      <div id="app-container">
        <header class="app-header">
          <nav>
            <router-link :to="{ name: 'Home' }">Каталог</router-link> |
            <router-link :to="{ name: 'Cart' }">Корзина ({{ cartItems.length }})</router-link>
            </nav>
          <div class="app-title">Мой Книжный Магазин Онлайн</div>
        </header>

        <main class="app-content">
          <router-view 
            :initialTotalLikes="totalLikes"
            :cartItemsIds="cartItems"
            :allBooks="allBooksForCartPage" 
            @update-cart="handleBookAddedToCart"
            @update-total-likes="incrementTotalLikes" 
          />
          </main>

        <footer class="app-footer">
          <p>&copy; {{ new Date().getFullYear() }} Vue Books Ltd.</p>
        </footer>
      </div>
    </template>
    ```

2.  **Обнови `<script>` в `App.vue`:**
    * Большая часть `data`, `computed`, `methods` переехала в `HomePage.vue`.
    * В `App.vue` **оставим управление состоянием корзины (`cartItems`) и общим количеством лайков (`totalLikes`)**, так как эти данные могут быть нужны на разных страницах или в шапке.
    * `allBooksForCartPage` будет содержать полный список книг, который нужен `CartPage` для получения деталей по ID.

    ```vue
    // src/App.vue - <script>
    export default {
      name: 'App',
      data() {
        return {
          cartItems: [], // ID книг в корзине
          totalLikes: 0,  // Общее количество лайков
           // Этот массив нужен, чтобы CartPage мог найти детали книг по ID из cartItems
           // В реальном приложении это пришло бы из API или Vuex/Pinia
          allBooksForCartPage: [ 
            { id: 1, title: 'Vue.js: Путь к Мастерству', author: 'Елена Разработчикова', price: 1500, coverImageUrl: '[https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial](https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial)', inStock: true, isBestseller: true },
            { id: 2, title: 'JavaScript для Профессионалов', author: 'Иван Кодер', price: 1250, coverImageUrl: '[https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial](https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial)', inStock: false, isBestseller: false },
            { id: 3, title: 'HTML и CSS: Современные Техники', author: 'Ольга Верстальщица', price: 990, coverImageUrl: '[https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial](https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial)', inStock: true, isBestseller: false }
          ]
        };
      },
      methods: {
        handleBookAddedToCart(bookId) {
          console.log('(App.vue) Получен ID книги для добавления в корзину:', bookId);
          if (!this.cartItems.includes(bookId)) {
            this.cartItems.push(bookId);
          } else {
            console.log(`(App.vue) Книга с ID: ${bookId} уже в корзине.`);
          }
        },
        incrementTotalLikes() {
            this.totalLikes++;
        }
      },
      // Computed и Watch, если они глобальны, могут остаться здесь.
      // Но большинство из них, вероятно, переехали в HomePage.vue
    }
    </script>
    ```
    * **Важно по передаче данных и событий:**
        * `HomePage.vue` теперь получает `initialTotalLikes` и `onAddToCart` как props. Когда в `BookCard` (внутри `HomePage`) нажимается "В корзину", `BookCard` генерирует событие `addToCart`. `HomePage` слушает его и вызывает свой метод `handleBookAddedToCartEvent`, который, в свою очередь, вызывает prop-функцию `this.onAddToCart(bookId)` (которая на самом деле `handleBookAddedToCart` из `App.vue`).
        * Аналогично для `totalLikes`, если `HomePage` должен его менять: он может получить `initialTotalLikes` и функцию для его обновления от `App.vue`.
        * Для `CartPage`: `<router-view>` передает `cartItemsIds` (это `cartItems` из `App.vue`) и `allBooks` (это `allBooksForCartPage` из `App.vue`) как props в `CartPage.vue`. `CartPage.vue` должен объявить эти props.

3.  **Обнови `<style>` в `App.vue` (добавь стили для каркаса):**
    ```css
    /* src/App.vue - <style> (можно scoped или глобальные) */
    #app-container {
      display: flex;
      flex-direction: column;
      min-height: 100vh;
    }
    .app-header {
      background-color: #35495e;
      color: white;
      padding: 15px 30px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .app-header nav a {
      font-weight: bold;
      color: #c0d1e2; /* Светлее для неактивных ссылок */
      margin: 0 10px;
      text-decoration: none;
    }
    .app-header nav a.router-link-exact-active {
      color: #42b983; /* Фирменный зеленый Vue для активной ссылки */
    }
    .app-title {
      font-size: 1.5em;
    }
    .app-content {
      flex-grow: 1; /* Занимает все доступное пространство */
      padding: 20px;
    }
    .app-footer {
      background-color: #f7f7f7;
      text-align: center;
      padding: 10px;
      border-top: 1px solid #eee;
      font-size: 0.9em;
    }
    /* Глобальные стили, если есть, лучше вынести в отдельный css-файл и импортировать в main.js */
    body { margin: 0; font-family: Avenir, Helvetica, Arial, sans-serif; }
    ```

### Шаг 5: Проверка навигации

1.  Сохрани все измененные файлы.
2.  Перезапусти сервер разработки, если он не перезапустился автоматически.
3.  **Проверь навигацию:**
    * Кликни по ссылкам "Каталог" и "Корзина" в навигационном меню. Содержимое страницы (внутри `<router-view>`) должно меняться, отображая `HomePage.vue` и `CartPage.vue` соответственно.
    * URL в адресной строке браузера должен обновляться.
    * Убедись, что список книг и фильтрация по-прежнему работают на "Главной странице" (`HomePage.vue`).
    * Убедись, что при добавлении товаров в корзину на `HomePage.vue` обновляется счетчик в шапке (`App.vue`) и затем эти товары отображаются на `CartPage.vue`.

## 📝 Итоговый код (ключевые моменты):

* **`src/router/index.js`**: должен содержать импорты компонентов-страниц и массив `routes`.
* **`src/main.js`**: должен импортировать `router` и делать `app.use(router)`.
* **`src/views/HomePage.vue`**: теперь содержит основную логику каталога (данные о книгах, фильтрацию, `v-for` с `BookCard`, обработку `addToCart` через prop-функцию).
* **`src/views/BookDetailPage.vue`**: простой компонент-заглушка.
* **`src/views/CartPage.vue`**: принимает `cartItemsIds` и `allBooks` как props, имеет `computed` для `detailedCartItems` и отображает их с помощью `CartItem.vue`.
* **`src/App.vue`**: теперь это компонент-каркас с `<nav>`, `<router-view>` и `<footer>`. Он управляет `cartItems` и `totalLikes`.

## 🤔 Что мы сделали:

* Установили (или проверили установку) и настроили Vue Router.
* Создали несколько компонентов-страниц (`HomePage`, `BookDetailPage`, `CartPage`).
* Определили маршруты для этих страниц.
* Значительно отрефакторили `App.vue`, превратив его в основной макет приложения с навигацией и областью для отображения страниц (`<router-view>`).
* Перенесли логику отображения каталога книг в `HomePage.vue`.
* Настроили передачу данных (корзины, общего списка книг) и обработчиков событий между `App.vue`, `HomePage.vue` и `CartPage.vue` с помощью props и кастомных событий (через `<router-view>`).

Это был большой и важный шаг! Теперь твое приложение имеет структуру, характерную для SPA.

В `TASK.MD` ты сможешь попрактиковаться в добавлении новых маршрутов и ссылок.