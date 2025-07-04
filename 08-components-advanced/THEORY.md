# Тема 08: 🧩 Компоненты: События, Слоты и Динамические компоненты
> <p align=center> 📑 Теория </p>

Привет! В предыдущей теме мы научились создавать базовые компоненты и передавать в них данные с помощью `props`. Теперь мы углубимся в более продвинутые концепции, которые позволяют компонентам "общаться" друг с другом, гибко настраивать их содержимое и даже динамически переключаться между ними. Речь пойдет о пользовательских событиях, слотах, динамических компонентах и `<keep-alive>`.

<br>

## ⬆️ Общение дочернего компонента с родительским: Генерация событий (`$emit`)

Мы знаем, что `props` позволяют передавать данные **от родителя к дочернему компоненту** (однонаправленный поток). Но что если дочернему компоненту нужно сообщить что-то родителю? Например, пользователь нажал кнопку внутри дочернего компонента, и родитель должен на это отреагировать.

Для этого дочерние компоненты могут **генерировать (emit) пользовательские события**. Родительский компонент может прослушивать эти события так же, как и нативные DOM-события, используя директиву `v-on` (или `@`).

**Как это работает:**

1.  **В дочернем компоненте:** Используется встроенный метод `this.$emit()`.
    * Первый аргумент — это **имя события** (строка, обычно в kebab-case или camelCase).
    * Последующие аргументы (опционально) — это **полезная нагрузка (payload)**, то есть данные, которые мы хотим передать вместе с событием.

    ```js
    // BookCard.vue - <script>
    // ...
    methods: {
      notifyParentAboutAddToCart() {
        // Генерируем событие 'add-to-cart' и передаем объект книги как данные
        this.$emit('add-to-cart', this.book); 
        alert(`Событие "add-to-cart" для книги "${this.book.title}" сгенерировано!`);
      }
    }
    // ...
    ```

    ```html
    <button @click="notifyParentAboutAddToCart">В корзину из компонента</button>
    ```

2.  **В родительском компоненте:** Прослушиваем это событие на теге дочернего компонента.

    ```html
    <BookCard 
      v-for="bookItem in filteredBooks" 
      :key="bookItem.id" 
      :book="bookItem"
      @add-to-cart="handleAddToCartInApp" 
    />
    ```

    ```js
    // App.vue - <script>
    // ...
    methods: {
      handleAddToCartInApp(bookFromCard) { // Аргумент - это данные, переданные с событием
        console.log('(App.vue) Получено событие add-to-cart для книги:', bookFromCard.title);
        // Здесь могла бы быть логика добавления книги в корзину (массив в data() App.vue)
        alert(`(App.vue) Книга "${bookFromCard.title}" получена для добавления в корзину!`);
      }
    }
    // ...
    ```

**Объявление генерируемых событий (Vue 3+):**
Хорошей практикой (и обязательно для некоторых случаев, например, при использовании `v-model` на компоненте) является объявление событий, которые компонент может генерировать, с помощью опции `emits`:

```js
// BookCard.vue - <script>
export default {
  name: 'BookCard',
  props: { /* ... */ },
  emits: ['add-to-cart', 'show-details'], // Массив имен событий
  // Или объект для валидации:
  // emits: {
  //   'add-to-cart': (payload) => {
  //     // можно валидировать payload
  //     return payload && payload.id;
  //   }
  // },
  methods: { /* ... */ }
}
</script>
```

> ❗ Это улучшает читаемость кода и документирует интерфейс компонента.

<br>

## 🎁 Распределение контента с помощью слотов (`<slot>`)

Иногда нам нужно создать компонент, который служит "оберткой" или "шаблоном", но его содержимое должно определяться родительским компонентом. Для этого используются **слоты.**

**Слот** — это как заполнитель в шаблоне дочернего компонента, куда родитель может вставить свою разметку.

<br>

1. **Слот по умолчанию (безымянный)**

