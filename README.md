<p align="center">
  <img src="assets/banner.svg" alt="FilePrism" width="100%">
</p>

# FilePrism

Онлайн-конвертер файлов. Изображения обрабатываются прямо в браузере, видео, аудио и документы — на сервере.

[English](README.en.md)

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

## Что умеет

- Конвертирует 67 пар форматов: изображения, видео, аудио и документы.
- Фотографии не загружаются на сервер, если браузер справляется сам.
- Видео, аудио и документы проходят через очередь и отдельный воркер.
- Интерфейс на русском и английском, светлая и тёмная темы, нормальный мобильный вид.
- Не нужны аккаунты. Файлы удаляются автоматически.

## Как устроено

Изображения декодируются и перекодируются в браузере через Canvas, WebCodecs и heic2any. Если браузер не умеет формат, файл уходит на сервер.

Всё остальное идёт по цепочке: nginx → API Next.js → Redis/BullMQ → воркер. Воркер по очереди запускает FFmpeg, LibreOffice, Poppler, sharp или pdf2docx. Результат лежит в общем volume и удаляется после скачивания или по TTL.

<p align="center">
  <img src="assets/architecture.svg" alt="Архитектура FilePrism" width="100%">
</p>

## Стек

- **Web:** Next.js 16, React 19, TypeScript, Tailwind CSS 4
- **Очередь:** Redis, BullMQ
- **Видео и аудио:** FFmpeg
- **Документы:** LibreOffice, Poppler, pdf2docx, pdf-lib
- **Изображения:** sharp, Canvas/WebCodecs, heic2any
- **Инфраструктура:** Docker Compose, nginx, certbot
- **Тесты:** Vitest, Playwright

## Контакты

Сделал [@vladshumetov](https://github.com/vladshumetov).  
Почта: **fileprism.support@gmail.com**

<p align="center"><sub>© 2026 FilePrism</sub></p>
