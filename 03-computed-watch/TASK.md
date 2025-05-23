# 🧠 Самостоятельная работа — Тема 3. Computed и Watch

---

## 📍 Что делать?

Эти задания помогут закрепить работу с вычисляемыми свойствами и наблюдателями. Мы добавим фильтр по статусу задач и научимся отслеживать изменения в списке.

---

## ✅ Задание 1: Фильтр по статусу задач

Добавь возможность выбирать, какие задачи отображать: все, только выполненные, или только невыполненные.

### Что нужно сделать:

1. Добавь переменную для хранения выбранного фильтра (например, `'all'`, `'done'`, `'active'`).
2. Размести на странице кнопки, по нажатию на которые фильтр будет изменяться.
3. Напиши метод, который будет менять фильтр при клике на кнопку.
4. Измени вычисляемое свойство `filteredTasks`, чтобы:
   - сначала отфильтровать задачи по статусу
   - затем применить поиск по названию

💡 Подсказка:
- Для фильтрации можно использовать `.filter()`
- Метод `.includes()` поможет при поиске
- Не обязательно всё писать сразу — двигайся поэтапно

---

## ✅ Задание 2: Наблюдение за изменением задач

Настрой `watch`, который будет отслеживать изменения в списке задач.

### Шаги:

1. Добавь наблюдатель за `tasks`
2. Проверь: при добавлении или удалении задачи в консоли должно выводиться сообщение

---

## ✅ Задание 3 (по желанию): Подсветка активной кнопки фильтра

Выдели кнопку активного фильтра — добавь простую стилизацию.

---

## 🧭 Куда дальше?

📘 Переходи к теме 4: События и методы
← Назад к практике