# BulkQR Technical Documentation

## Architecture Overview

BulkQR is a single-page application (SPA) built entirely in HTML5, CSS3, and vanilla JavaScript. It operates as a completely client-side application with no server dependencies.

### Core Technologies
- **HTML5**: Semantic markup, forms, canvas manipulation
- **CSS3**: Grid layout, flexbox, animations, responsive design
- **Vanilla JavaScript**: ES6+ features, async/await, localStorage API
- **External Libraries**:
  - QRCode.js (v1.0.0): QR code generation
  - jsPDF (v2.5.1): PDF creation and export

## Data Structure

### Product Data Model
```javascript
{
  "categoryKey": {
    "name": "Display Name",
    "products": [
      {
        "code": "RE",
        "name": "Rectangle",
        "sizes": ["X", "S", "M", "L", "E"]
      }
    ]
  }
}
```

### QR Tracking Data
```javascript
{
  "categoryKey-productCode-size": {
    "lastId": 123,
    "yearMonth": "2501"
  }
}
```

### Configuration Data
```javascript
{
  "rows": 9,
  "cols": 7,
  "labelWidth": 25.4,
  "labelHeight": 25.4,
  "logoEnabled": true,
  "logoData": "data:image/png;base64,...",
  "logoSize": 20,
  "logoPadding": 2
  // ... additional settings
}
```

## Key Components

### 1. Product Management System

#### Category Management
- **CRUD Operations**: Full create, read, update, delete functionality
- **Data Validation**: Ensures unique category keys and proper naming
- **Migration Support**: Automatic upgrade from legacy data formats

```javascript
// Category structure migration
function migrateProductData(oldFormat) {
  const newFormat = {};
  Object.keys(oldFormat).forEach(categoryKey => {
    if (Array.isArray(oldFormat[categoryKey])) {
      // Convert old array format to new object format
      newFormat[categoryKey] = {
        name: formatCategoryName(categoryKey),
        products: oldFormat[categoryKey]
      };
    }
  });
  return newFormat;
}
```

#### Product Management
- **Dynamic Forms**: Category-dependent size options
- **Code Validation**: Ensures unique product codes within categories
- **Size Management**: Flexible size assignments per product

### 2. QR Code Generation Engine

#### Core Generation Logic
```javascript
async function generateSingleQR(qrValue, config) {
  // 1. Create QR code with specified error correction
  // 2. Add logo overlay if enabled
  // 3. Return base64 image data
  
  return new Promise((resolve) => {
    const qr = new QRCode(tempContainer, {
      text: qrValue,
      width: size,
      height: size,
      correctLevel: config.errorCorrection
    });
    
    // Wait for generation, then add logo
    setTimeout(async () => {
      const canvas = tempContainer.querySelector('canvas');
      if (config.logoEnabled && config.logoData) {
        await addLogoToCanvas(canvas, config);
      }
      resolve(canvas.toDataURL('image/png'));
    }, 100);
  });
}
```

#### ID Management System
- **Sequence Tracking**: Maintains separate ID sequences per product/size combination
- **Gap Detection**: Identifies and reports missing ID sequences
- **Manual Override**: Allows custom starting points while maintaining tracking

#### Logo Integration
```javascript
function addLogoToCanvas(canvas, config) {
  return new Promise((resolve) => {
    const ctx = canvas.getContext('2d');
    const img = new Image();
    
    img.onload = function() {
      const logoSize = (canvas.width * config.logoSize / 100);
      const x = (canvas.width - logoSize) / 2;
      const y = (canvas.height - logoSize) / 2;
      
      // Add white background if padding specified
      if (config.logoPadding > 0) {
        ctx.fillStyle = 'white';
        ctx.fillRect(x - config.logoPadding, y - config.logoPadding, 
                     logoSize + (config.logoPadding * 2), 
                     logoSize + (config.logoPadding * 2));
      }
      
      // Draw logo centered
      ctx.drawImage(img, x, y, logoSize, logoSize);
      resolve();
    };
    
    img.src = config.logoData;
  });
}
```

### 3. PDF Export System

