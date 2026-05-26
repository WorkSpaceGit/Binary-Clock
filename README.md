# ∞ Бінарний годинник

Мінімальний бінарний годинник для iPhone. Один файл. Без залежностей. Без фреймворків.

---

## Філософія

Мета була не побудувати додаток-годинник — а побудувати *найменш можливий* годинник, який все одно працює красиво. Кожен рядок коду або виправдовує своє місце, або вирізається.

Темний фон. Зелені біти. Більше нічого.

---

## Що всередині

Один `index.html` який робить все:

- відображає BCD бінарний годинник (години / хвилини / секунди)
- встановлюється як PWA через inline-маніфест згенерований з JS
- реєструє Service Worker через Blob URL — жодних зайвих файлів
- генерує іконку `∞` під час запуску через Canvas, яка перевикористовується для favicon, apple-touch-icon і маніфесту
- запитує Screen Wake Lock щоб екран не вимикався
- враховує чубчик і home indicator iPhone через `safe-area-inset` відступи

---

## Рішення щодо продуктивності

Кожен вибір зроблений з урахуванням CPU та батареї мобільного.

**Бітова математика замість рядків**
Кожна цифра декодується побітовим зсувом `(digit >> (len - 1 - j)) & 1` — нуль алокацій рядків на кожен тік.

**Diff-рендеринг**
`bitState[]` зберігає попередній стан кожного біта. DOM торкається лише коли біт дійсно змінився — не кожної секунди безумовно.

**`border` замість `box-shadow` для активних бітів**
`box-shadow` запускає перерахунок layout і повний repaint. Зміна `border-color` — чистий composite, найдешевша можлива візуальна операція на мобільному GPU.

**Без transitions**
Перемикання миттєве. Естетика цифрового сигналу, нульова вартість анімації.

**`requestAnimationFrame` при старті**
Годинник чекає поки браузер завершить перший layout-прохід перед рендером — жодного зайвого перерахунку при завантаженні.

**Синхронізований `setTimeout`**
`1000 - (Date.now() % 1000)` вирівнює кожен тік по системному годиннику. Без дрейфу, без зайвих викликів.

**CSS `contain: paint`**
Кожен біт ізольований — repaint одного біта не каскадує на сусідні елементи.

**Поведінка Safari з батареєю збережена навмисно**
Коли додаток згорнутий, Safari заморожує таймери. Це нормальна системна поведінка і залишена без змін — батарея важливіша за фоновий тік.

---

## Встановлення на iPhone

1. Розмісти файл будь-де (GitHub Pages, локальний сервер тощо)
2. Відкрий у Safari
3. Поділитися → На екран «Домів»

Wake Lock і PWA повноцінно працюють лише при запуску з головного екрану.

---

## Розмір

`~5 КБ`. Без збірки. Без node_modules. Відкрив — і готово.

# ∞ Binary Clock

A minimal binary clock for iPhone. One file. No dependencies. No frameworks.

---

## Philosophy

The goal was not to build a clock app — it was to build the *least possible* clock that still works beautifully. Every line of code either earns its place or gets cut.

Dark background. Green bits. Nothing else.

---

## What's inside

A single `index.html` that does everything:

- renders a BCD binary clock (hours / minutes / seconds)
- installs as a PWA via inline manifest generated from JS
- registers a Service Worker via Blob URL — no extra files
- generates the `∞` icon at runtime via Canvas, reused for favicon, apple-touch-icon and manifest
- requests Screen Wake Lock so the display stays on
- respects iPhone notch and home indicator via `safe-area-inset` padding

---

## Performance decisions

Every choice was made with mobile CPU and battery in mind.

**Bit-level math instead of string parsing**
Each digit is decoded with a bitwise shift `(digit >> (len - 1 - j)) & 1` — zero string allocations per tick.

**Diff rendering**
`bitState[]` tracks the previous state of every bit. The DOM is only touched when a bit actually changes — not on every second unconditionally.

**`border` instead of `box-shadow` for active bits**
`box-shadow` triggers layout recalculation and full repaint. A `border-color` swap is pure composite — the cheapest possible visual change on mobile GPU.

**No transitions**
Switching is instant. Digital signal aesthetics, zero animation cost.

**`requestAnimationFrame` for cold start**
The clock waits for the browser to finish its first layout pass before rendering — no wasted recalculation on load.

**Synchronized `setTimeout`**
`1000 - (Date.now() % 1000)` aligns each tick to the wall clock. No drift, no redundant calls.

**CSS `contain: paint`**
Each bit is isolated — a repaint on one bit doesn't cascade to neighboring elements.

**Safari battery behavior respected**
When the app is backgrounded, Safari freezes timers. This is intentional system behavior and left untouched — the battery matters more than a background tick.

---

## Installation on iPhone

1. Host the file anywhere (GitHub Pages, local server, etc.)
2. Open in Safari
3. Share → Add to Home Screen

Wake Lock and PWA features are fully active only when launched from the home screen.

---

## Size

`~5 KB`. No build step. No node_modules. Open and done.
