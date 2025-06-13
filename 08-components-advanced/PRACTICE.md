# Тема 08: Практика 💻 События, Слоты в `BookCard`
> <p align=center> 🛠️ Практика </p>

В теоретической части мы рассмотрели, как компоненты могут общаться с родителями через события (`$emit`), как использовать слоты (`<slot>`) для гибкого наполнения контентом, а также динамические компоненты и `<keep-alive>`. На этой практике мы сосредоточимся на событиях и слотах, чтобы сделать наш компонент `BookCard` более функциональным и настраиваемым.

<br>

## 🎯 Цель практики:

1.  **События:**
    * Модифицировать компонент `BookCard.vue` так, чтобы при клике на кнопку "В корзину" он генерировал пользовательское событие (например, `addToCart`).
    * В событие передавать `id` (или весь объект) книги, которую нужно добавить в корзину.
    * В родительском компоненте `App.vue` прослушать это событие и обновить массив `cartItems` (который мы создадим в `data` компонента `App.vue`), добавив в него `id` книги.
    * Отобразить содержимое `cartItems` на странице `App.vue`.
2.  **Слоты:**
    * Добавить в компонент `BookCard.vue` элемент `<slot>` (слот по умолчанию).
    * Из родительского компонента `App.vue` передать в этот слот дополнительную информацию для некоторых книг (например, значок "скидка" или специальное предложение).

<br>

## 🛠️ Подготовка:

1.  Убедись, что твой проект (например, `my-bookstore-app`), над которым мы работали в **Теме 07** (с созданным компонентом `BookCard.vue`), открыт в VS Code.
2.  Запусти сервер для разработки, если он еще не запущен:
    ```bash
    npm run serve
    ```
3.  Открой в браузере страницу твоего приложения (обычно `http://localhost:8080/`).

<br>

## Часть 1: Генерация и обработка события "Добавить в корзину" 🛒

### Шаг 1: Объявление и генерация события в `BookCard.vue`

1.  Открой файл `src/components/BookCard.vue`.
2.  В секции `<script>` добавь опцию `emits`, чтобы объявить, какие события может генерировать этот компонент:
    ```js
    // src/components/BookCard.vue - <script>
    export default {
      name: 'BookCard',
      props: {
        book: {
          type: Object,
          required: true
        }
      },
      emits: ['addToCart'], // Объявляем событие, которое компонент может генерировать
      methods: {
        // ... другие методы (handleDetailsClick, handleLikeClick) ...
        handleAddToCart() {
          // Вместо alert или console.log, теперь генерируем событие
          this.$emit('addToCart', this.book.id); // Передаем ID книги вместе с событием
          console.log(`(BookCard) Событие "addToCart" с ID: ${this.book.id} сгенерировано.`);
        },
        handleFavoriteClick() { // Оставим этот alert для примера
            alert('Книга "' + this.book.title + '" (из компонента BookCard) добавлена в избранное!');
        },
        handleDetailsClick() { // И этот тоже пока оставим
            console.log('(BookCard) Клик "Подробнее" для:', this.book.title);
            alert('Детализация для "' + this.book.title + '" будет доступна позже.');
        },
        handleLikeClick() { // И этот
             console.log('(BookCard) Клик "Нравится" для:', this.book.title);
             alert('Функция "Нравится" для "' + this.book.title + '" будет обработана в App.vue.');
        }
      }
    }
    </script>
    ```
    * Мы добавили `emits: ['addToCart']`.
    * В методе `handleAddToCart` мы заменили `alert` на `this.$emit('addToCart', this.book.id)`. Это сгенерирует событие `addToCart` и передаст `this.book.id` в качестве данных (полезной нагрузки) этого события.

### Шаг 2: Создание массива `cartItems` и метода-обработчика в `App.vue`

1.  Открой файл `src/App.vue`.
2.  В функции `data()` добавь новое свойство `cartItems` – это будет массив, хранящий ID книг в корзине.
    ```js
    // src/App.vue - data()
    data() {
      return {
        // ... pageTitle, totalLikes, searchTerm, selectedSortCriterion, books ...
        cartItems: [] // <--- Наша новая корзина (массив ID книг)
      };
    }
    ```
