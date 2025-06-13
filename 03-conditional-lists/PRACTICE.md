# Тема 03: Практика 💻 Условный рендеринг и списки книг
> <p align=center> 🛠️ Практика </p>

В теоретической части мы рассмотрели директивы `v-if`, `v-else`, `v-show` для условного отображения и `v-for` для работы со списками, а также важность атрибута `:key`. Теперь самое время применить эти знания на практике и отобразить наш первый каталог книг!

<br>

## 🎯 Цель практики:

1.  Создать в `data()` компонента `App.vue` массив объектов, представляющий список книг. Каждая книга будет иметь свойства: `id`, `title`, `author`, `price`, `coverImageUrl`, `inStock` (наличие на складе).
2.  Отобразить этот список книг на странице, используя директиву `v-for`. Для каждой книги вывести её обложку, название, автора и цену.
3.  Для книг, которых нет в наличии (`inStock: false`), отобразить сообщение "Нет в наличии", используя директиву `v-if`.


<br>

## 🛠️ Подготовка:

1.  Убедись, что твой проект (например, `my-bookstore-app`), над которым мы работали в **Теме 02**, открыт в VS Code.
2.  Запусти сервер для разработки, если он еще не запущен:
    ```bash
    npm run serve
    ```
3.  Открой в браузере страницу твоего приложения (обычно `http://localhost:8080/`). Ты должен видеть результат работы из предыдущей практики (информацию об одной книге и, возможно, кнопку "Купить").


<br>

## 🚀 Шаги выполнения:

### Шаг 1: Обновляем данные в `App.vue` – создаем список книг

Раньше у нас в `data()` был один объект `book`. Теперь мы заменим его на массив `books`, чтобы хранить несколько книг.

Открой файл `src/App.vue` и модифицируй функцию `data()` в секции `<script>`:

```vue
<script>
export default {
  name: 'App',
  data() {
    return {
      pageTitle: 'Наш Книжный Каталог', // Обновим заголовок
      // isPurchaseDisabled: true, // Можете пока закомментировать или удалить, если не нужно

      // 👇 Заменяем одиночный объект book на массив books 👇
      books: [
        {
          id: 1, // Уникальный ID для :key
          title: 'Vue.js: Путь к Мастерству',
          author: 'Елена Разработчикова',
          price: 1500,
          coverImageUrl: '[https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial](https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial)',
          inStock: true
        },
        {
          id: 2,
          title: 'JavaScript для Профессионалов',
          author: 'Иван Кодер',
          price: 1250,
          coverImageUrl: '[https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial](https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial)',
          inStock: false // Этой книги нет в наличии
        },
        {
          id: 3,
          title: 'HTML и CSS: Современные Техники',
          author: 'Ольга Верстальщица',
          price: 990,
          coverImageUrl: '[https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial](https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial)',
          inStock: true
        }
      ]
    };
  }
}
</script>
```

**Что мы сделали:**
* Заменили свойство `book` на свойство `books`, которое теперь является массивом.
* Каждый элемент массива — это объект, представляющий книгу. Обрати внимание, что у каждой книги есть уникальный `id` (это важно для `:key`) и свойство `inStock` (булево значение).

### Шаг 2: Отображаем список книг с помощью `v-for`

Теперь изменим секцию `<template>` так, чтобы она отображала этот список книг. Ты можешь удалить или закомментировать предыдущую разметку, которая отображала одну книгу.

```vue
<template>
  <div id="book-catalog-app">
    <h1>{{ pageTitle }}</h1>
    <div class="book-list">
      <div v-for="book in books" :key="book.id" class="book-card">
        <img :src="book.coverImageUrl" :alt="'Обложка книги ' + book.title" class="book-cover">
        <h3>{{ book.title }}</h3>
        <p><strong>Автор:</strong> {{ book.author }}</p>
        <p><strong>Цена:</strong> {{ book.price }} руб.</p>
        </div>
    </div>
  </div>
</template>
```

**Разбор:**
* Мы создали `div` с классом `book-list` для обертывания нашего списка.
* `v-for="book in books"`: Эта директива говорит Vue пройти по каждому элементу в массиве `books`. В каждой итерации текущий элемент будет доступен как переменная `book`.
* `:key="book.id"`: Мы привязываем атрибут `key` к уникальному идентификатору `id` каждой книги. Это помогает Vue эффективно обновлять список. **Никогда не забывай про `:key` при работе с `v-for`!**
* Внутри блока `v-for` мы отображаем обложку (используя `:src` из прошлой темы), название, автора и цену текущей книги (`book`).

Добавь немного стилей в секцию `<style>` для лучшего отображения (можешь использовать свои или адаптировать эти):

