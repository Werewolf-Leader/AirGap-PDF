# Implementation Plan

- [x] 1. Create HTML structure with CDN dependencies and glassmorphism base styling
  - Create index.html with HTML5 doctype and semantic structure
  - Add Tailwind CSS CDN link in head
  - Add pdf-lib CDN script (https://unpkg.com/pdf-lib/dist/pdf-lib.min.js)
  - Add pdf.js CDN script for compression functionality
  - Add downloadjs CDN script or prepare native download implementation
  - Create gradient background with glassmorphism container
  - Add meta tags for responsive viewport
  - _Requirements: 8.1, 8.2, 9.3_

- [x] 2. Implement mode switcher component with three-way tab navigation
  - Create tab interface with "Merge", "Split", and "Compress" buttons
  - Add glassmorphism styling for active/inactive tab states
  - Implement JavaScript click handlers to switch between modes
  - Add state management to track current mode
  - Implement state reset functionality when switching modes
  - _Requirements: 1.1, 1.2, 1.3, 1.4_

- [x] 3. Build upload zone component with drag-and-drop functionality
  - Create large upload zone div with dashed border and centered content
  - Add icon and instructional text ("Drag & drop PDFs here or click to upload")
  - Create hidden file input element with appropriate accept attribute
  - Implement drag-over visual feedback with event listeners (dragenter, dragover, dragleave, drop)
  - Implement click-to-upload functionality
  - Add logic to accept multiple files in merge mode and single file in split/compress modes
  - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 2.6_

- [x] 4. Add privacy badge component
  - Create badge element with shield/lock icon
  - Add text "100% Secure & Offline. Your files never leave your device."
  - Apply glassmorphism styling with green/blue accent
  - Position near upload zone for visibility
  - _Requirements: 3.1, 3.2, 3.3_

- [x] 5. Implement file validation logic with mode-specific size limits
  - Create validatePDFFile function to check file type
  - Add file size validation (200MB for merge, 1GB for split/compress)
  - Implement error detection for encrypted/password-protected PDFs using pdf-lib
  - Create error display function using browser alert
  - Add validation calls on file upload
  - _Requirements: 4.1, 4.2, 4.3, 4.4_

- [x] 6. Build file list display for merge mode
  - Create container to display uploaded files
  - Show file name and size for each uploaded file
  - Add remove button for each file with click handler
  - Implement file removal functionality
  - Update UI to show/hide file list based on uploaded files
  - Store files in application state array
  - _Requirements: 6.1_

- [x] 7. Create split configuration panel for split mode
  - Display total page count after PDF upload
  - Create radio buttons for split options (by range, individual pages, every N pages)
  - Add input field for page ranges with placeholder "e.g., 1-3, 5-7"
  - Add input field for page interval (every N pages option)
  - Implement input validation for split configuration
  - Show/hide panel based on mode and file upload status
  - _Requirements: 7.1, 7.2_

- [x] 8. Create compress configuration panel for compress mode
  - Display total page count after PDF upload
  - Add quality preset buttons (Low, Medium, High)
  - Implement custom quality slider (30%-100%)
  - Display original file size
  - Add warning message about CPU-intensive processing
  - Show/hide panel based on mode and file upload status
  - _Requirements: New compress feature_

- [x] 9. Implement processing state and action button with progress tracking
  - Create action button with dynamic text ("Merge PDFs", "Split PDF", or "Compress PDF")
  - Apply glassmorphism styling with hover effects
  - Implement button enable/disable logic based on file upload and configuration
  - Create processing spinner/overlay component with progress bar
  - Add click handler to trigger merge, split, or compress operation
  - Show spinner and disable button during processing
  - Display progress for compress mode (page X of Y)
  - Hide spinner and re-enable button after operation completes
  - _Requirements: 5.1, 5.2, 5.3, 5.4_

- [x] 10. Implement PDF merge functionality
  - Create async mergePDFs function
  - Read each uploaded file as ArrayBuffer
  - Use pdf-lib to create new PDFDocument
  - Loop through uploaded files and copy all pages to merged document
  - Serialize merged PDF document
  - Trigger download with filename "merged.pdf" using native method
  - Add error handling with try-catch and user alerts
  - Reset application state after successful merge
  - _Requirements: 6.2, 6.3, 6.4_

- [x] 11. Implement PDF split functionality
  - Create async splitPDF function
  - Read uploaded file as ArrayBuffer
  - Use pdf-lib to load and parse PDF
  - Extract total page count and update UI
  - Based on split configuration mode:
    - Parse page ranges for range mode
    - Generate individual page splits for individual mode
    - Calculate page groups for every-N mode
  - Create separate PDFDocument for each split section
  - Copy specified pages to each new document
  - Serialize all split documents
  - Trigger downloads for each file with descriptive names (e.g., "split-1.pdf", "split-2.pdf")
  - Add error handling with try-catch and user alerts
  - Reset application state after successful split
  - _Requirements: 7.3, 7.4_

- [x] 12. Implement PDF compress functionality
  - Create async compressPDF function
  - Read uploaded file as ArrayBuffer
  - Use pdf.js to load and render PDF pages
  - Loop through each page with progress updates
  - Render each page to canvas at scale 1.0
  - Convert canvas to JPEG blob with configured quality
  - Use pdf-lib to create new PDFDocument
  - Embed JPEG images as pages in new document
  - Serialize compressed PDF document
  - Calculate and display compression statistics (original size, compressed size, reduction percentage)
  - Trigger download with filename "compressed.pdf"
  - Add error handling with try-catch and user alerts
  - Reset application state after successful compression
  - _Requirements: New compress feature_

- [x] 13. Add responsive design and final UI polish
  - Ensure mobile-first responsive layout using Tailwind breakpoints
  - Add smooth transitions and animations (transition-all duration-300)
  - Implement hover effects on interactive elements
  - Add pulse effect for drag-over state
  - Verify glassmorphism effects (backdrop-blur, transparency, borders)
  - Test and adjust spacing, sizing, and alignment
  - Ensure touch-friendly button sizes for mobile
  - _Requirements: 8.2, 8.3, 8.4_

- [ ] 14. Perform comprehensive testing and validation
  - Test mode switching and state reset across all three modes
  - Test drag-and-drop and click-to-upload in all modes
  - Test file validation with non-PDF files
  - Test merge with 2-5 PDF files
  - Test split with various configurations (range, individual, every-N)
  - Test compress with different quality presets and custom settings
  - Test error handling for invalid files and configurations
  - Test in multiple browsers (Chrome, Firefox, Safari)
  - Verify privacy badge visibility and messaging
  - Test responsive behavior on different screen sizes
  - Verify processing spinner and progress bar appear correctly
  - Test large file handling (up to 200MB for merge, 1GB for split/compress)
  - _Requirements: All_
