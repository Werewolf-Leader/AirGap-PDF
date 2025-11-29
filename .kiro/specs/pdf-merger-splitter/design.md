# Design Document

## Overview

The PDF Tools is a single-page web application that operates entirely in the browser using client-side JavaScript. The application provides three core functionalities: merging multiple PDFs, splitting PDFs into separate files, and compressing PDFs to reduce file size. The application leverages the pdf-lib library for PDF manipulation, pdf.js for rendering during compression, and provides a modern glassmorphism UI built with Tailwind CSS. All processing happens locally, ensuring complete privacy and security.

## Architecture

### Technology Stack

- **HTML5**: Semantic markup structure
- **Tailwind CSS (CDN)**: Utility-first CSS framework for styling
- **Vanilla JavaScript (ES6+)**: Client-side logic and PDF processing
- **pdf-lib (CDN)**: PDF manipulation library for merge and split operations
- **pdf.js (CDN)**: PDF rendering library for compression functionality
- **downloadjs (CDN)**: File download utility (or native download implementation)

### Application Structure

The application follows a simple single-page architecture with:
- One HTML file containing all markup
- Inline or embedded JavaScript for application logic
- CDN-loaded external libraries
- No build process or server-side components required

## Components and Interfaces

### 1. Mode Switcher Component

**Purpose**: Toggle between Merge, Split, and Compress modes

**Structure**:
- Three tab buttons styled as a segmented control
- Active state styling with glassmorphism effects
- Click handlers to switch modes

**Behavior**:
- Only one mode active at a time
- Switching modes clears uploaded files and resets UI state
- Visual feedback for active/inactive states

### 2. Upload Zone Component

**Purpose**: Accept PDF file uploads via drag-and-drop or click

**Structure**:
- Large, centered drop zone with dashed border
- Icon and instructional text
- Hidden file input element
- Visual states: default, hover, drag-over, has-files

**Behavior**:
- In Merge Mode: accepts multiple files (up to 200MB each)
- In Split Mode: accepts single file (up to 1GB)
- In Compress Mode: accepts single file (up to 1GB)
- Drag-over visual feedback
- Click to trigger file input dialog
- File validation on upload with mode-specific size limits

### 3. Privacy Badge Component

**Purpose**: Communicate offline processing to users

**Structure**:
- Badge/banner with shield or lock icon
- Text: "100% Secure & Offline. Your files never leave your device."
- Positioned near upload zone for visibility

**Styling**:
- Glassmorphism effect with green/blue accent
- Semi-transparent background with backdrop blur

### 4. File List Display (Merge Mode)

**Purpose**: Show uploaded files in merge mode

**Structure**:
- List of uploaded file names
- File size display
- Remove button for each file
- Drag handles for reordering (optional enhancement)

**Behavior**:
- Display files in upload order
- Allow removal of individual files
- Update merge button state based on file count

### 5. Split Configuration Panel (Split Mode)

**Purpose**: Configure how to split the PDF

**Structure**:
- Page count display
- Split options:
  - Split by page range (e.g., "1-3, 4-6")
  - Split into individual pages
  - Split every N pages
- Input validation

**Behavior**:
- Show after file upload
- Validate split configuration
- Enable split button when valid

### 6. Compress Configuration Panel (Compress Mode)

**Purpose**: Configure compression quality settings

**Structure**:
- Page count display
- Quality preset buttons (Low, Medium, High)
- Custom quality slider (30%-100%)
- Original file size display
- Warning message about CPU-intensive processing

**Behavior**:
- Show after file upload in compress mode
- Update quality value when preset is selected
- Allow custom quality adjustment via slider
- Display current quality percentage

### 7. Action Button Component

**Purpose**: Trigger merge, split, or compress operation

**Structure**:
- Large, prominent button
- Text changes based on mode: "Merge PDFs", "Split PDF", or "Compress PDF"
- Loading spinner state

