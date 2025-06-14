# Тема 02: Практика 💻 Динамические атрибуты, классы и стили
> <p align=center> 🛠️ Практика </p>

В теоретической части мы изучили директиву `v-bind` (и её сокращение `:`) для динамического управления HTML-атрибутами, CSS-классами и инлайн-стилями. Пришло время применить эти знания на практике! Мы начнем формировать карточку для книги из нашего будущего каталога.

<br>

## 🎯 Цель практики:

1.  Добавить в `App.vue` данные об одной книге (название, автор, URL обложки, признак "новинка").
2.  Отобразить информацию о книге в шаблоне.
3.  Динамически установить путь к обложке книги, используя привязку к атрибуту `:src`.
4.  Динамически изменять цвет названия книги в зависимости от того, является ли она "новинкой", используя привязку `:style` или `:class`.

<br>

## 🛠️ Подготовка:

1.  Убедись, что твой проект (например, `my-bookstore-app`), над которым мы работали в **Теме 01**, открыт в VS Code.
2.  Запусти сервер для разработки, если он еще не запущен:
    ```bash
    npm run serve
    ```
3.  Открой в браузере страницу твоего приложения (обычно `http://localhost:8080/`). Ты должен видеть результат работы из предыдущей практики.

<br>

## 🚀 Шаги выполнения:

### Шаг 1: Добавляем данные о книге в `App.vue`

Сейчас наш `App.vue` содержит общую информацию. Давай добавим в него объект, представляющий одну книгу.

Открой файл `src/App.vue` и модифицируй секцию `<script>` (а именно, функцию `data()`) следующим образом:

```vue
<script>
export default {
  name: 'App',
  data() {
    return {
      // Данные из предыдущей практики (можно оставить или изменить)
      pageTitle: 'Мой Книжный Магазин',
      greetingMessage: 'Добро пожаловать в наш каталог!',
      storeSlogan: 'Книги для каждого!',
      foundingYear: 2024,

      // 👇 Добавляем информацию о книге 👇
      book: {
        id: 1,
        title: 'Vue.js: Полное руководство',
        author: 'Алекс Программист',
        // Используем сервис для временных изображений-заглушек
        coverImageUrl: 'https://placehold.co/150x220?text=Обложка+Книги&font=arial',
        price: 1250.50,
        isNewRelease: true // Признак "новинки"
      }
    };
  }
}
</script>
```

**Что мы сделали:**

* Мы добавили новое свойство `book` в объект, возвращаемый функцией `data()`.
* `book` – это объект, содержащий различную информацию о книге, включая `title`, `author`, `coverImageUrl` (URL для изображения обложки) и булево свойство `isNewRelease`.

<br>

### Шаг 2: Отображаем информацию о книге в шаблоне

Теперь давай выведем эту информацию на страницу. Измени секцию `<template>` в `App.vue`:

```vue
<template>
  <div id="my-bookstore-app"> <h1>{{ pageTitle }}</h1>
    <p>{{ greetingMessage }}</p>
    <hr> <div class="book-card">
      <h2>{{ book.title }}</h2>
      <p>Автор: {{ book.author }}</p>
      <p>Цена: {{ book.price }} руб.</p>
      </div>

    <hr>
    <p>{{ storeSlogan }} (c) {{ foundingYear }}</p>
  </div>
</template>
```

Сохрани файл. В браузере ты должен увидеть название, автора и цену книги. Обложки пока нет.

<br>

### Шаг 3: Динамические привязка обложки книги (`:src`)

Добавим изображение обложки. Внутри `div` с классом `book-card` (например, перед названием книги или после цены) вставь тег `<img>`:

```html
<img :src="book.coverImageUrl" :alt="'Обложка книги ' + book.title">
      <h2>{{ book.title }}</h2>
```

**Что здесь важно:**
* Мы используем `:src="book.coverImageUrl"`. Это сокращение от `v-bind:src`. Vue возьмет значение свойства `book.coverImageUrl` из `data()` и подставит его в атрибут `src` тега `<img>`.
* Мы также использовали `:alt` для динамического формирования текста для атрибута `alt`, что является хорошей практикой для доступности.

Сохрани файл. Теперь ты должен увидеть изображение-заглушку на странице!

<div align=center> 
<img src='../static/02/book.png' style='border:3px solid #4D5D53; border-radius:10px;box-shadow: 0px 4px 8px 0px rgba(77, 93, 83, 0.36);' />
</div>

<br>

### Шаг 4: Динамическое изменение стиля названия книги

Давай сделаем так, чтобы название книги выделялось цветом, если это новинка (`book.isNewRelease === true`).

#### Вариант А: Используя `:style` (для простого инлайн-стиля)

Измени строку с названием книги (`<h2>`) следующим образом:

```html
      <h2 :style="{ color: book.isNewRelease ? 'green' : 'inherit' }">{{ book.title }}</h2>
```

