# Тема 07: 🧩 Компоненты: Основы

> <p align=center> 📑 Теория </p>

Привет! До сих пор мы работали в основном внутри одного компонента `App.vue`. Но по мере роста приложения такой подход становится неудобным. Чтобы создавать сложные интерфейсы, Vue использует **компонентный подход**. Это одна из ключевых и самых мощных концепций фреймворка.

<br>

## Что такое Компоненты? 🧩

Представь, что ты собираешь конструктор LEGO. У тебя есть разные типы кирпичиков: маленькие, большие, колеса, окна и т.д. Каждый кирпичик имеет свою форму и назначение, и ты можешь использовать их многократно для создания сложных моделей.

**Компоненты во Vue — это как эти кирпичики LEGO для твоего пользовательского интерфейса.**

**Компонент** — это независимый, переиспользуемый блок кода, который инкапсулирует в себе:
* **HTML-разметку (`<template>`)**: Как он выглядит.
* **JavaScript-логику (`<script>`)**: Как он себя ведет (данные, методы, вычисляемые свойства, наблюдатели и т.д.).
* **CSS-стили (`<style>`)**: Как он оформлен (опционально, стили могут быть и глобальными).

По сути, компонент — это маленький "экземпляр Vue" со своей собственной, изолированной областью видимости. Ты можешь думать о компонентах как о **пользовательских HTML-элементах**, которые ты сам создаешь. Например, вместо обычного `<button>`, ты можешь создать компонент `<StyledButtonWithIcon>` со своей логикой и стилями.

> 📗 Весь интерфейс Vue-приложения обычно представляет собой дерево вложенных друг в друга компонентов.

<br>

## 👍 Преимущества компонентного подхода

Использование компонентов дает **множество преимуществ**:

1.  **Переиспользование (Reusability)**: Написав компонент один раз (например, карточку товара, кнопку с иконкой, поле ввода с валидацией), ты можешь использовать его в разных частях приложения сколько угодно раз.
2.  **Поддерживаемость (Maintainability)**: Маленькие, изолированные компоненты легче понимать, отлаживать и изменять, чем один огромный файл с кодом. Если нужно что-то поправить в карточке товара, ты идешь в код компонента карточки товара.
3.  **Масштабируемость (Scalability)**: Большие и сложные приложения легче разрабатывать, разбивая их на управляемые компоненты. Команды могут работать над разными компонентами параллельно.
4.  **Организация и Структура**: Компоненты помогают лучше структурировать проект, делая его более логичным и понятным.

<br>

## 📄 Создание Компонента (SFC - .vue файлы)

Во Vue компоненты чаще всего создаются в виде **Однофайловых Компонентов (Single File Components, SFC)** — это файлы с расширением `.vue`. Ты уже знаком с таким файлом — `App.vue` это тоже компонент!

Стандартная структура SFC (`.vue` файла):
```html
<template>
  <div class="my-component">
    <p>{{ message }}</p>
    <button @click="greet">Нажми</button>
  </div>
</template>

<script>
export default {
  name: 'MyExampleComponent', // Имя компонента (необязательно, но полезно для отладки)
  data() {
    return {
      message: 'Привет из компонента!'
    };
  },
  methods: {
    greet() {
      alert(this.message);
    }
  }
  // props, computed, watch, etc. будут здесь
}
</script>

<style scoped>
/* CSS-стили компонента */
/* `scoped` означает, что стили будут применяться только к этому компоненту */
.my-component {
  border: 1px solid green;
  padding: 10px;
}
p {
  color: green;
}
</style>
```

> 📗 Каждый `.vue` файл инкапсулирует шаблон, логику и стили (если они есть) своего компонента.

<br>

## 📝 Регистрация Компонентов

Прежде чем ты сможешь использовать созданный компонент, его нужно **зарегистрировать**. Есть два способа регистрации: локальная и глобальная.

1. **Локальная регистрация (предпочтительный способ)**

Локальная регистрация означает, что компонент доступен только внутри того родительского компонента, где он был зарегистрирован. Это **рекомендуемый способ** для большинства случаев.

**Как это сделать:**
  1. **Импортируй** компонент в `<script>` родительского компонента.
  2. **Зарегистрируй** его в опции `components` родительского компонента.

**Пример:**

Допустим, у нас есть компонент `BookCard.vue` (мы создадим его на практике). Чтобы использовать его в `App.vue`:

