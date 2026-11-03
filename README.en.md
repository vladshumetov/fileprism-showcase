<p align="center">
  <img src="assets/banner.svg" alt="FilePrism" width="100%">
</p>

# FilePrism

An online file converter. Images are processed in the browser; video, audio and documents on the server.

[Русский](README.md)

## Screenshots

<p align="center">
  <img src="screenshots/home-hero.png" alt="FilePrism home page" width="100%">
</p>

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/converter-light.png" alt="Converter, light theme" width="100%">
    </td>
    <td width="50%">
      <img src="screenshots/converter-dark.png" alt="Converter, dark theme" width="100%">
    </td>
  </tr>
</table>

<p align="center">
  <img src="screenshots/converter-mobile.png" alt="Converter on mobile" width="300">
</p>

## What it does

- Converts 67 format pairs across images, video, audio and documents.
- Photos stay in the browser when it can handle the format.
- Video, audio and documents go through a queue and a dedicated worker.
- Russian and English UI, light and dark themes, mobile layout.
- No accounts. Files are deleted automatically.

## How it works

Images are decoded and re-encoded in the browser with Canvas, WebCodecs and heic2any. If the browser cannot handle a format, the file is sent to the server.

Everything else goes through nginx → Next.js API → Redis/BullMQ → worker. The worker runs FFmpeg, LibreOffice, Poppler, sharp or pdf2docx one job at a time. Results live in a shared volume and are removed after download or by TTL.

<p align="center">
  <img src="assets/architecture.svg" alt="FilePrism architecture" width="100%">
</p>

## Stack

- **Web:** Next.js 16, React 19, TypeScript, Tailwind CSS 4
- **Queue:** Redis, BullMQ
- **Video and audio:** FFmpeg
- **Documents:** LibreOffice, Poppler, pdf2docx, pdf-lib
- **Images:** sharp, Canvas/WebCodecs, heic2any
- **Infrastructure:** Docker Compose, nginx, certbot
- **Tests:** Vitest, Playwright

## Contact

Made by [@vladshumetov](https://github.com/vladshumetov).  
Email: **fileprism.support@gmail.com**

<p align="center"><sub>© 2026 FilePrism</sub></p>