**Разбор:**
* `:style` позволяет привязать объект JavaScript к инлайн-стилям элемента.
* `{ color: book.isNewRelease ? 'green' : 'inherit' }:
  * `color`: CSS-свойство, которое мы хотим изменить.
  * `book.isNewRelease ? 'green' : 'inherit'`: это терарный оператор JavaScript.
    * Если `book.isNewRelease` истинно (`true`), то цвет текста `green`.
    * Иначе (если `false`), цвет текста будет унаследован (`inherit`) от родительского элемента (или будет черным по умолчанию, если не задано иное).

Сохрани файл. Если `book.isNewRelease` установлено в `true` в `data()`, название книги должно стать зеленым.

#### Вариант Б. Используя `:class` (более гибкий и рекомендуемый для состояний)

Этот способ часто предпочтительнее, так как он отделяет логику стилизации от JavaScript и переносит её в CSS.

1. **Добавь CSS-класс в секцию `<style>` файла `App.vue`:**

```vue
<style>
/* ... твои существующие стили ... */

.new-release-title {
  color: green;
  font-weight: bold; /* Например, сделаем еще и жирным */
}

.book-card img { /* Небольшой стиль для обложки */
  max-width: 100px; /* Чтобы обложка не была слишком большой */
  display: block;
  margin: 0 auto 10px; /* Центрируем и добавляем отступ снизу */
}
</style>
```

2. **Примени этот класс к названию книги (`<h2>`) условно:** Если ты пробовал Вариант А, верни `<h2>` к исходному виду (`<h2>{{ book.title }}</h2>`), а затем измени его так:

```html
<h2 :class="{ 'new-release-title': book.isNewRelease }">{{ book.title }}</h2>
```

  **Разбор:**
    * `:class` здесь принимает объект.
    * `{ 'new-release-title': book.isNewRelease }`:
      * `new-release-title`: имя CSS-класса, который мы хотим применить.
      * `book.isNewRelease`: условие. Если оно `true`, класс `new-release-title` будет добавлен к элементу `<h2>`. Если `false`, класс не будет добавлен.

Сохрани файл. Результат должен быть таким же (зеленое и жирное название, если `isNewRelease: true`).

<br>

### Шаг 5: Проверьте результат и поэкспериментируйте!

* Убедись, что обложка отображается и название книги меняет стиль.
* **Эксперимент**: Открой `App.vue` и измени значение `book.isNewRelease` в `data()` с `true` на `false` (или наоборот). Сохрани файл. Стиль названия книги должен автоматически измениться в браузере без перезагрузки страницы! Это и есть реактивность Vue в действии с привязкой классов/стилей.
* Попробуй изменить URL в `book.coverImageUrl` на другой (например, `https://placehold.co/150x220/ff0000/ffffff?text=Другая+Обложка`) и посмотри, как обновится изображение.

<br>

## 📝 Итоговый примерный код `App.vue` (с использованием `:class`):

```vue
<template>
  <div id="my-bookstore-app">
    <h1>{{ pageTitle }}</h1>
    <p>{{ greetingMessage }}</p>
    <hr>

    <div class="book-card">
      <img :src="book.coverImageUrl" :alt="'Обложка книги ' + book.title">
      <h2 :class="{ 'new-release-title': book.isNewRelease }">{{ book.title }}</h2>
      <p>Автор: {{ book.author }}</p>
      <p>Цена: {{ book.price }} руб.</p>
    </div>

    <hr>
    <p>{{ storeSlogan }} (c) {{ foundingYear }}</p>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      pageTitle: 'Мой Книжный Магазин',
      greetingMessage: 'Добро пожаловать в наш каталог!',
      storeSlogan: 'Книги для каждого!',
      foundingYear: 2024,
      book: {
        id: 1,
        title: 'Vue.js: Полное руководство',
        author: 'Алекс Программист',
        coverImageUrl: '[https://placehold.co/150x220?text=Обложка+Книги&font=arial](https://placehold.co/150x220?text=Обложка+Книги&font=arial)',
        price: 1250.50,
        isNewRelease: true
      }
    };
  }
}
</script>

<style>
#my-bookstore-app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px; /* Немного уменьшил отступ */
  padding: 20px;
}

h1 {
  color: #42b983;
}

.book-card {
  border: 1px solid #eee;
  padding: 15px;
  margin: 15px auto;
  max-width: 300px; /* Ограничим ширину карточки */
  box-shadow: 2px 2px 5px rgba(0,0,0,0.1);
  border-radius: 5px;
}

.book-card img {
  max-width: 100px;
  display: block;
  margin: 0 auto 10px;
  border: 1px solid #ddd;
}

.new-release-title {
  color: green;
  font-weight: bold;
}

hr {
  margin: 20px 0;
}
</style>
```

<br>

## 🤔 Что мы сделали:

* Добавили структурированные данные (объект `book`) в `data()`.
* Научились динамически устанавливать атрибут `src` для изображений.
* Рассмотрели два способа условного применения стилей: через `:style` и через `:class`.

Отличная работа! Эти навыки очень важны для создания интерактивных интерфейсов. В `TASK.md` тебя ждут задания для самостоятельного закрепления.

---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](./THEORY.md) | [Вперед ➡️](./TASK.md)** 

</div>