```html
// App.vue (родительский компонент)
<template>
  <div>
    <h1>Мой книжный магазин</h1>
    <BookCard title="Vue для всех" author="А. Автор" />
    <book-card :title="anotherBook.title" :author="anotherBook.author" /> </div>
</template>

<script>
// 1. Импортируем компонент
import BookCard from './components/BookCard.vue'; // Укажи правильный путь к файлу

export default {
  name: 'App',
  // 2. Регистрируем компонент локально
  components: {
    BookCard // Теперь <BookCard> можно использовать в <template> этого компонента
    // Если хочешь использовать другое имя в шаблоне: 'my-book-card': BookCard
  },
  data() {
    return {
      anotherBook: { title: 'Продвинутый Vue', author: 'Б. Профи' }
    };
  }
}
</script>
```

**Преимущества локальной регистрации:**
* **Явные зависимости:** Сразу видно, какие компоненты использует родитель.
* **Лучше для "tree-shaking":** Если компонент не используется, сборщик проекта может его не включать в финальную сборку, уменьшая размер приложения.

2. **Глобальная регистрация (реже)**

Глобально зарегистрированные компоненты доступны в любом компоненте вашего приложения без необходимости их импортировать и локально регистрировать.

Глобальная регистрация обычно выполняется в файле `src/main.js` с помощью метода `app.component()`:

```js
// src/main.js
import { createApp } from 'vue'
import App from './App.vue'

// Импортируем компонент, который хотим зарегистрировать глобально
import MyGlobalComponent from './components/MyGlobalComponent.vue'

const app = createApp(App)

// Глобальная регистрация
app.component('MyGlobalComponent', MyGlobalComponent) // Первый аргумент - имя тега, второй - объект компонента

app.mount('#app')
```

Теперь `<MyGlobalComponent>` можно использовать в любом шаблоне приложения.

**Когда использовать глобальную регистрацию?**
* Для очень общих, базовых компонентов, которые используются очень часто по всему приложению (например, кастомные кнопки, иконки, элементы разметки). **Недостатки:**
  * Может "загрязнять" глобальное пространство имен.
  * Менее очевидно, откуда берется компонент, при чтении шаблона.
  * Может ухудшить "tree-shaking", если не все глобальные компоненты реально используются.

<br>

## 🏷️ Использование Компонентов в Шаблоне

После регистрации компонент используется в шаблоне родителя как обычный HTML-тег:
* Если вы зарегистрировали компонент как `BookCard` (PascalCase), в шаблоне его можно использовать как `<BookCard>` или `<book-card>` (kebab-case). Kebab-case рекомендуется для совместимости с HTML, который нечувствителен к регистру.
* Компоненты могут быть самозакрывающимися, если у них нет содержимого слота: `<BookCard />`.

<br>

## 📥 Передача данных в дочерние компоненты: Входные параметры (Props)

Часто нам нужно передать данные из родительского компонента в дочерний. Например, для нашего `BookCard` нужно передать название книги, автора, цену и т.д. Это делается с помощью **входных параметров (props)**.

### Что такое props?
Props (сокращение от "properties") — это пользовательские атрибуты, которые вы можете зарегистрировать на компоненте. Когда значение передается в такой атрибут, оно становится свойством экземпляра этого компонента.

1. **Объявление props в дочернем компоненте**

В дочернем компоненте (например, `BookCard.vue`) нужно объявить, какие props он ожидает получить. Это делается с помощью опции `props`:
  * **Простой массив строк (имена props):**

    ```js
    // BookCard.vue
    export default {
      name: 'BookCard',
      props: ['title', 'author', 'price']
      // ...
    }
    ```

  * **Объект с валидацией (предпочтительнее для более сложных случаев):**

  Позволяет указать тип данных, значение по умолчанию, и является ли prop обязательным.

  ``` js

  // BookCard.vue
  export default {
    name: 'BookCard',
    props: {
      title: String, // Простая проверка типа
      author: {
        type: String,
        required: true // Этот prop обязателен
      },
      price: {
        type: Number,
        default: 0 // Значение по умолчанию, если prop не передан
      },
      isAvailable: {
        type: Boolean,
        default: true
      },
      tags: { // Для объектов или массивов значение по умолчанию должно возвращаться из функции-фабрики
        type: Array,
        default: () => []
      }
    }
    // ...
  }
  ```

> ❗ Валидация типов очень полезна для отладки и предотвращения ошибок.

<br>

2. **Передача props из родительского компонента**