#### Layout Calculation
```javascript
function calculatePDFLayout(config) {
  const A4_WIDTH = 210;   // mm
  const A4_HEIGHT = 297;  // mm
  
  const totalLabelsWidth = config.cols * config.labelWidth + 
                          (config.cols - 1) * config.gapHorizontal;
  const totalLabelsHeight = config.rows * config.labelHeight + 
                           (config.rows - 1) * config.gapVertical;
  
  let marginX, marginY;
  if (config.autoMargins) {
    marginX = (A4_WIDTH - totalLabelsWidth) / 2;
    marginY = (A4_HEIGHT - totalLabelsHeight) / 2;
  } else {
    marginX = parseFloat(config.marginLeft) || 0;
    marginY = parseFloat(config.marginTop) || 0;
  }
  
  return { marginX, marginY, totalLabelsWidth, totalLabelsHeight };
}
```

#### Batch PDF Generation
```javascript
async function generateBatchPDF(qrCodes, config) {
  const pdf = new jsPDF('p', 'mm', 'a4');
  const layout = calculatePDFLayout(config);
  
  // Pre-generate all QR codes in parallel batches
  const qrImages = await generateAllQRCodesWithProgress(qrCodes, config);
  
  // Create PDF pages
  const qrPerPage = config.rows * config.cols;
  const totalPages = Math.ceil(qrImages.length / qrPerPage);
  
  for (let page = 0; page < totalPages; page++) {
    if (page > 0) pdf.addPage();
    
    const startIndex = page * qrPerPage;
    const endIndex = Math.min(startIndex + qrPerPage, qrImages.length);
    
    for (let i = startIndex; i < endIndex; i++) {
      const position = i - startIndex;
      const row = Math.floor(position / config.cols);
      const col = position % config.cols;
      
      const x = layout.marginX + col * (config.labelWidth + config.gapHorizontal);
      const y = layout.marginY + row * (config.labelHeight + config.gapVertical);
      
      addQRToPDF(pdf, qrImages[i], qrCodes[i].value, x, y, config);
    }
  }
  
  return pdf;
}
```

### 4. Configuration Management

#### Settings Persistence
```javascript
const DEFAULT_CONFIG = {
  rows: 9,
  cols: 7,
  labelWidth: 25.4,
  labelHeight: 25.4,
  marginTop: 'auto',
  marginBottom: 'auto',
  marginLeft: 'auto',
  marginRight: 'auto',
  autoMargins: true,
  gapHorizontal: 0,
  gapVertical: 0,
  qrSize: 70,
  errorCorrection: 'M',
  showText: true,
  pdfQuality: 1.0,
  logoEnabled: true,
  logoData: DEFAULT_MOOSE_LOGO,
  logoSize: 20,
  logoPadding: 2
};

function loadConfiguration() {
  const stored = localStorage.getItem('pdfConfig');
  if (stored) {
    const storedConfig = JSON.parse(stored);
    pdfConfig = { ...DEFAULT_CONFIG, ...storedConfig };
    
    // Ensure logo data is preserved if not in stored config
    if (!storedConfig.logoData && DEFAULT_CONFIG.logoData) {
      pdfConfig.logoData = DEFAULT_CONFIG.logoData;
      pdfConfig.logoEnabled = DEFAULT_CONFIG.logoEnabled;
    }
  } else {
    pdfConfig = { ...DEFAULT_CONFIG };
  }
}
```

## Performance Optimizations

### 1. Parallel QR Code Generation
```javascript
async function generateAllQRCodesWithProgress(qrCodes, config) {
  const BATCH_SIZE = 10;
  const results = [];
  
  for (let i = 0; i < qrCodes.length; i += BATCH_SIZE) {
    const batch = qrCodes.slice(i, i + BATCH_SIZE);
    const batchPromises = batch.map(qr => generateSingleQR(qr.value, config));
    
    const batchResults = await Promise.all(batchPromises);
    results.push(...batchResults);
    
    // Update progress
    updateProgress('Generating QR Codes', 
                  `Processing batch ${Math.floor(i/BATCH_SIZE) + 1}...`, 
                  i + batch.length, qrCodes.length);
  }
  
  return results;
}
```

### 2. Memory Management
- **Cleanup Temporary Elements**: Remove DOM elements after QR generation
- **Canvas Optimization**: Reuse canvas contexts where possible
- **Image Data Compression**: Optimize base64 encoding for storage

