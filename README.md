<p align="center">
  <img src="assets/banner.svg" alt="FilePrism" width="100%">
</p>

# FilePrism

Онлайн-конвертер файлов. Изображения обрабатываются прямо в браузере, видео, аудио и документы — на сервере.

<p align="center">
  <img src="https://img.shields.io/badge/Next.js-16-000000?style=for-the-badge&logo=nextdotjs&logoColor=white" alt="Next.js">
  <img src="https://img.shields.io/badge/React-19-20232A?style=for-the-badge&logo=react&logoColor=61DAFB" alt="React">
  <img src="https://img.shields.io/badge/TypeScript-5.9-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/Tailwind_CSS-4-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white" alt="Tailwind CSS">
  <img src="https://img.shields.io/badge/Docker_Compose-ready-2496ED?style=for-the-badge&logo=docker&logoColor=white" alt="Docker Compose">
  <img src="https://img.shields.io/badge/Redis_%2B_BullMQ-queue-DC382D?style=for-the-badge&logo=redis&logoColor=white" alt="Redis and BullMQ">
  <br>
  <img src="https://img.shields.io/badge/pairs-67-8B5CF6?style=for-the-badge" alt="67 pairs">
  <img src="https://img.shields.io/badge/guides-18-8B5CF6?style=for-the-badge" alt="18 guides">
  <img src="https://img.shields.io/badge/hubs-23-8B5CF6?style=for-the-badge" alt="23 hubs">
  <img src="https://img.shields.io/badge/pages-243-8B5CF6?style=for-the-badge" alt="243 pages">
  <img src="https://img.shields.io/badge/languages-RU_%7C_EN-8B5CF6?style=for-the-badge" alt="RU and EN">
  <img src="https://img.shields.io/badge/tests-67_passed-2EA043?style=for-the-badge&logo=vitest&logoColor=white" alt="67 tests">
</p>

[English](README.en.md)

FilePrism — это 67 пар форматов, 18 гайдов и 243 страницы на русском и английском. Конвертация не требует аккаунта и установки программ, а изображения обрабатываются на устройстве пользователя, когда браузер поддерживает формат.

## Что внутри

| Пар | Гайдов | Хабов | Страниц |
|---:|---:|---:|---:|
| 67 | 18 | 23 | 243 |

26 пар из 67 конвертируются в браузере, 41 — на сервере.

67 пар — это не «все форматы мира», а те направления, которые действительно ищут: JPG в PNG, HEIC в JPG, PDF в DOCX, MP4 в GIF, XLSX в CSV и так далее. Под каждую пару есть отдельная страница, под каждый формат — хаб, а под частые вопросы — гайды.

## Скриншоты

<p align="center">
  <img src="screenshots/home-hero.png" alt="Главная страница FilePrism" width="100%">
</p>

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/converter-light.png" alt="Конвертер, светлая тема" width="100%">
    </td>
    <td width="50%">
      <img src="screenshots/converter-dark.png" alt="Конвертер, тёмная тема" width="100%">
    </td>
  </tr>
</table>

<p align="center">
  <img src="screenshots/converter-mobile.png" alt="Конвертер на телефоне" width="300">
</p>

## Почему изображения остаются в браузере

Фотографии — самый частый сценарий. Для JPG → PNG или HEIC → JPG файл не обязательно отправлять на сервер и обратно: браузер декодирует изображение и отрисовывает его на Canvas. Поэтому картинки обрабатываются локально, а сервер подключается только если браузер не справился с форматом.

Это быстрее, не создаёт лишний трафик и оставляет фотографии на устройстве. Для HEIC используется heic2any, для остального — Canvas и WebCodecs. Если формат вроде TIFF или SVG браузер не поддерживает, включается серверный путь.

## Как работает серверная часть

Видео, аудио и документы — другая история. Здесь нужны FFmpeg, LibreOffice и Poppler, поэтому файл уходит на сервер. Цепочка выглядит так:

`nginx → API Next.js → Redis/BullMQ → worker`

API проверяет тип файла по сигнатуре, размер и лимиты, сохраняет загрузку и создаёт задачу. Воркер берёт одну задачу за раз и запускает нужный инструмент: FFmpeg для видео и аудио, LibreOffice для DOCX/XLSX/PPTX, Poppler для PDF, sharp для картинок и ICO, pdf2docx для PDF → DOCX.

Результат лежит в общем volume, клиент опрашивает прогресс и забирает файл. После скачивания или по TTL всё удаляется.

<p align="center">
  <img src="assets/architecture.svg" alt="Архитектура FilePrism" width="100%">
</p>

## Что поддерживается

- **Изображения:** JPG, PNG, WebP, HEIC, AVIF, BMP, TIFF, SVG и ICO. Большая часть конвертируется в браузере.
- **Видео и аудио:** MP4, MOV, WebM, MKV, AVI, GIF, MP3, M4A, WAV и OGG.
- **Документы:** PDF, DOCX, XLSX, PPTX, HTML, CSV, TXT и изображения в PDF.

Лимиты подобраны под небольшой сервер: изображения до 25 МБ, видео и аудио до 100 МБ и 5 минут, документы до 25 МБ.

## Как это сделано

- **Один реестр конвертаций.** На нём построены роуты, страницы, лимиты и выбор движка. Новая пара — это данные и контент, а не новый шаблон.
- **Контент как данные.** Страницы пар и гайдов лежат в MDX и валидируются на сборке. При отсутствии перевода сборка падает.
- **Изолированные профили LibreOffice.** На каждую задачу — свой профиль, иначе параллельные конвертации мешают друг другу.
- **MIME-различия.** AVI может определяться как `video/vnd.avi`, SVG — текстовый формат, а некоторые BMP sharp не читает напрямую. Для этих случаев предусмотрены fallback-и.
- **Очередь с одним воркером.** Один воркер, лимиты, таймауты и очистка. Для небольшого VPS это надёжнее, чем параллельная обработка всего сразу.

## Стек

Next.js 16, React 19, TypeScript и Tailwind CSS 4 на фронте. Redis и BullMQ для очереди. FFmpeg, LibreOffice, Poppler, pdf2docx и sharp для конвертации. Docker Compose, nginx и certbot для запуска. Vitest и Playwright для тестов.

## Контакты

Связаться: **vladshumetov@gmail.com**  
Поддержка сайта: **fileprism.support@gmail.com**  
GitHub: [@vladshumetov](https://github.com/vladshumetov)

<p align="center"><sub>© 2026 FilePrism</sub></p>
