<p align="center">
  <img src="assets/banner.svg" alt="FilePrism" width="100%">
</p>

# FilePrism

An online file converter. Images are processed in the browser; video, audio and documents on the server.

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

[Русский](README.md)

FilePrism covers 67 format pairs, 18 guides and 243 pages in Russian and English. Converting a file does not require an account or an install, and photos stay on the device when the browser can handle the format.

## What's inside

| Pairs | Guides | Hubs | Pages |
|---:|---:|---:|---:|
| 67 | 18 | 23 | 243 |

26 pairs convert in the browser; 41 run on the server.

## Screenshots

<p align="center">
  <img src="screenshots/en-home.png" alt="FilePrism home page in English" width="100%">
</p>

<table>
  <tr>
    <td width="50%">
      <img src="screenshots/en-converter-light.png" alt="Converter in English, light theme" width="100%">
    </td>
    <td width="50%">
      <img src="screenshots/en-converter-dark.png" alt="Converter in English, dark theme" width="100%">
    </td>
  </tr>
</table>

<p align="center">
  <img src="screenshots/en-converter-mobile.png" alt="Converter in English on mobile" width="300">
</p>

## Why images stay in the browser

Photos are the most common case. If someone needs JPG to PNG or HEIC to JPG, sending the file to a server and back is often unnecessary. The browser can decode the image and draw it on a Canvas, so the conversion happens locally and the server is only used when the browser cannot handle the format.

It is faster, uses less traffic and keeps personal photos on the device. HEIC goes through heic2any; everything else uses Canvas and WebCodecs. If a format such as TIFF or SVG cannot be handled in the browser, the server path takes over.

## How the server works

Video, audio and documents need FFmpeg, LibreOffice and Poppler, so those files go to the server:

`nginx → Next.js API → Redis/BullMQ → worker`

The API checks the file signature, size and limits, stores the upload and creates a job. The worker takes one job at a time and runs the right tool: FFmpeg for video and audio, LibreOffice for DOCX/XLSX/PPTX, Poppler for PDF, sharp for images and ICO, pdf2docx for PDF to DOCX.

The result sits in a shared volume. The client polls for progress and downloads the file. Everything is removed after download or by TTL.

<p align="center">
  <img src="assets/architecture.svg" alt="FilePrism architecture" width="100%">
</p>

## What it supports

- **Images:** JPG, PNG, WebP, HEIC, AVIF, BMP, TIFF, SVG and ICO. Most of them convert in the browser.
- **Video and audio:** MP4, MOV, WebM, MKV, AVI, GIF, MP3, M4A, WAV and OGG.
- **Documents:** PDF, DOCX, XLSX, PPTX, HTML, CSV, TXT and images to PDF.

Limits are sized for a small server: images up to 25 MB, video and audio up to 100 MB and 5 minutes, documents up to 25 MB.

## How it is built

- **One conversion registry.** Routes, pages, limits and engine selection are built on it. Adding a pair is data and content, not a new page template.
- **Content as data.** Pair pages and guides live in MDX and are validated at build time. A missing translation fails the build.
- **Isolated LibreOffice profiles.** Each job gets its own profile; otherwise concurrent conversions interfere with each other.
- **MIME differences.** AVI may be detected as `video/vnd.avi`, SVG is a text format, and some BMP variants are not readable by sharp directly. Those cases have fallbacks.
- **A single-worker queue.** One worker, limits, timeouts and cleanup. On a small VPS this is more reliable than processing everything in parallel.

## Stack

Next.js 16, React 19, TypeScript and Tailwind CSS 4 on the front end. Redis and BullMQ for the queue. FFmpeg, LibreOffice, Poppler, pdf2docx and sharp for conversions. Docker Compose, nginx and certbot for deployment. Vitest and Playwright for tests.

## Contact

Contact: **vladshumetov@gmail.com**  
Site support: **fileprism.support@gmail.com**  
GitHub: [@vladshumetov](https://github.com/vladshumetov)

<p align="center"><sub>© 2026 FilePrism</sub></p>