Если в компоненте есть только один слот без имени, он называется слотом по умолчанию.

  * **Определение в дочернем компоненте (MyWrapper.vue):**

  ```html
  <div class="wrapper-style">
    <h4>Заголовок обертки</h4>
    <slot>
      <p>Это текст по умолчанию для слота.</p>
    </slot>
    <footer>Подвал обертки</footer>
  </div>
  ```

  * **Использование в родительском компоненте:**
  Весь контент, помещенный между открывающим и закрывающим тегами компонента `<MyWrapper>`, будет вставлен в `<slot>`.

  ```html
  <MyWrapper>
    <p>Этот абзац будет вставлен в слот компонента MyWrapper.</p>
    <button>И эта кнопка тоже!</button>
  </MyWrapper>

  <MyWrapper />
  ```

<br>

2. **Именованные слоты**

Если компонент должен иметь несколько мест для вставки контента, используются именованные слоты.

  * **Определение в дочернем компоненте (`PageLayout.vue`):**

  ```html
  <div class="page-container">
    <header>
      <slot name="header"></slot>
    </header>
    <main>
      <slot></slot> </main>
    <footer>
      <slot name="footer">
        <p>© 2025 Моя Компания (подвал по умолчанию)</p>
      </slot>
    </footer>
  </div>
  ```

  * **Предоставление контента из родительского компонента:**
  Используется директива `v-slot` на теге `<template>` (или сокращение `#`).

  ```html
  <PageLayout>
    <template v-slot:header> <h1>Заголовок моей страницы</h1>
    </template>

    <p>Основное содержимое страницы...</p> 
    <template #footer> <p>Мой кастомный подвал страницы.</p>
    </template>
  </PageLayout>
  ```

<br>

3. **Слоты с ограниченной областью видимости (Scoped Slots)**

Иногда контенту слота, который определяется в родителе, нужен доступ к данным, которые есть только в дочернем компоненте (и не передаются как props). В этом случае используются слоты с ограниченной областью видимости.

Дочерний компонент может передавать данные в свой слот как атрибуты:

  * **Определение в дочернем компоненте (`UserList.vue`):**

  ```html
  <ul>
    <li v-for="user in users" :key="user.id">
      <slot :userFromChild="user" :isAdmin="user.role === 'admin'">
        {{ user.name }} </slot>
    </li>
  </ul>
  ```


  ```js
  // UserList.vue - <script>
  export default {
    data() {
      return {
        users: [
          { id: 1, name: 'Алиса', role: 'user' },
          { id: 2, name: 'Борис', role: 'admin' }
        ]
      };
    }
  }
  </script>
  ```

  * **Получение данных в родительском компоненте:**
  Используем `v-slot` со значением, которое будет объектом, содержащим переданные данные.

  ```html
  <UserList>
    <template v-slot:default="slotProps"> <strong>{{ slotProps.userFromChild.name }}</strong>
      <span v-if="slotProps.isAdmin"> (Администратор)</span>
      <em> - Роль: {{ slotProps.userFromChild.role }}</em>
    </template>
  </UserList>

  <UserList>
    <template #default="{ userFromChild, isAdmin }">
      <strong>{{ userFromChild.name }}</strong>
      <span v-if="isAdmin"> (Администратор)</span>
    </template>
  </UserList>
  ```

> 📗 Scoped slots делают компоненты невероятно гибкими, позволяя родительскому компоненту полностью контролировать отображение частей дочернего компонента, используя при этом данные самого дочернего компонента.

<br>

## 🔄 Динамические компоненты: `<component :is="...">`

Иногда нужно переключаться между разными компонентами во время выполнения, не зная заранее, какой именно компонент будет отображен. Для этого Vue предоставляет специальный мета-компонент `<component>`.

Его атрибут `:is` может быть связан с:

* Именем зарегистрированного компонента (строка).
* Или непосредственно с объектом импортированного компонента.