### 3. Progress Tracking
```javascript
function showProgress(title, message, current, total) {
  const overlay = document.getElementById('progress-overlay');
  const progressFill = document.getElementById('progress-fill');
  const progressText = document.getElementById('progress-text');
  const progressTitle = document.getElementById('progress-title');
  
  progressTitle.textContent = title;
  progressText.textContent = message;
  
  const percentage = total > 0 ? (current / total) * 100 : 0;
  progressFill.style.width = percentage + '%';
  
  overlay.style.display = 'flex';
}
```

## Security Considerations

### 1. Input Validation
- **XSS Prevention**: All user inputs are properly escaped
- **File Upload Validation**: Logo uploads restricted to image formats
- **Pattern Validation**: Category keys and product codes follow strict patterns

### 2. Client-Side Storage
- **No Sensitive Data**: All data stored locally, no server transmission
- **Data Encryption**: Could be enhanced with client-side encryption
- **Backup Recommendations**: Users advised to export data regularly

## Browser Compatibility

### Supported Features
- **Canvas API**: For QR code generation and logo overlay
- **FileReader API**: For logo upload and processing
- **localStorage API**: For data persistence
- **CSS Grid/Flexbox**: For responsive layouts
- **ES6+ Features**: Arrow functions, async/await, destructuring

### Polyfill Requirements
Modern browsers (Chrome 60+, Firefox 55+, Safari 12+, Edge 79+) support all required features natively. No polyfills required for target browsers.

## Testing Strategy

### Unit Testing (Recommended)
```javascript
// Example test structure
describe('QR Code Generation', () => {
  test('generates correct QR code format', () => {
    const result = generateQRValue('RE', 'L', '2501', 1);
    expect(result).toBe('RMT-REL-2501-001');
  });
  
  test('handles ID sequencing correctly', () => {
    // Test ID tracking and increment logic
  });
});

describe('PDF Layout Calculation', () => {
  test('calculates margins correctly for auto-center', () => {
    const config = { rows: 9, cols: 7, labelWidth: 25.4, labelHeight: 25.4 };
    const layout = calculatePDFLayout(config);
    expect(layout.marginX).toBeCloseTo(expectedMargin);
  });
});
```

### Integration Testing
- **End-to-End Workflows**: Complete product creation to PDF export
- **Data Persistence**: localStorage save/load operations
- **Cross-Browser Testing**: Verify functionality across target browsers

### Performance Testing
- **Large Batch Generation**: Test with 1000+ QR codes
- **Memory Usage Monitoring**: Check for memory leaks during batch operations
- **PDF Generation Time**: Measure performance across different configurations

## Deployment

### Single File Distribution
The application is designed as a single HTML file containing:
- All HTML markup
- Complete CSS styling (embedded)
- Full JavaScript functionality (embedded)
- Default assets (base64 encoded)

### CDN Dependencies
External libraries loaded from CDN:
```html
<script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.3/build/qrcode.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
```

### Offline Capabilities
- **Core Functionality**: Works completely offline after initial load
- **CDN Fallbacks**: Consider local copies for offline deployment
- **Progressive Enhancement**: Graceful degradation if CDN resources fail

## Maintenance and Updates

### Version Management
- **Semantic Versioning**: Follow semver for release numbering
- **Migration Scripts**: Automatic data structure upgrades
- **Backward Compatibility**: Maintain support for previous data formats

### Monitoring
- **Error Tracking**: Implement client-side error reporting
- **Usage Analytics**: Optional analytics for feature usage
- **Performance Metrics**: Track generation times and success rates

## Future Enhancements

### Planned Features
1. **Cloud Storage Integration**: Sync data across devices
2. **Batch Import/Export**: CSV/Excel import for bulk product creation
3. **Template System**: Industry-specific templates and formats
4. **Advanced Analytics**: Detailed usage reporting and insights
5. **Multi-language Support**: Internationalization and localization
6. **Plugin System**: Extensible architecture for custom features

### Architecture Improvements
1. **Module System**: Refactor into ES6 modules for better organization
2. **State Management**: Implement centralized state management
3. **Component Architecture**: Move toward reusable component system
4. **Testing Framework**: Add comprehensive automated testing
5. **Build Process**: Implement build pipeline for optimization
6. **TypeScript Migration**: Add type safety and better tooling

---

This technical documentation provides a comprehensive overview of the BulkQR system architecture, implementation details, and maintenance guidelines for developers and contributors.