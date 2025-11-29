---
inclusion: always
---

# Technical Stack

## Core Technologies

- **HTML5**: Single-page application structure
- **Vanilla JavaScript (ES6+)**: All application logic, no frameworks
- **Tailwind CSS (CDN)**: Utility-first styling via `https://cdn.tailwindcss.com`
- **pdf-lib (CDN)**: PDF manipulation via `https://unpkg.com/pdf-lib/dist/pdf-lib.min.js`
- **pdf.js (CDN)**: PDF rendering for compression via `https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.4.120/pdf.min.js`
- **downloadjs (CDN)**: File downloads via `https://cdnjs.cloudflare.com/ajax/libs/downloadjs/1.4.7/download.min.js`

## Architecture Pattern

- Client-side only, no backend
- State-driven rendering with global state object
- Functional programming approach with pure functions
- Event-driven UI updates

## Build System

**None required** - This is a zero-build project. The application runs directly in the browser without compilation, bundling, or transpilation.

## Development Workflow

### Running Locally
```bash
# Serve with any static file server
python -m http.server 8000
# or
npx serve .
```

Then open `http://localhost:8000` in a browser.

### Testing
Manual testing in browser. No automated test suite currently exists.

### Deployment
Simply upload `index.html` to any static hosting service (GitHub Pages, Netlify, Vercel, etc.). No build step required.

## Browser Compatibility

- Chrome/Edge (Chromium-based): Full support
- Firefox: Full support  
- Safari: Full support (iOS 11.3+)

Requires modern browser with ES6+ support and FileReader API.

## Key Libraries Usage

### pdf-lib
```javascript
const { PDFDocument } = PDFLib;
const pdfDoc = await PDFDocument.load(arrayBuffer);
const mergedPdf = await PDFDocument.create();
const copiedPages = await mergedPdf.copyPages(pdfDoc, pdfDoc.getPageIndices());
```

### pdf.js
```javascript
pdfjsLib.GlobalWorkerOptions.workerSrc = 'path/to/pdf.worker.min.js';
const loadingTask = pdfjsLib.getDocument({ data: arrayBuffer });
const pdfDoc = await loadingTask.promise;
const page = await pdfDoc.getPage(pageNum);
```

### File Handling
Native File API and ArrayBuffer for reading PDFs. Blob URLs for downloads. Canvas API for rendering pages during compression.

## Performance Considerations

- File size limits: 200MB per file (merge), 1GB (split/compress)
- Compression is CPU-intensive and may take several minutes for large PDFs
- Processing happens with progress updates in compress mode
- Memory cleanup via URL.revokeObjectURL() after downloads