**Behavior**:
- Disabled when no files uploaded or invalid configuration
- Shows spinner during processing
- Triggers appropriate operation based on mode

### 8. Processing Overlay

**Purpose**: Provide feedback during operations

**Structure**:
- Semi-transparent overlay
- Centered spinner with text
- Progress bar with percentage (for compress mode)
- Page counter (e.g., "Processing page 5 of 20")
- Prevents interaction during processing

**Behavior**:
- Appears when operation starts
- Shows progress updates for compress mode
- Dismisses when operation completes
- Shows error state if operation fails

**Design Rationale**: Compression operations can take several minutes for large PDFs, so detailed progress feedback is essential to prevent user confusion and provide transparency about the processing status.

## Data Models

### FileItem (Merge Mode)

```javascript
{
  file: File,           // Native File object
  name: string,         // File name
  size: number,         // File size in bytes
  id: string           // Unique identifier
}
```

### SplitConfiguration

```javascript
{
  mode: 'range' | 'individual' | 'every-n',
  ranges: string,       // e.g., "1-3, 5-7" (for range mode)
  pageInterval: number  // e.g., 2 (for every-n mode)
}
```

### CompressConfiguration

```javascript
{
  quality: number,      // 0.3-1.0 (JPEG quality)
  preset: 'low' | 'medium' | 'high' | 'custom'
}
```

### ApplicationState

```javascript
{
  currentMode: 'merge' | 'split' | 'compress',
  uploadedFiles: FileItem[],      // For merge mode
  uploadedFile: File | null,      // For split/compress mode
  splitConfig: SplitConfiguration | null,
  compressConfig: CompressConfiguration,
  isProcessing: boolean,
  totalPages: number | null,      // For split/compress mode
  processingProgress: {           // For compress mode
    current: number,
    total: number
  } | null,
  originalSize: number | null,    // For compress mode
  compressedSize: number | null   // For compress mode
}
```

## Core Functionality

### PDF Merging Process

1. User uploads multiple PDF files
2. Validate each file is a valid PDF
3. On merge button click:
   - Set processing state
   - Load pdf-lib library
   - Create new PDFDocument
   - For each uploaded file:
     - Load file as ArrayBuffer
     - Parse with pdf-lib
     - Copy all pages to new document
   - Serialize merged document
   - Trigger download with filename "merged.pdf"
   - Reset state

### PDF Splitting Process

1. User uploads single PDF file
2. Validate file is a valid PDF
3. Load PDF and display page count
4. User configures split options
5. On split button click:
   - Set processing state
   - Load pdf-lib library
   - Parse source PDF
   - Based on split configuration:
     - Create separate PDFDocument for each range/page
     - Copy specified pages to each document
   - Serialize all documents
   - Trigger downloads for each file
   - Reset state

### PDF Compression Process

1. User uploads single PDF file
2. Validate file is a valid PDF
3. Load PDF and display page count
4. User configures compression quality (preset or custom)
5. On compress button click:
   - Set processing state
   - Load pdf.js library
   - Parse source PDF
   - For each page:
     - Update progress indicator (page X of Y)
     - Render page to canvas at scale 1.0
     - Convert canvas to JPEG blob with configured quality
     - Embed JPEG image in new PDFDocument
   - Serialize compressed document
   - Calculate compression statistics (original size, compressed size, reduction percentage)
   - Display compression results to user
   - Trigger download with filename "compressed.pdf"
   - Reset state

**Design Rationale**: The compression approach converts each PDF page to a JPEG image, which significantly reduces file size but may impact quality. This trade-off is made explicit through quality presets and a custom slider, giving users control over the balance between file size and visual fidelity. The page-by-page processing with progress updates ensures transparency during potentially long operations.

### File Validation

