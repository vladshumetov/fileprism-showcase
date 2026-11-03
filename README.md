<p align="center">
  <img src="assets/banner.svg" alt="FilePrism — конвертируйте что угодно, сохраняя приватность" width="100%">
</p>

<h1 align="center">FilePrism</h1>

<p align="center">
  Двуязычный конвертер файлов с приватностью по умолчанию: изображения, видео, аудио и документы.<br>
  Изображения конвертируются в браузере. Остальное — в изолированной серверной очереди.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Next.js-16-000?logo=nextdotjs&logoColor=white" alt="Next.js">
  <img src="https://img.shields.io/badge/TypeScript-5.9-3178C6?logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/Tailwind_CSS-4-06B6D4?logo=tailwindcss&logoColor=white" alt="Tailwind CSS">
  <img src="https://img.shields.io/badge/Docker_Compose-ready-2496ED?logo=docker&logoColor=white" alt="Docker Compose">
  <img src="https://img.shields.io/badge/Redis_%2B_BullMQ-queue-DC382D?logo=redis&logoColor=white" alt="Redis и BullMQ">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/67_пар_конвертации-8B5CF6" alt="67 пар">
  <img src="https://img.shields.io/badge/18_гайдов-8B5CF6" alt="18 гайдов">
  <img src="https://img.shields.io/badge/243_страницы-8B5CF6" alt="243 страницы">
  <img src="https://img.shields.io/badge/языки-RU_%7C_EN-8B5CF6" alt="RU и EN">
  <img src="https://img.shields.io/badge/тесты-67_passed-2EA043?logo=vitest&logoColor=white" alt="Тесты">
</p>

<p align="center">
  <strong>Русский</strong> · <a href="README.en.md">English</a>
</p>

> **Статус:** MVP готов. Публичный деплой в процессе. Исходный код продакшена приватный; этот репозиторий — продуктовый и инженерный кейс.

## Что это

FilePrism — онлайн-конвертер файлов, доведённый до состояния, близкого к продакшену. Внутри 67 пар форматов в четырёх категориях, 18 лонгридов, 23 хаба форматов и 243 статические страницы на русском и английском.

Главная идея — **гибридная конвертация**:

- **Изображения** декодируются и перекодируются в браузере через Canvas, WebCodecs и heic2any. Если браузер справляется, файл не покидает устройство.
- **Видео, аудио и документы** проходят через nginx и API Next.js, попадают в очередь Redis/BullMQ и обрабатываются одним воркером через FFmpeg, LibreOffice, Poppler, sharp и pdf2docx.
- Результаты отдаются из общего volume и удаляются после скачивания или по TTL.

## Скриншоты

<p align="center">
  <img src="screenshots/home-hero.png" alt="Главная страница FilePrism" width="100%">
</p>

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/converter-light.png" alt="Конвертер — светлая тема" width="100%">
    </td>
    <td width="50%">
      <img src="screenshots/converter-dark.png" alt="Конвертер — тёмная тема" width="100%">
    </td>
  </tr>
  <tr>
    <td align="center"><sub>Конвертер — светлая тема</sub></td>
    <td align="center"><sub>Конвертер — тёмная тема</sub></td>
  </tr>
</table>

<p align="center">
  <img src="screenshots/converter-mobile.png" alt="Мобильный конвертер" width="300">
</p>
<p align="center"><sub>Мобильная вёрстка</sub></p>

<p align="center">
  <img src="screenshots/guide-hero.png" alt="Двуязычный гайд" width="100%">
</p>
<p align="center"><sub>Один из 18 двуязычных гайдов</sub></p>

## Ключевые цифры

| Область | Результат |
|---|---|
| Конвертации | 67 пар: изображения, видео, аудио, документы |
| Контент | 18 двуязычных гайдов и 23 хаба форматов |
| Страницы | 243 статические страницы на RU и EN |
| Приватность | Без аккаунтов и истории, автоудаление файлов |
| Лимиты | Изображения 25 МБ; видео/аудио 100 МБ, 5 минут, Full HD; документы 25 МБ |
| SEO | SSG, canonical, hreflang, `x-default`, sitemap, robots, JSON-LD |
| Монетизация | Слоты AdSense + Яндекс.РСЯ, consent, защита от CLS |
| Эксплуатация | Docker Compose, healthchecks, TLS-entrypoint nginx, certbot |