3.  В опции `methods` компонента `App.vue` создай новый метод, который будет добавлять ID книги в массив `cartItems`.
    ```js
    // src/App.vue - methods
    methods: {
      // ... твои существующие методы (если они были) ...
      // Метод для обработки события addToCart от BookCard
      handleBookAddedToCart(bookId) {
        console.log(`(App.vue) Получен ID книги для добавления в корзину: ${bookId}`);
        if (!this.cartItems.includes(bookId)) { // Проверяем, чтобы не добавлять дубликаты
          this.cartItems.push(bookId);
        } else {
          console.log(`(App.vue) Книга с ID: ${bookId} уже в корзине.`);
        }
      },
      // Старые методы, которые теперь не вызываются напрямую из BookCard
      // showBookDetails(book) { /* ... */ },
      // incrementTotalLikes() { /* ... */ }
    }
    ```
4.  **Отобразим содержимое корзины** для наглядности. В `<template>` компонента `App.vue` (например, под заголовком или счетчиком лайков) добавь:
    ```html
    <p>🛒 Корзина (ID товаров): {{ cartItems.length ? cartItems.join(', ') : 'пока пуста' }}</p>
    <p>Всего товаров в корзине: {{ cartItems.length }}</p>
    ```

### Шаг 3: Прослушивание события в `App.vue`

Теперь в шаблоне `App.vue`, где ты используешь компонент `<BookCard>` внутри цикла `v-for`, добавь прослушиватель для нашего нового события `addToCart`:

```html
<div class="book-list">
  <BookCard
    v-for="bookItem in filteredBooks"
    :key="bookItem.id"
    :book="bookItem"
    @add-to-cart="handleBookAddedToCart" >
    </BookCard>
  </div>
```

* `@add-to-cart="handleBookAddedToCart"`: Когда компонент `BookCard` сгенерирует событие `add-to-cart`, будет вызван метод `handleBookAddedToCart` компонента `App.vue`. Полезная нагрузка события (ID книги) будет автоматически передана в этот метод.

### Шаг 4: Проверка добавления в корзину

* Сохрани оба файла (`BookCard.vue` и `App.vue`).
* Открой приложение в браузере.
* Кликай на кнопки "В корзину" (или "🛒 Добавить") у разных книг.
* Ты должен видеть, как обновляется отображение "Корзина (ID товаров): ..." и "Всего товаров в корзине: ...", показывая ID добавленных книг.
* Проверь консоль – там должны быть сообщения от `BookCard.vue` (о генерации события) и от `App.vue` (о получении ID).

*(Скриншот/GIF: пользователь кликает "В корзину" на нескольких книгах, и список ID в секции "Корзина" на странице обновляется).*

<br>

## Часть 2: Использование слота в `BookCard.vue` 🎁

Давай сделаем наш `BookCard` еще более гибким, добавив возможность вставлять в него дополнительную информацию из родительского компонента.

### Шаг 5: Добавление `<slot>` в `BookCard.vue`

1. Открой файл `src/components/BookCard.vue`.

2. В его секции `<template>` выбери место, куда можно было бы вставить дополнительную информацию. Например, под ценой или перед кнопками действий.

3. Вставь туда тег `<slot>`. Ты можешь добавить и содержимое по умолчанию, которое будет отображаться, если родитель ничего не передаст в слот.

```html
// src/components/BookCard.vue - <template>
<template>
  <div class="book-card">
    <p v-if="!book.inStock" class="out-of-stock">Нет в наличии</p>
    <p v-else class="in-stock">В наличии</p>

    <div class="book-extra-info">
      <slot>
        </slot>
    </div>

    <div class="book-actions">
      </div>
  </div>
</template>
```

Добавим немного стиля для `.book-extra-info`:

```css
    /* src/components/BookCard.vue - <style scoped> */
    /* ... */
    .book-extra-info {
      font-size: 0.85em;
      margin: 8px 0;
      min-height: 1.2em; /* Чтобы карточки не прыгали по высоте, если слота нет */
    }
    /* ... */
```

### Шаг 6: Передача контента в слот из `App.vue`

Теперь в `App.vue`, там где используется `<BookCard>`, мы можем передать в него контент.

1. Открой файл `src/App.vue`.

