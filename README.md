# AirGap PDF Tools

A privacy-focused, client-side web application for manipulating PDF files. All processing happens entirely in your browser - your files never leave your device.

## Features

### 🔒 Complete Privacy
- **100% offline processing** - files never leave your device
- No server uploads or data collection
- No tracking or analytics
- Works entirely in the browser

### 📄 PDF Operations

**Merge PDFs**
- Combine multiple PDF files into one document
- Drag-and-drop interface
- Supports files up to 200MB each

**Split PDFs**
- Extract individual pages
- Split by custom page ranges (e.g., "1-3, 5-7, 9")
- Split every N pages at regular intervals
- Supports files up to 1GB

**Compress PDFs**
- Reduce file size with adjustable quality settings
- Quality presets: Low, Medium, High, or Custom
- Real-time progress tracking
- Supports files up to 1GB

## Quick Start

### Option 1: Use Online
Visit the live application: [AirGap PDF Tools](https://werewolf-leader.github.io/AirGap-PDF/)

### Option 2: Run Locally

1. Clone the repository:
```bash
git clone https://github.com/Werewolf-Leader/AirGap-PDF.git
cd AirGap-PDF
```

2. Serve the file with any static server:
```bash
# Using Python
python -m http.server 8000

# Or using Node.js
npx serve .
```

3. Open `http://localhost:8000` in your browser

### Option 3: Direct File Access
Simply open `index.html` directly in your browser - no server required!

## Technology Stack

- **HTML5** - Single-page application
- **Vanilla JavaScript (ES6+)** - No frameworks
- **Tailwind CSS** - Utility-first styling (CDN)
- **pdf-lib** - PDF manipulation (CDN)
- **pdf.js** - PDF rendering for compression (CDN)

## Browser Compatibility

- ✅ Chrome/Edge (Chromium-based)
- ✅ Firefox
- ✅ Safari (iOS 11.3+)

Requires a modern browser with ES6+ support and FileReader API.

## How It Works

All PDF processing happens client-side using WebAssembly-powered libraries:

1. **Merge**: Combines PDF documents using pdf-lib
2. **Split**: Extracts pages based on your configuration
3. **Compress**: Renders pages to JPEG with adjustable quality, then rebuilds the PDF

No build step required - the entire application runs from a single HTML file.

## Privacy & Security

- **Zero server communication** - all operations are local
- **No data persistence** - files are cleared from memory after processing
- **Open source** - inspect the code yourself
- **No external dependencies at runtime** - only CDN libraries for PDF processing

## File Size Limits

- **Merge**: 200MB per file
- **Split**: 1GB per file
- **Compress**: 1GB per file

## Development

This is a zero-build project. To contribute:

1. Fork the repository
2. Make your changes to `index.html`
3. Test in your browser
4. Submit a pull request

No compilation, bundling, or transpilation needed!

## License

Open source - feel free to use, modify, and distribute.

## Support

Found a bug or have a feature request? [Open an issue](https://github.com/Werewolf-Leader/AirGap-PDF/issues)

---

**Built with privacy in mind. Your files, your device, your control.**