## Архитектура

<p align="center">
  <img src="assets/architecture.svg" alt="Архитектура FilePrism" width="100%">
</p>

Система сознательно разделена на два пути:

1. **Локальный путь.** Браузер читает файл, конвертирует через Canvas/WebCodecs и скачивает результат. Без загрузки и без нагрузки на CPU сервера.
2. **Очередь.** nginx завершает TLS и проксирует в Next.js. API проверяет magic bytes и лимиты, сохраняет файл, создаёт задачу BullMQ и возвращает job id. Один воркер обрабатывает задачу и пишет результат в общий volume. Клиент опрашивает прогресс и скачивает результат.

Воркер работает с `concurrency: 1`, чтобы оставаться стабильным на небольших VPS. Очистка удаляет просроченные задачи и файлы.

## Стек

| Слой | Технологии |
|---|---|
| Web | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS 4 |
| UI | shadcn-подобные примитивы, Lucide, Framer Motion, next-themes |
| API | Route handlers Next.js, Node runtime, `file-type`, rate limit |
| Очередь | BullMQ + Redis, изолированные задачи, прогресс, TTL-очистка |
| Видео | FFmpeg (H.264, VP9, Opus, MP3, AAC, GIF-палитры) |
| Документы | LibreOffice Writer/Calc/Impress, Poppler, pdf2docx, pdf-lib |
| Изображения | sharp на сервере, Canvas/WebCodecs в браузере, heic2any |
| Инфраструктура | Docker Compose, nginx, certbot, healthchecks, общие volume |
| Тесты | Vitest, Playwright |

## Инженерные решения

- **Единый реестр конвертаций** управляет роутами, SEO-страницами, лимитами и выбором движка. Новая пара — это данные и контент, а не новый шаблон страницы.
- **Программное SEO** генерирует страницы пар и хабы форматов из реестра. Контент лежит в типизированных MDX-файлах и валидируется на сборке; отсутствие перевода ломает билд.
- **Гибридная обработка** убирает трафик изображений с сервера и оставляет очередь для тяжёлых видео, аудио и документов.
- **Структурированные данные**: `WebSite`, `WebApplication`, `FAQPage`, `HowTo`, `CollectionPage`, `Article`, `BreadcrumbList`.
- **Реклама и consent** сделаны как провайдер-агностичные слоты. Скрипты грузятся только после согласия, высота слотов зарезервирована против CLS.
- **Тесты** покрывают реестр, схемы контента, SEO-хелперы и сборку аргументов воркера, а Playwright проверяет браузерную конвертацию и сохранение задачи при смене языка.

## Что было сложного

- **Разделение браузера и сервера.** Выбрать, какие форматы можно конвертировать локально без потери UX, и корректно откатываться на сервер, если браузер не умеет кодировать формат.
- **Стабильность на слабом сервере.** Один воркер, жёсткие лимиты, ограничение очереди, rate limit, таймауты и автоудаление.
- **Документы.** Объединить LibreOffice, Poppler, pdf2docx и pdf-lib за одним интерфейсом задач, с изолированными профилями LibreOffice на каждую задачу.
- **Двуязычное SEO.** Держать деревья RU/EN, метаданные, sitemap и валидацию контента синхронными.
- **Реальные MIME-различия.** `video/vnd.avi`, текстовая детекция SVG и варианты BMP, которые sharp не читает напрямую.

## Дорожная карта

- [ ] Публичный деплой с HTTPS
- [ ] AdSense / Яндекс.РСЯ и ID аналитики
- [ ] Search Console и Яндекс.Вебмастер
- [ ] Lighthouse mobile ≥ 90
- [ ] Потоковая загрузка или более строгий контроль конкурентности
- [ ] Больше хабов, гайдов и внешних ссылок

## Исходники и контакт

Исходный код продакшена приватный. Готов провести по архитектуре, компромиссам и деталям реализации.

- GitHub: [@vladshumetov](https://github.com/vladshumetov)
- Email: **fileprism.support@gmail.com**

## Лицензия

Контент showcase © 2026 FilePrism. Исходный код продакшена не включён и не лицензирован для распространения.
