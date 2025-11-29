---
inclusion: always
---

# Project Structure

## File Organization

```
.
├── index.html              # Single-file application (all HTML, CSS, JS)
├── .kiro/
│   ├── specs/
│   │   └── pdf-merger-splitter/
│   │       ├── design.md   # Detailed design document
│   │       └── tasks.md    # Implementation checklist
│   └── steering/           # AI assistant guidance documents
└── .vscode/                # Editor configuration
```

## Code Organization (within index.html)

### HTML Structure
- `<head>`: Meta tags, Tailwind CDN, inline styles
- `<body>`: Main container with glassmorphism styling
- `#app`: Dynamic content rendering target

### CSS (Inline `<style>`)
- Glassmorphism effects (`.glass`, `.glass-strong`)
- Gradient background
- Animations (pulse, spin)
- Responsive breakpoints
- Touch-friendly sizing

### JavaScript (Inline `<script>`)

**State Management**
- Global `state` object with current mode, files, and processing status
- `updateState()` function triggers re-renders

**Core Functions**
- `init()`: Initialize app on DOM ready
- `render()`: Generate and inject HTML based on state
- `switchMode()`: Toggle between merge/split modes

**File Handling**
- `handleFileSelect()`: Process file input events
- `validatePDFFile()`: Check file type, size, encryption
- `addFile()`: Add validated files to state
- `removeFile()`: Remove files from merge list

**PDF Operations**
- `mergePDFs()`: Combine multiple PDFs using pdf-lib
- `splitPDF()`: Divide PDF based on configuration
- `compressPDF()`: Reduce file size by rendering pages to JPEG
- `parsePageRanges()`: Parse range strings like "1-3, 5-7"
- `loadPDFPageCount()`: Extract page count for split/compress mode

**UI Handlers**
- `handleDragOver/Leave/Drop()`: Drag-and-drop functionality
- `triggerFileInput()`: Open file picker
- `updateSplitConfig()`: Update split settings
- `updateCompressConfig()`: Update compression settings
- `isActionEnabled()`: Validate action button state

**Utilities**
- `formatFileSize()`: Convert bytes to human-readable format

## State Structure

```javascript
{
  currentMode: 'merge' | 'split' | 'compress',
  uploadedFiles: Array<{file, name, size, id}>,  // Merge mode
  uploadedFile: File | null,                      // Split/Compress mode
  splitConfig: {
    mode: 'individual' | 'range' | 'every-n',
    ranges: string,
    pageInterval: number
  } | null,
  compressConfig: {
    quality: number,                              // 0.3-1.0
    preset: 'low' | 'medium' | 'high' | 'custom'
  },
  isProcessing: boolean,
  totalPages: number | null,
  processingProgress: {current, total} | null,    // For compress mode
  originalSize: number | null,                    // For compress mode
  compressedSize: number | null                   // For compress mode
}
```

## UI Components (Rendered Dynamically)

1. Mode Switcher - Tab navigation (Merge/Split/Compress)
2. Privacy Badge - Security messaging
3. Upload Zone - Drag-and-drop area
4. File List - Merge mode file display
5. Split Configuration Panel - Split mode settings
6. Compress Configuration Panel - Compress mode settings with quality presets and slider
7. Action Button - Merge/Split/Compress trigger
8. Processing Overlay - Loading state with progress bar for compress mode

## Conventions

- **No external files**: Everything in one HTML file for portability
- **CDN dependencies**: No npm or package management
- **Inline event handlers**: onclick, onchange attributes in rendered HTML
- **Template literals**: For dynamic HTML generation
- **Async/await**: For PDF processing operations
- **State-driven rendering**: Full re-render on state changes
