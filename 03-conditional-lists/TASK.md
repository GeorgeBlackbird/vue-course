# Тема 03: Задания для самостоятельной работы ✍️
> <p align=center> ✏️ Самостоятельная работа </p>

Ты отлично поработал с отображением списков и условным рендерингом на практике! Теперь давай закрепим эти знания и немного углубимся в детали.

<br>

## 🎯 Цели заданий:

* Применить директиву `v-if` для отображения условного элемента внутри цикла `v-for`.
* Углубить понимание различий между директивами `v-if` и `v-show` и их сценариев использования.

<br>

## 🛠️ Подготовка:

* В качестве основы используй код из файла `App.vue`, который у тебя получился после выполнения `PRACTICE.MD` для **Темы 03** (со списком книг).
* Убедись, что сервер разработки (`npm run serve`) запущен.

<br>


## Задание 1: Ярлык "Бестселлер" 🌟

Давай добавим к нашим книгам ярлык "Бестселлер!", если книга действительно является таковой.

1.  **Обнови данные о книгах:**
    * В файле `App.vue`, в методе `data()`, модифицируй каждый объект в массиве `books`. Добавь к каждому объекту книги новое булево свойство `isBestseller`.
    * Для одной-двух книг установи значение `isBestseller: true`, для остальных — `false`.

2.  **Отобрази ярлык "Бестселлер!":**
    * В секции `<template>`, внутри цикла `v-for` (там, где отображается каждая `book-card`), найди место рядом с названием книги (`<h3>{{ book.title }}</h3>`).
    * Добавь небольшой элемент (например, `<span>`) с текстом "🔥 Бестселлер!" (или просто "Бестселлер!").
    * Используй директиву `v-if` так, чтобы этот `<span>` с ярлыком отображался **только если** свойство `isBestseller` для текущей книги (`book.isBestseller`) имеет значение `true`.

3.  **(Опционально) Добавь стили для ярлыка:**
    * В секцию `<style>` добавь немного CSS для класса, который ты можешь присвоить этому `<span>`, чтобы он выглядел как ярлык (например, другой цвет, небольшой фон, отступы).
    * Не забудь присвоить этот класс своему `<span>`.

**Что должно получиться?**
* У тех книг, для которых `isBestseller` установлено в `true`, рядом с названием должен появиться ярлык "🔥 Бестселлер!".
* У остальных книг этого ярлыка быть не должно.
* Проверь, изменяя значения `isBestseller` в `data()` для разных книг.

<br>

## Задание 2: Теоретический вопрос — `v-if` vs `v-show` 🤔

Ответь на следующий вопрос, основываясь на материале из `THEORY.MD` этой темы:

**Вопрос:** В чем заключается основное различие в работе директив `v-if` и `v-show`? В каких случаях предпочтительнее использовать каждую из них, и почему?

**На что обратить внимание в ответе:**
* Объясни, как каждая директива влияет на DOM-дерево.
* Укажи на разницу в "стоимости" первоначального рендеринга и "стоимости" переключения состояния (показать/скрыть).
* Приведи рекомендации, когда выбор одной директивы будет более оправдан по сравнению с другой.

---
### 💡 Подсказки:

* **Для Задания 1:**
    * Элемент `<span>` отлично подходит для небольших текстовых вставок, таких как ярлыки.
    * Условие для `v-if` будет выглядеть как `v-if="book.isBestseller"`.
* **Для Задания 2:**
    * Вспомни, какая из директив действительно удаляет и воссоздает элементы, а какая просто играет с их CSS-свойством `display`.
    * Подумай, что эффективнее, если элемент нужно часто показывать и скрывать, а что — если условие меняется редко.

<br>

## ✨ Как проверить себя:

* **Для Задания 1:**
    * [ ] Свойство `isBestseller` добавлено ко всем объектам книг в массиве `books`.
    * [ ] Ярлык "🔥 Бестселлер!" (или аналогичный) отображается только у тех книг, где `isBestseller: true`.
    * [ ] Если изменить значение `isBestseller` для книги в `data()` и сохранить, отображение ярлыка корректно обновляется.

* **Для Задания 2:**
    * [ ] Твой ответ четко разделяет `v-if` (условное создание/удаление из DOM) и `v-show` (переключение `display: none`).
    * [ ] Ты объяснил(а), что `v-if` "ленивый" и имеет более высокие затраты на переключение, а `v-show` имеет более высокие затраты на начальный рендеринг, но дешевле в переключении.
    * [ ] Ты привел(а) корректные рекомендации по выбору директивы в зависимости от частоты изменения условия.



Отличная работа! Умение правильно использовать `v-if`, `v-show` и `v-for` с `key` — это основа для создания сложных и производительных интерфейсов на Vue.

> В следующей теме мы будем разбираться с обработкой пользовательских событий! 🖱️👆

---


<div align=center style="display:flex;justify-content:center;"> 

**[⬅️ Назад](./PRACTICE.md) | [Вперед ➡️](../04-event-handling/THEORY.md)** 

</div>