```javascript
function validatePDFFile(file) {
  // Check file type
  if (file.type !== 'application/pdf') {
    throw new Error('Only PDF files are accepted');
  }
  
  // Check file size with mode-specific limits
  let MAX_SIZE;
  if (state.currentMode === 'merge') {
    MAX_SIZE = 200 * 1024 * 1024; // 200MB per file
  } else if (state.currentMode === 'split' || state.currentMode === 'compress') {
    MAX_SIZE = 1024 * 1024 * 1024; // 1GB
  }
  
  if (file.size > MAX_SIZE) {
    const limitMB = Math.round(MAX_SIZE / (1024 * 1024));
    throw new Error(`File size exceeds ${limitMB}MB limit for ${state.currentMode} mode`);
  }
  
  // Additional validation after loading with pdf-lib
  // Check for encryption/password protection
}
```

**Design Rationale**: Different file size limits are enforced based on the operation mode. Merge mode has a lower per-file limit (200MB) since multiple files are processed together, while split and compress modes allow larger files (1GB) since only one file is processed at a time. This balances functionality with browser memory constraints.

## UI/UX Design

### Glassmorphism Styling

- Semi-transparent backgrounds: `bg-white/10` or `bg-white/20`
- Backdrop blur: `backdrop-blur-lg`
- Subtle borders: `border border-white/20`
- Soft shadows: `shadow-xl`
- Gradient accents for buttons and active states

### Color Scheme

- Background: Gradient (e.g., purple to blue)
- Primary accent: Blue/cyan for buttons
- Success: Green for privacy badge
- Error: Red for error messages
- Text: White with varying opacity

### Responsive Layout

- Mobile-first approach using Tailwind breakpoints
- Single column layout on mobile
- Centered content with max-width on desktop
- Touch-friendly button sizes

### Animations

- Smooth transitions: `transition-all duration-300`
- Hover effects on interactive elements
- Fade in/out for modals and overlays
- Pulse effect for drag-over state

## Error Handling

### Error Types and Messages

1. **Invalid File Type**
   - Message: "Please upload PDF files only"
   - Action: Reject file, show alert

2. **File Size Exceeded**
   - Message: "File size exceeds [limit]MB limit for [mode] mode"
   - Action: Reject file, show alert

3. **Encrypted/Password-Protected PDF**
   - Message: "This PDF is password-protected and cannot be processed"
   - Action: Reject file, show alert

4. **Corrupted PDF**
   - Message: "This PDF file appears to be corrupted"
   - Action: Reject file, show alert

5. **Processing Error**
   - Message: "An error occurred while processing your PDF. Please try again."
   - Action: Reset state, show alert

6. **Invalid Split Configuration**
   - Message: "Please enter valid page ranges (e.g., 1-3, 5-7)"
   - Action: Disable split button, show inline error

### Error Display

- Use browser `alert()` for critical errors
- Inline validation messages for form inputs
- Toast notifications for non-blocking errors (optional enhancement)

## Testing Strategy

### Manual Testing Checklist

1. **Mode Switching**
   - Verify tabs switch correctly between all three modes
   - Confirm state resets on mode change

2. **File Upload**
   - Test drag-and-drop functionality
   - Test click-to-upload
   - Verify file validation with mode-specific size limits
   - Test multiple files in merge mode (up to 200MB each)
   - Test single file in split mode (up to 1GB)
   - Test single file in compress mode (up to 1GB)

3. **Merge Functionality**
   - Upload 2-5 PDFs and merge
   - Verify page order in output
   - Test with different PDF sizes
   - Verify download triggers

4. **Split Functionality**
   - Test split by range
   - Test split into individual pages
   - Test split every N pages
   - Verify all files download

5. **Compress Functionality**
   - Test with different quality presets (low, medium, high)
   - Test custom quality slider
   - Verify progress bar updates during processing
   - Verify compression statistics display (original size, compressed size, reduction percentage)
   - Test with large PDFs to verify progress tracking
   - Verify download triggers

6. **Error Handling**
   - Upload non-PDF file
   - Upload encrypted PDF (if available)
   - Test invalid split ranges
   - Test file size limits for each mode