```css
<style>
#book-catalog-app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
  padding: 20px;
}

.book-list {
  display: flex; /* Расположим книги в ряд */
  flex-wrap: wrap; /* Позволим переноситься на новую строку */
  justify-content: center; /* Центрируем карточки */
  gap: 20px; /* Промежуток между карточками */
}

.book-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 15px;
  width: 200px; /* Фиксированная ширина карточки */
  box-shadow: 2px 2px 8px rgba(0,0,0,0.1);
  display: flex;
  flex-direction: column; /* Элементы внутри карточки - в столбик */
  align-items: center; /* Центрируем по горизонтали */
}

.book-cover {
  width: 100px; /* Фиксированная ширина обложки */
  height: 150px; /* Фиксированная высота обложки */
  object-fit: cover; /* Чтобы изображение красиво вписывалось */
  margin-bottom: 10px;
  border: 1px solid #eee;
}

h1 { color: #42b983; }
h3 { margin: 10px 0 5px; font-size: 1.1em; }
p { margin: 5px 0; font-size: 0.9em; }
</style>
```

Сохрани файл. Ты должен увидеть список из трех книг, каждая со своей информацией и обложкой.

### Шаг 3: Условное отображение сообщения "Нет в наличии"

Теперь для книг, у которых `inStock` равно `false`, мы должны показать сообщение "Нет в наличии". Мы сделаем это внутри цикла `v-for`, для каждой карточки книги.

Найди место в `<div class="book-card">` (например, после цены) и добавь следующий код:
```html
        <p><strong>Цена:</strong> {{ book.price }} руб.</p>

        <p v-if="!book.inStock" class="out-of-stock">
          Нет в наличии
        </p>
        <p v-else class="in-stock">
          В наличии
        </p>
```

**Разбор:**
* `v-if="!book.inStock"`: Этот параграф будет отображен только если `book.inStock` ложно (`false`). Восклицательный знак `!` означает "НЕ".
* `v-else`: Этот параграф будет отображен, если условие в `v-if` (т.е. `!book.inStock`) ложно, что означает, что `book.inStock` истинно (`true`).
* Мы также добавили классы `out-of-stock` и `in-stock` для возможной стилизации.

Добавь стили для этих классов в секцию `<style>`:

```css
/* ... твои существующие стили ... */
.out-of-stock {
  color: red;
  font-weight: bold;
}

.in-stock {
  color: green;
}
```

Сохрани файл. Теперь для книги "JavaScript для Профессионалов" (у которой `inStock: false`) должно появиться красное сообщение "Нет в наличии", а для остальных - зеленое "В наличии".

### Шаг 4: Проверьте результат и поэкспериментируйте!
* Убедись, что все книги отображаются корректно, и сообщение о наличии соответствует значению `inStock` для каждой книги.
* **Эксперимент:** В `data()`, измени значение `inStock` для одной из книг (например, для первой сделай `false`, а для второй `true`). Сохрани файл и посмотри, как динамически изменится сообщение о наличии в браузере. Это демонстрирует мощь связки `v-for` и `v-if` с реактивными данными!


<br>

## 📝 Итоговый примерный код App.vue:

```vue
<template>
  <div id="book-catalog-app">
    <h1>{{ pageTitle }}</h1>
    <div class="book-list">
      <div v-for="book in books" :key="book.id" class="book-card">
        <img :src="book.coverImageUrl" :alt="'Обложка книги ' + book.title" class="book-cover">
        <h3>{{ book.title }}</h3>
        <p><strong>Автор:</strong> {{ book.author }}</p>
        <p><strong>Цена:</strong> {{ book.price }} руб.</p>
        <p v-if="!book.inStock" class="out-of-stock">
          Нет в наличии
        </p>
        <p v-else class="in-stock">
          В наличии
        </p>
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
      books: [
        { id: 1, title: 'Vue.js: Путь к Мастерству', author: 'Елена Разработчикова', price: 1500, coverImageUrl: '[https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial](https://placehold.co/120x180/42b983/ffffff?text=VueMastery&font=arial)', inStock: true },
        { id: 2, title: 'JavaScript для Профессионалов', author: 'Иван Кодер', price: 1250, coverImageUrl: '[https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial](https://placehold.co/120x180/f0db4f/000000?text=JSPro&font=arial)', inStock: false },
        { id: 3, title: 'HTML и CSS: Современные Техники', author: 'Ольга Верстальщица', price: 990, coverImageUrl: '[https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial](https://placehold.co/120x180/e34f26/ffffff?text=HTMLCSS&font=arial)', inStock: true }
      ]
    };
  }
}
</script>

<style>
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
  width: 200px;
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
h3 { margin: 10px 0 5px; font-size: 1.1em; line-height: 1.2; min-height: 2.4em; /* Для выравнивания высоты заголовков */ }
p { margin: 5px 0; font-size: 0.9em; }
.out-of-stock { color: red; font-weight: bold; }
.in-stock { color: green; }
</style>
```

<br>

## 🤔 Что мы сделали:
* Создали массив объектов `books` для представления списка товаров.
* Использовали `v-for` для итерации по этому массиву и отображения каждой книги.
* Вспомнили и применили важность атрибута `:key`.
* Использовали `v-if` и `v-else` для условного отображения информации о наличии товара.

Это большой шаг вперед! Теперь ты умеешь работать не только с одиночными данными, но и с целыми коллекциями, а также управлять их отображением в зависимости от условий. В `TASK.MD` будут задания для закрепления.

---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](./THEORY.md) | [Вперед ➡️](./TASK.md)** 

</div>
