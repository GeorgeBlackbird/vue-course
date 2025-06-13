# Тема 11: Практика 💻 Управляем корзиной с помощью Pinia
> <p align=center> 🛠️ Практика </p>

В теоретической части мы познакомились с Pinia — официальным решением для управления состоянием в Vue 3. Теперь пришло время применить эти знания на практике и перенести всю логику управления нашей корзиной из компонентов в централизованное хранилище.

<br>

## 🎯 Цель практики:

1.  Установить и настроить Pinia в нашем проекте.
2.  Создать два хранилища:
    * `productStore.js` для хранения основного списка книг.
    * `cartStore.js` для управления состоянием корзины (ID товаров, методы добавления).
3.  Перенести логику и данные (массив `cartItems`, методы `handleBookAddedToCart`) из `App.vue` и других компонентов в `cartStore.js`.
4.  Использовать **действия (actions)** из `cartStore` для добавления книги в корзину при клике в `BookCard.vue`.
5.  Использовать **геттеры (getters)** из `cartStore` для отображения товаров в корзине на `CartPage.vue` и счетчика товаров в `App.vue`.

<br>

## 🛠️ Подготовка:

1.  Убедись, что твой проект, над которым мы работали в **Теме 10** (с динамической маршрутизацией), открыт в VS Code.
2.  Запусти сервер для разработки (`npm run serve`).

<br>

## 🚀 Шаги выполнения:

### Шаг 1: Установка и настройка Pinia

1.  **Установи Pinia:**
    ```bash
    npm install pinia
    ```
2.  **Создай папку для хранилищ:**
    * Внутри папки `src` создай новую папку `stores`.
3.  **Подключи Pinia в `src/main.js`:**
    ```javascript
    // src/main.js
    import { createApp } from 'vue'
    import { createPinia } from 'pinia' // 1. Импорт
    import App from './App.vue'
    import router from './router'

    const app = createApp(App)
    const pinia = createPinia() // 2. Создание экземпляра

    app.use(router)
    app.use(pinia) // 3. Подключение Pinia к приложению

    app.mount('#app')
    ```

### Шаг 2: Создание хранилищ (Stores)

Мы разделим наше состояние: товары и корзина будут в разных хранилищах.

1.  **Создай `productStore.js`:**
    * В `src/stores` создай файл `productStore.js`.
    * Перенеси в него массив `books` из `App.vue`.

    ```javascript
    // src/stores/productStore.js
    import { defineStore } from 'pinia';

    export const useProductStore = defineStore('products', {
      state: () => ({
        books: [
          { id: 1, title: 'Vue.js: Путь к Мастерству', author: 'Елена Разработчикова', price: 1500, coverImageUrl: '[https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial](https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial)', inStock: true, isBestseller: true, description: 'Полное руководство по Vue.js от основ до продвинутых техник.' },
          { id: 2, title: 'JavaScript для Профессионалов', author: 'Иван Кодер', price: 1250, coverImageUrl: '[https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial](https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial)', inStock: false, isBestseller: false, description: 'Все тонкости и нюансы современного JavaScript.' },
          { id: 3, title: 'HTML и CSS: Современные Техники', author: 'Ольга Верстальщица', price: 990, coverImageUrl: '[https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial](https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial)', inStock: true, isBestseller: false, description: 'Изучите современные подходы к верстке веб-страниц.' }
        ]
      }),
      // В этом хранилище могут быть геттеры для поиска книги по ID и т.д.
      getters: {
        getBookById: (state) => (id) => {
          return state.books.find(book => book.id === id);
        }
      }
    });
    ```