7. **UI/UX**
   - Verify glassmorphism effects
   - Test responsive behavior
   - Check processing spinner appears
   - Verify progress bar for compress mode
   - Verify privacy badge visibility
   - Check warning message in compress mode

### Browser Compatibility

Test in:
- Chrome/Edge (Chromium)
- Firefox
- Safari

### Performance Considerations

- Large files (10MB+) should process within reasonable time for merge and split
- Compression is CPU-intensive and may take several minutes for large PDFs
- Progress updates in compress mode prevent UI blocking and provide user feedback
- UI should remain responsive during processing
- Memory cleanup after operations (URL.revokeObjectURL())
- Canvas cleanup after each page render in compress mode

## Implementation Notes

### Library Usage

**pdf-lib** (for merge and split):
```javascript
const { PDFDocument } = PDFLib;

// Load existing PDF
const existingPdfBytes = await file.arrayBuffer();
const pdfDoc = await PDFDocument.load(existingPdfBytes);

// Create new PDF
const mergedPdf = await PDFDocument.create();

// Copy pages
const copiedPages = await mergedPdf.copyPages(pdfDoc, pdfDoc.getPageIndices());
copiedPages.forEach(page => mergedPdf.addPage(page));

// Save
const pdfBytes = await mergedPdf.save();
```

**pdf.js** (for compress):
```javascript
// Configure worker
pdfjsLib.GlobalWorkerOptions.workerSrc = 'path/to/pdf.worker.min.js';

// Load PDF
const loadingTask = pdfjsLib.getDocument({ data: arrayBuffer });
const pdfDoc = await loadingTask.promise;

// Get page
const page = await pdfDoc.getPage(pageNum);
const viewport = page.getViewport({ scale: 1.0 });

// Render to canvas
const canvas = document.createElement('canvas');
const context = canvas.getContext('2d');
canvas.width = viewport.width;
canvas.height = viewport.height;
await page.render({ canvasContext: context, viewport: viewport }).promise;

// Convert to JPEG
const blob = await new Promise(resolve => {
  canvas.toBlob(resolve, 'image/jpeg', quality);
});
```

**Native download** (alternative to downloadjs):
```javascript
const blob = new Blob([pdfBytes], { type: 'application/pdf' });
const url = URL.createObjectURL(blob);
const a = document.createElement('a');
a.href = url;
a.download = 'merged.pdf';
document.body.appendChild(a);
a.click();
document.body.removeChild(a);
URL.revokeObjectURL(url);
```

### State Management

Use a simple global state object with helper functions:
```javascript
const state = {
  currentMode: 'merge',
  uploadedFiles: [],
  isProcessing: false
};

function updateState(updates) {
  Object.assign(state, updates);
  render();
}
```

### File Reading

```javascript
function readFileAsArrayBuffer(file) {
  return new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = () => resolve(reader.result);
    reader.onerror = reject;
    reader.readAsArrayBuffer(file);
  });
}
```

## Security Considerations

- All processing happens client-side
- No data transmission to servers
- Files remain in browser memory only
- Memory cleared after operations (URL.revokeObjectURL())
- No persistent storage of user files
- Canvas elements cleaned up after compression
- No external API calls during processing

## Compression Quality Trade-offs

**Design Decision**: The compression feature uses a JPEG-based approach that converts each PDF page to an image. This design choice has important implications:

**Advantages**:
- Significant file size reduction (often 50-80% smaller)
- Simple implementation using standard browser APIs
- Predictable compression behavior
- Works with any PDF content

**Limitations**:
- Text becomes non-selectable (rasterized)
- Vector graphics converted to raster images
- Quality loss at lower compression settings
- CPU-intensive processing for large documents

**Rationale**: This approach prioritizes simplicity and broad compatibility over preserving PDF features like text selection and vector graphics. The quality presets and custom slider give users explicit control over the quality/size trade-off, making the limitations transparent and acceptable for use cases where file size reduction is the primary goal (e.g., email attachments, web uploads).
