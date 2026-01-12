# UX/UI СИСТЕМА И ДИЗАЙН-СИСТЕМА

## 1. ЦВЕТОВАЯ ПАЛИТРА

### Основные цвета

**Primary (Основной):**
- Индиго: `#6366f1` — главный акцентный цвет
- Использование: кнопки, ссылки, акценты

**Secondary (Вторичные):**
- Синий: `#3b82f6` — Модуль Foundation
- Розовый: `#ec4899` — Модуль Heart
- Фиолетовый: `#8b5cf6` — Модуль Mind
- Оранжевый: `#f59e0b` — Модуль Purpose

**Нейтральные:**
- Тёмный фон: `#1a1a2e`
- Тёмный градиент: `#1a1a2e → #16213e`
- Светлый фон: `#f5f5f7`
- Белый: `#ffffff`
- Серый текст: `#6b7280`

### Семантические цвета

**Success (Успех):** `#10b981` — зелёный
**Warning (Предупреждение):** `#f59e0b` — янтарный
**Error (Ошибка):** `#ef4444` — красный
**Info (Информация):** `#3b82f6` — синий

---

## 2. ТИПОГРАФИКА

### Шрифты

**Основной шрифт:**
- Семейство: Inter или System UI Stack
- Веса: 400 (regular), 500 (medium), 600 (semibold), 700 (bold)

**Заголовочный шрифт:**
- Семейство: Manrope или аналог
- Веса: 600, 700, 800

### Размеры текста

```
H1: 64px / 72px (desktop) | 40px / 48px (mobile)
H2: 48px / 56px (desktop) | 32px / 40px (mobile)
H3: 36px / 44px (desktop) | 28px / 36px (mobile)
H4: 24px / 32px
H5: 20px / 28px
H6: 18px / 24px

Body Large: 18px / 28px
Body Regular: 16px / 24px
Body Small: 14px / 20px
Caption: 12px / 16px
```

### Иерархия

- **H1:** Только для главного заголовка страницы
- **H2:** Заголовки основных секций
- **H3:** Заголовки подсекций
- **Body Regular:** Основной текст
- **Body Small:** Вспомогательный текст

---

## 3. ИНТЕРВАЛЫ И СЕТКА

### Система интервалов (8px base)

```
xs:   4px   (0.5 unit)
sm:   8px   (1 unit)
md:   16px  (2 units)
lg:   24px  (3 units)
xl:   32px  (4 units)
2xl:  48px  (6 units)
3xl:  64px  (8 units)
4xl:  96px  (12 units)
5xl:  128px (16 units)
```

### Сетка (Grid)

**Desktop (> 1200px):**
- Контейнер: 1200px max-width
- Колонки: 12
- Gutter: 24px
- Margin: 48px

**Tablet (768px - 1200px):**
- Контейнер: 100% - 48px margin
- Колонки: 8
- Gutter: 16px
- Margin: 24px

**Mobile (< 768px):**
- Контейнер: 100% - 32px margin
- Колонки: 4
- Gutter: 16px
- Margin: 16px

---

## 4. КОМПОНЕНТЫ

### 4.1 Кнопки

#### Primary Button (Основная)
```
Стиль: Solid fill
Фон: #6366f1
Текст: #ffffff
Padding: 16px 32px
Border-radius: 8px
Font-size: 16px
Font-weight: 600

Hover: 
- Фон: #4f46e5
- Transform: translateY(-2px)
- Box-shadow: увеличение

Active:
- Transform: translateY(0)
```

#### Secondary Button (Вторичная)
```
Стиль: Outline
Фон: transparent
Текст: #6366f1
Border: 2px solid #6366f1
Padding: 14px 30px (меньше из-за border)
Border-radius: 8px

Hover:
- Фон: #6366f1
- Текст: #ffffff
```

#### Text Button (Текстовая)
```
Стиль: Без фона и border
Текст: #6366f1
Padding: 8px 16px
Hover: Подчёркивание
```

### 4.2 Карточки (Cards)

#### Стандартная карточка
```
Фон: #ffffff
Border-radius: 12px
Padding: 24px
Box-shadow: 0 1px 3px rgba(0,0,0,0.1)

Hover:
- Box-shadow: 0 8px 24px rgba(0,0,0,0.15)
- Transform: translateY(-4px)
- Transition: 0.3s ease
```

#### Карточка с изображением
```
Структура:
- Изображение (aspect-ratio: 16/9)
- Контент (padding: 24px)
- Футер (border-top, padding: 16px 24px)
```

### 4.3 Формы (Forms)

#### Input Field
```
Height: 48px
Padding: 12px 16px
Border: 2px solid #e5e7eb
Border-radius: 8px
Font-size: 16px

Focus:
- Border: 2px solid #6366f1
- Box-shadow: 0 0 0 3px rgba(99,102,241,0.1)

Error:
- Border: 2px solid #ef4444
```

#### Checkbox / Radio
```
Size: 20px
Border-radius: 4px (checkbox) / 50% (radio)
Border: 2px solid #d1d5db

Checked:
- Фон: #6366f1
- Border: #6366f1
- Иконка: белая галочка
```

### 4.4 Навигация

#### Header (Desktop)
```
Height: 72px
Фон: rgba(26, 26, 46, 0.8) — полупрозрачный
Backdrop-filter: blur(10px)
Position: sticky top
Z-index: 1000

Скроллинг:
- Уменьшение высоты до 60px
- Увеличение blur
- Тень снизу
```

#### Header (Mobile)
```
Height: 64px
Гамбургер-меню
Полноэкранное меню при открытии
```

---

## 5. ИКОНКИ И ИЛЛЮСТРАЦИИ

### Стиль иконок
- **Тип:** Line icons (линейные)
- **Толщина линии:** 2px
- **Размеры:** 16px, 20px, 24px, 32px, 48px
- **Библиотека:** Lucide Icons или аналог

### Иллюстрации
- **Стиль:** Минималистичные, абстрактные
- **Цвета:** Из основной палитры
- **Использование:** Заголовки секций, пустые состояния

---

## 6. АНИМАЦИИ И ПЕРЕХОДЫ

### Timing Functions
```
ease-out: cubic-bezier(0, 0, 0.2, 1)
ease-in: cubic-bezier(0.4, 0, 1, 1)
ease-in-out: cubic-bezier(0.4, 0, 0.2, 1)
```

### Длительность
```
Fast: 150ms — hover, клики
Normal: 300ms — переходы
Slow: 500ms — появление контента
Very Slow: 800ms — сложные анимации
```

### Типовые анимации

**Fade In:**
```css
opacity: 0 → 1
duration: 300ms
```

**Slide Up:**
```css
transform: translateY(20px) → translateY(0)
opacity: 0 → 1
duration: 500ms
```

**Scale:**
```css
transform: scale(0.95) → scale(1)
opacity: 0 → 1
duration: 300ms
```

---

## 7. ТЕНИ (Shadows)

```
shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05)
shadow: 0 1px 3px rgba(0, 0, 0, 0.1)
shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1)
shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1)
shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.15)
shadow-2xl: 0 25px 50px rgba(0, 0, 0, 0.25)
```

---