В шаблоне родительского компонента props передаются как атрибуты:

  * **Статические значения (передаются как строки, если не используется `v-bind`):**
  
    ```html
    <BookCard title="Изучаем Vue" author="Автор Книги" :price="100" :is-available="true" />
    ```

  Обратите внимание: для нестроковых типов (Number, Boolean) или для передачи переменных нужно использовать `v-bind` (или сокращение `:`), даже если значение кажется статическим. Без `v-bind`, `price="100"` передаст строку "100", а не число.

  * **Динамические значения (через `v-bind` или `:`):**

  Это самый частый способ. Мы **связываем** prop с данными родительского компонента.

  ```html
  <BookCard
    :title="myBook.title"
    :author="myBook.author"
    :price="myBook.price"
    :is-available="myBook.inStock"
    :tags="myBook.genreTags"
  />
  ```

  ```js
      // App.vue - data()
      data() {
        return {
          myBook: {
            title: 'Vue.js для Профи',
            author: 'Мастер Vue',
            price: 1990,
            inStock: true,
            genreTags: ['фреймворк', 'javascript', 'веб']
          }
        };
      }
  ```

**Именование props:**

* В JavaScript (при объявлении props) принято использовать `camelCase` (например, `bookTitle`).
<br>
* В HTML-шаблоне (при передаче props) принято использовать `kebab-case` (например, `book-title`). Vue автоматически преобразует `kebab-case` в `camelCase`. `props: ['bookTitle']` <===> `<BookCard book-title="..."/>`

<br>

3. **Использование props в дочернем компоненте**

После объявления и передачи, props становятся доступны внутри дочернего компонента:

* В `<script>`: через `this.имяProp` (например, `this.title`).
* В `<template>`: напрямую `{{ имяProp }}` (например, `{{ title }}`).

```html
// BookCard.vue
<template>
  <div class="book-card-details">
    <h3>{{ title }}</h3>
    <p>Автор: {{ author }}</p>
    <p v-if="price > 0">Цена: {{ price }} руб.</p>
    <p v-if="!isAvailable" style="color: red;">Нет в наличии</p>
    <ul>
      <li v-for="tag in tags" :key="tag">{{ tag }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'BookCard',
  props: {
    title: String,
    author: String,
    price: Number,
    isAvailable: Boolean,
    tags: Array
  },
  mounted() {
    console.log('Название книги (из prop):', this.title);
  }
}
</script>
```

## ➡️ Однонаправленный поток данных (One-Way Data Flow)

Это очень важная концепция во Vue (и многих других фреймворках): **данные передаются от родителя к дочернему компоненту через props только в одном направлении ("сверху вниз").**

**Дочерний компонент НЕ ДОЛЖЕН напрямую изменять (мутировать) полученный prop.**

**Почему?**
* Если бы дочерние компоненты могли менять props, стало бы очень сложно отслеживать, откуда пришли изменения данных, и отлаживать приложение.
* Это нарушило бы четкость потока данных и привело бы к тому, что состояние родителя могло бы меняться непредсказуемо из-за действий дочернего компонента.

**Что делать, если нужно изменить значение, основанное на prop, внутри дочернего компонента?**

1. **Использовать prop как начальное значение для локального свойства `data`:**

  ```js
  props: ['initialCounter'],
  data() {
    return {
      localCounter: this.initialCounter // Используем prop для инициализации
    };
  }
  // Теперь можно изменять localCounter
  ```

2. **Использовать вычисляемое свойство (`computed`) на основе prop:**

    ```js
    props: ['price'],
    computed: {
      priceWithTax() {
        return this.price * 1.2; // Вычисляем новое значение, не меняя prop
      }
    }
    ```

Если дочернему компоненту нужно сообщить родителю об изменении (например, пользователь что-то сделал в дочернем компоненте, и это должно повлиять на данные родителя), для этого используются **пользовательские события (custom events)**, о которых мы поговорим в следующей теме.

<br>

## ✨ Итог

Компоненты — это фундаментальная часть Vue.js.

* Они позволяют разбивать интерфейс на **независимые, переиспользуемые блоки.**
* Регистрируются **локально** (предпочтительно) или **глобально**.
* Данные передаются от родителя к дочернему компоненту через **props**.
* Во Vue действует **однонаправленный поток данных** для props.

Освоение компонентов открывает путь к созданию сложных, хорошо структурированных и легко поддерживаемых Vue-приложений.

<br>

## 🔗 Полезные ссылки

* **Основы компонентов (Component Basics):** https://ru.vuejs.org/guide/essentials/component-basics.html
* **Входные параметры (Props):** https://ru.vuejs.org/guide/components/props.html
* **Регистрация компонентов (Component Registration):** https://ru.vuejs.org/guide/components/registration.html

> В практической части мы создадим наш первый компонент `BookCard` и будем передавать в него данные о книгах!

---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](../06-computed-watchers/TASK.md) | [Вперед ➡️](./PRACTICE.md)** 

</div>