2.  **Создай `cartStore.js`:**
    * В `src/stores` создай файл `cartStore.js`.
    * Здесь будет логика корзины. Это хранилище будет использовать `productStore` для получения деталей о товарах.

    ```javascript
    // src/stores/cartStore.js
    import { defineStore } from 'pinia';
    import { useProductStore } from './productStore'; // Импортируем хранилище продуктов

    export const useCartStore = defineStore('cart', {
      state: () => ({
        itemIds: [], // Храним только ID товаров
      }),
      getters: {
        // Геттер для получения количества товаров
        count() {
          return this.itemIds.length;
        },
        // Геттер для получения детальной информации о товарах в корзине
        detailedItems() {
          const productStore = useProductStore(); // Получаем доступ к другому хранилищу
          return this.itemIds.map(itemId => 
            productStore.getBookById(itemId) // Используем геттер из productStore
          ).filter(item => item !== undefined);
        },
        // Геттер для общей стоимости
        totalPrice() {
            return this.detailedItems.reduce((total, item) => total + item.price, 0);
        }
      },
      actions: {
        addItem(itemId) {
          if (!this.itemIds.includes(itemId)) {
            this.itemIds.push(itemId);
            console.log(`(cartStore) Товар с ID: ${itemId} добавлен.`);
          } else {
            console.log(`(cartStore) Товар с ID: ${itemId} уже в корзине.`);
          }
        },
        removeItem(itemId) {
          this.itemIds = this.itemIds.filter(id => id !== itemId);
          console.log(`(cartStore) Товар с ID: ${itemId} удален.`);
        },
      },
    });
    ```
    * Обрати внимание, как `cartStore` использует `useProductStore()` внутри `getters`, чтобы получить доступ к данным о товарах. Это мощная возможность Pinia!

### Шаг 3: Рефакторинг компонентов для использования хранилищ

Теперь удалим старое управление состоянием из компонентов и заменим его на использование Pinia.

1.  **Рефакторинг `App.vue`:**
    * Удали из `data()` массивы `books` и `cartItems`, а также метод `handleBookAddedToCart`.
    * Используй `cartStore` для отображения количества товаров в корзине в навигации.

    ```html
    // src/App.vue - <template>
    <template>
      <div id="app-container">
        <header class="app-header">
          <nav>
            <router-link :to="{ name: 'Home' }">Каталог</router-link> |
            <router-link :to="{ name: 'Cart' }">Корзина ({{ cartItemCount }})</router-link>
          </nav>
          </header>
        <main class="app-content">
          <router-view />
        </main>
        </div>
    </template>

    <script>
    import { useCartStore } from './stores/cartStore'; // Импортируем хранилище

    export default {
      name: 'App',
      computed: {
        // Создаем вычисляемое свойство для доступа к геттеру
        cartItemCount() {
          return useCartStore().count;
        }
      },
      // data() и methods() теперь пустые (в контексте корзины)
    }
    </script>
    ```

2.  **Рефакторинг `HomePage.vue`:**
    * Удаляем `props`, `data`, `methods`, связанные с книгами и корзиной. Теперь все берется из хранилищ.

    ```html
    // src/views/HomePage.vue
    <template>
      <div class="home-page">
        <h1>{{ pageTitle }}</h1>
        <hr>
        <div class="book-list">
          <BookCard
            v-for="bookItem in filteredBooks" 
            :key="bookItem.id"
            :book="bookItem"
            @add-to-cart="handleAddToCart" />
          </div>
      </div>
    </template>

    <script>
    import BookCard from '../components/BookCard.vue';
    import { useProductStore } from '../stores/productStore'; // Импорт хранилища продуктов
    import { useCartStore } from '../stores/cartStore';   // Импорт хранилища корзины

    export default {
      name: 'HomePage',
      components: { BookCard },
      data() {
        return {
          pageTitle: 'Наш Книжный Каталог',
          searchTerm: '',
          selectedSortCriterion: 'title',
        };
      },
      computed: {
        // Получаем список книг из хранилища продуктов
        allBooks() {
          return useProductStore().books;
        },
        // `filteredBooks` теперь зависит от `allBooks` из хранилища
        filteredBooks() {
          const term = this.searchTerm.trim().toLowerCase();
          if (!term) return this.allBooks;
          return this.allBooks.filter(book => book.title.toLowerCase().includes(term));
        }
      },
      methods: {
        handleAddToCart(bookId) {
          // Вызываем действие из хранилища корзины
          const cartStore = useCartStore();
          cartStore.addItem(bookId);
        }
        //navigateToBookDetailPage остается здесь, т.к. это логика навигации
      }
    }
    </script>
    ```