**Пример (вкладки):**

```html
<div>
  <button @click="activeTab = 'TabA'">Вкладка A</button>
  <button @click="activeTab = 'TabB'">Вкладка B</button>
  <button @click="activeTab = 'TabC'">Вкладка C</button>

  <component :is="currentTabComponent"></component>
</div>
```

```js
// Parent.vue - <script>
import TabA from './components/TabA.vue';
import TabB from './components/TabB.vue';
import TabC from './components/TabC.vue';

export default {
  components: { TabA, TabB, TabC }, // Можно и не регистрировать, если передавать объект компонента
  data() {
    return {
      activeTab: 'TabA' // Или имя компонента как строка 'TabA'
    };
  },
  computed: {
    currentTabComponent() {
      // Если activeTab - строка с именем компонента, зарегистрированного локально или глобально
      // return this.activeTab; 
      // Или, если activeTab - ключ, а компоненты импортированы:
      if (this.activeTab === 'TabA') return TabA;
      if (this.activeTab === 'TabB') return TabB;
      if (this.activeTab === 'TabC') return TabC;
      return null;
    }
  }
}
</script>
```

<br>

## 🧠 Сохранение состояния динамических компонентов: `<keep-alive>`

По умолчанию, когда вы переключаетесь между динамическими компонентами с помощью `<component :is="...">` (или при использовании `v-if`), Vue **уничтожает** старый компонент и **создает** новый. Это означает, что любое состояние старого компонента (например, введенный текст в поля формы, позиция скролла) будет потеряно.

Если вы хотите, чтобы состояние неактивных компонентов **сохранялось (кэшировалось)**, их можно обернуть во встроенный компонент `<keep-alive>`:

```html
<keep-alive>
  <component :is="currentTabComponent"></component>
</keep-alive>
```

Теперь, при переключении вкладок, состояние компонентов `TabA`, `TabB`, `TabC` будет сохраняться.

### Хуки жизненного цикла для `<keep-alive>`:
Компоненты внутри `<keep-alive>` получают два дополнительных хука жизненного цикла:
* `activated`: вызывается при активации кэшированного компонента.
* `deactivated`: вызывается при деактивации (когда компонент убирается из DOM, но остается в кэше).

### Атрибуты `<keep-alive>` (кратко):
* `include`: Строка или RegExp. Только компоненты с именами, соответствующими этому, будут кэшироваться.
* `exclude`: Строка или RegExp. Любой компонент с совпадающим именем не будет кэшироваться.
* `max`: Максимальное количество экземпляров компонентов для кэширования.

<br>

## ✨ Итог

В этой теме мы рассмотрели продвинутые, но очень важные аспекты работы с компонентами:

* **Генерация событий (`$emit`):** для коммуникации от дочернего компонента к родительскому.
* **Слоты (`<slot>`):** для гибкого распределения контента, делая компоненты более переиспользуемыми (обычные, именованные, scoped).
* **Динамические компоненты (`<component :is="...">`):** для переключения между компонентами во время выполнения.
* `<keep-alive>:` для сохранения состояния (кэширования) динамических компонентов или компонентов, управляемых `v-if`.

Эти инструменты значительно расширяют ваши возможности по созданию сложных, интерактивных и хорошо структурированных приложений на Vue.js.

<br>

## 🔗 Полезные ссылки

* **Пользовательские события (Component Events):** https://ru.vuejs.org/guide/components/events.html
* **Слоты (Slots):** https://ru.vuejs.org/guide/components/slots.html
* **Динамические компоненты и `<keep-alive>`:** https://ru.vuejs.org/guide/built-ins/keep-alive.html и https://ru.vuejs.org/guide/essentials/component-basics.html#dynamic-components

> В практической части мы научим наш компонент `BookCard` генерировать события и поэкспериментируем со слотами!

---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](../07-components-basics/TASK.md) | [Вперед ➡️](./PRACTICE.md)** 

</div>