2. В цикле `v-for`, где рендерится `<BookCard>`, для некоторых книг (например, для тех, у которых низкая цена или которые являются бестселлерами) вставь какой-нибудь HTML-код между открывающим и закрывающим тегами `<BookCard>`.

```html
<div class="book-list">
  <BookCard
    v-for="bookItem in filteredBooks"
    :key="bookItem.id"
    :book="bookItem"
    @add-to-cart="handleBookAddedToCart"
  >
    <span v-if="bookItem.price < 1000" class="discount-badge">
      🔥 Супер Цена!
    </span>
    <span v-if="bookItem.isBestseller && bookItem.id === 1" style="color: purple; font-weight:bold;">
      Эксклюзивное предложение!
    </span>
  </BookCard>
</div>
```

3. Добавь стиль для `.discount-badge` в `App.vue` (или в глобальные стили, если хочешь):

```css
    /* src/App.vue - <style> (можно не scoped, если хотите чтобы и другие компоненты могли использовать) */
    .discount-badge {
      color: #dc3545; /* Красный цвет Bootstrap для danger */
      font-weight: bold;
      padding: 2px 5px;
      border: 1px solid #dc3545;
      border-radius: 4px;
      display: inline-block; /* Чтобы занимал только необходимое место */
    }
```

### Шаг 7: Проверка отображения слота

1. Сохрани оба файла.
2. Посмотри на страницу в браузере.
3. Для тех книг, для которых ты передал контент в слот (например, книги с ценой меньше 1000 рублей), должен отобразиться значок "🔥 Супер Цена!".
4. Для книг, где условие `v-if` внутри слота не выполнилось, и если у слота было содержимое по умолчанию, оно бы отобразилось. В нашем случае, если ни одно из условий `v-if` не сработало, слот будет пустым.

<br>

## 📝 Итоговый примерный код (ключевые части):

**`src/components/BookCard.vue` (изменения в `<script>` и `<template>`):**

```js
// <script>
export default {
  name: 'BookCard',
  props: { /* ... */ },
  emits: ['addToCart'], // Добавили
  methods: {
    handleAddToCart() {
      this.$emit('addToCart', this.book.id); // Изменили
      console.log(`(BookCard) Событие "addToCart" с ID: ${this.book.id} сгенерировано.`);
    },
    // ... другие методы-заглушки ...
  }
}
// <template>
// ...
<div class="book-extra-info">
  <slot></slot> </div>
// ...
</script>
```

**`src/App.vue` (изменения в `<script>` и `<template>`):**

```html
// <script> - data()
data() {
  return { /* ..., */ cartItems: [] };
}
// <script> - methods
methods: {
  handleBookAddedToCart(bookId) { /* ... */ }
}
// <template>
// ...
<p>🛒 Корзина (ID товаров): {{ cartItems.length ? cartItems.join(', ') : 'пока пуста' }}</p>
// ...
<BookCard
  v-for="bookItem in filteredBooks"
  :key="bookItem.id"
  :book="bookItem"
  @add-to-cart="handleBookAddedToCart" >
  <span v-if="bookItem.price < 1300" class="discount-badge">
    🔥 Супер Цена!
  </span>
  <strong v-if="bookItem.id === 3" style="color: blue; display: block; margin-top: 5px;">Рекомендация Редакции!</strong>
</BookCard>
// ...
</script>
// <style> (в App.vue или глобально)
.discount-badge { /* ... */ }
</style>
```

<br>

## 🤔 Что мы сделали:

* Научили компонент `BookCard` **генерировать пользовательское событие** `addToCart` при клике на кнопку, передавая `id` книги.
* В `App.vue` **прослушали это событие** и обновили массив `cartItems`.
* Добавили в `BookCard` **слот по умолчанию**, что позволило вставлять кастомный HTML-контент из `App.vue` внутрь карточки книги.

Эти механизмы делают компоненты по-настоящему мощными и гибкими, позволяя строить сложные взаимодействия и настраиваемые интерфейсы.

> В `TASK.MD` ты сможешь попрактиковаться с именованными слотами или передать более сложные данные через события.
> 
---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](./THEORY.md) | [Вперед ➡️](./TASK.md)** 

</div>