3.  **Рефакторинг `BookCard.vue`:**
    * Здесь изменения минимальны. Компонент по-прежнему генерирует событие `addToCart`. Логика сместилась из `App.vue` в `HomePage.vue`, но интерфейс `BookCard` остался тем же. Убедись, что `emits` все еще объявлен.

4.  **Рефакторинг `CartPage.vue`:**
    * Удаляем `props` (`cartItemsIds`, `allBooks`).
    * Вся информация теперь берется напрямую из `cartStore`.

    ```html
    // src/views/CartPage.vue
    <template>
      <div class="cart-page">
        <h1>🛒 Ваша Корзина</h1>
        <div v-if="itemCount === 0">
          <p>Ваша корзина пока пуста.</p>
        </div>
        <div v-else class="cart-items-list-container">
          <CartItem 
            v-for="cartBookItem in items" 
            :key="cartBookItem.id" 
            :item="cartBookItem" 
          />
          <p class="cart-total">Общая стоимость: {{ total }} руб.</p>
          <p class="cart-total">Всего товаров: {{ itemCount }}</p>
        </div>
      </div>
    </template>

    <script>
    import CartItem from '../components/CartItem.vue';
    import { useCartStore } from '../stores/cartStore'; // Импорт хранилища корзины

    export default {
      name: 'CartPage',
      components: { CartItem },
      computed: {
        // Используем геттеры из хранилища для отображения данных
        items() {
          return useCartStore().detailedItems;
        },
        itemCount() {
          return useCartStore().count;
        },
        total() {
            return useCartStore().totalPrice;
        }
      }
      // props теперь не нужны
    }
    </script>
    ```

### Шаг 4: Проверка функциональности

1.  Сохрани все файлы.
2.  Открой приложение в браузере.
3.  **Проверь работу:**
    * На `HomePage.vue` нажми "В корзину" на нескольких книгах.
    * Счетчик товаров в шапке (в `App.vue`) должен обновиться.
    * Перейди на страницу "Корзина".
    * На `CartPage.vue` должны отображаться все добавленные товары с их деталями и общей стоимостью.
    * Вся функциональность должна работать как раньше, но теперь она управляется из централизованного хранилища Pinia!

<br>

## 📝 Итоговый код (ключевые моменты):

* **`src/main.js`**: `app.use(createPinia())`
<br>
* **`src/stores/productStore.js`**: содержит `state` с массивом `books`.
<br>
* **`src/stores/cartStore.js`**: содержит `state` с `itemIds`, `getters` для `count`, `detailedItems`, `totalPrice` и `actions` для `addItem`, `removeItem`.
<br>
* **`App.vue`**: теперь очень "тонкий", использует `cartStore` только для счетчика в шапке.
<br>
* **`HomePage.vue`**: использует `productStore` для получения списка книг и `cartStore` для вызова действия `addItem`.
<br>
* **`CartPage.vue`**: использует `cartStore` для получения `detailedItems`, `count` и `totalPrice` для отображения содержимого корзины.

<br>

## 🤔 Что мы сделали:

* Установили и настроили Pinia.
* **Централизовали состояние** нашего приложения, разделив его на логические хранилища (`productStore`, `cartStore`).
* **Избавились от "прокидывания" props и цепочек событий** для управления корзиной.
* Научили компоненты напрямую взаимодействовать с хранилищем: вызывать **actions** для изменения состояния и использовать **getters** для получения обработанных данных.

Теперь наше приложение имеет гораздо более чистую, надежную и масштабируемую архитектуру.

> В `TASK.MD` ты сможешь попрактиковаться в добавлении новых действий и геттеров в хранилище.
> 
---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](./THEORY.md) | [Вперед ➡️](./TASK.md)** 

</div>