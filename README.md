# BulkQR - Professional QR Code Management System

A comprehensive single-file HTML application for managing products, generating QR codes with custom logos, and exporting professional PDF labels for commercial printing.

## 🚀 Features

### Product & Category Management
- **Full CRUD Operations**: Create, read, update, and delete categories and products
- **Custom Categories**: Add unlimited product categories with custom names
- **Size Management**: Configure available sizes per category (X, S, M, L, E, O)
- **Product Organization**: Manage products with codes, names, and size variations
- **Data Persistence**: All data stored locally with automatic backup

### QR Code Generation
- **Bulk Generation**: Generate unlimited QR codes with pagination support
- **Custom Format**: `RMT-[TYPE][SIZE]-[YYMM]-[ID]` format for tracking
- **ID Tracking**: Automatic ID management with gap detection
- **Logo Integration**: Embed custom logos in QR code centers
- **Default Branding**: Ships with professional moose logo

### PDF Export System
- **A4 Label Printing**: Optimized for 25.4×25.4mm commercial label sheets
- **Configurable Layout**: Adjust rows, columns, margins, and gaps
- **Professional Output**: High-quality PDF generation with jsPDF
- **Progress Tracking**: Real-time progress bars during PDF generation
- **Print History**: Track all generated PDFs with statistics

### Configuration Management
- **Extensive Customization**: 
  - Page layout (rows/columns)
  - Label dimensions and margins
  - QR code size and error correction
  - Logo settings (size, padding, background)
  - PDF quality settings
- **Auto-Calculate**: Automatic margin calculation for centered layouts
- **Preset Management**: Save and restore configuration presets

### User Interface
- **Professional Design**: Clean, responsive interface
- **Tab Navigation**: Organized into Management, Generation, Configuration, and History
- **Form Validation**: Comprehensive input validation and error handling
- **Progress Feedback**: Visual feedback for all operations
- **Mobile Responsive**: Works on desktop, tablet, and mobile devices

## 🔧 Installation & Setup

### Quick Start
1. **Download**: Clone the repository or download `index.html`
   ```bash
   git clone https://github.com/pv2447407/bulkqr.git
   ```

2. **Open**: Simply open `index.html` in any modern web browser
   - No server required
   - No installation needed
   - Works offline

3. **Start Using**: The application loads with default product categories and is ready to use

### Browser Requirements
- Chrome/Chromium 60+
- Firefox 55+
- Safari 12+
- Edge 79+

## 📖 User Guide

### Getting Started

#### 1. Product Management
Navigate to the **Product Management** tab to set up your inventory:

**Managing Categories:**
- Click **"+ Add Category"** to create new product categories
- Set category name (e.g., "Crystals", "Keychains")
- Define internal key (letters only, e.g., "crystals")
- Select available sizes for the category
- Use **Edit Category** to modify names and available sizes
- **Delete Category** removes the category and all its products

**Managing Products:**
- Click **"+ Add Product"** to add items to categories
- Enter product code (2 letters, e.g., "RE")
- Set product name (e.g., "Rectangle")
- Select available sizes from category options
- Edit or delete products as needed

#### 2. QR Code Generation
Switch to **Generate QR Codes** tab:

**Basic Generation:**
1. Select category (e.g., Crystals)
2. Choose product (e.g., RE - Rectangle)
3. Pick size (e.g., L - Large)
4. Set year/month (YYMM format)
5. Enter quantity or use suggested starting ID
6. Click **"Generate QR Codes"**

**QR Code Format:**
Generated codes follow the format: `RMT-[TYPE][SIZE]-[YYMM]-[ID]`
- Example: `RMT-REL-2501-001` (Rectangle Large, Jan 2025, ID 001)

**Features:**
- Automatic ID tracking prevents duplicates
- Gap detection identifies missing sequences
- Unlimited quantity with pagination
- Logo overlay on all generated codes

#### 3. PDF Export
After generating QR codes:

**Export Process:**
1. Click **"Export PDF"** 
2. Monitor progress bar during generation
3. PDF downloads automatically when complete
4. Filename includes product details and date range

**PDF Features:**
- A4 format optimized for label printing
- 25.4×25.4mm label dimensions
- Configurable layouts (default 7×9 grid)
- Professional margins and spacing
- Embedded logos in QR codes

#### 4. Configuration
Customize the system in the **Configuration** tab:

**Page Layout:**
- Rows and columns per page
- Label dimensions (width/height)
- Margins and gaps between labels
- Auto-calculate for centered layouts

**QR Code Settings:**
- Size percentage of label area
- Error correction level (L/M/Q/H)
- Show/hide text labels
- PDF generation quality

**Logo Settings:**
- Enable/disable logos in QR codes
- Upload custom logo images
- Set logo size (10-30% of QR code)
- Configure background padding

**Print History:**
- View all generated PDF sessions
- Track quantities and date ranges
- Export statistics and summaries

### 5. Print History
Monitor usage in the **Print History** tab:
- Complete list of all PDF exports
- Product details and quantities
- Generation dates and times
- Total statistics and summaries

## 🎨 Customization

### Logo Integration
The system includes a default moose logo, but you can customize:

**Upload Custom Logo:**
1. Go to Configuration → Logo Settings
2. Click "Upload Custom Logo"
3. Select PNG, JPG, or other image formats
4. Adjust size and padding as needed
5. Save configuration

**Logo Requirements:**
- Recommended: Square images (1:1 aspect ratio)
- Formats: PNG, JPG, GIF, SVG
- Size: 10-30% of QR code area
- Background: Transparent or white recommended

### Advanced Configuration

**Label Printing:**
- Default: 7×9 grid (63 labels per page)
- A4 paper: 210×297mm
- Label size: 25.4×25.4mm (1×1 inch)
- Compatible with commercial label sheets

**QR Code Formatting:**
- Error correction levels affect logo compatibility
- Higher error correction (H) supports larger logos
- Medium (M) recommended for balanced performance
- Text labels can be disabled for logo-only printing

## 📊 Technical Details

### Data Storage
- **Local Storage**: All data persists in browser localStorage
- **Automatic Migration**: Seamlessly upgrades data structure
- **Export/Import**: Manual backup through browser tools
- **No Server**: Completely client-side application

### Performance
- **Optimized Generation**: Parallel QR code creation
- **Progress Tracking**: Real-time feedback for large batches
- **Memory Efficient**: Handles thousands of QR codes
- **Fast PDF Export**: Optimized rendering pipeline

### File Structure
```
bulkqr/
├── index.html          # Complete application (2,663 lines)
├── README.md           # This documentation
└── .git/              # Git repository data
```

### Dependencies
All dependencies are loaded via CDN:
- **QRCode.js**: QR code generation library
- **jsPDF**: PDF creation and export
- **Browser APIs**: File handling, localStorage, canvas manipulation

## 🔄 Workflow Examples

### Typical Usage Workflow
1. **Setup** (One-time):
   - Add product categories (Crystals, Keychains, etc.)
   - Add products with codes and sizes
   - Configure logo and layout preferences

2. **Daily Operations**:
   - Generate QR codes for specific products
   - Export PDFs for label printing
   - Print labels on commercial label sheets
   - Apply labels to physical products

3. **Management**:
   - Review print history for tracking
   - Add new products as inventory expands
   - Update configurations as needed

### Business Integration
- **Inventory Tracking**: QR codes link to product databases
- **Quality Control**: Unique IDs for batch tracking
- **Customer Service**: Scannable codes for product information
- **Marketing**: Branded QR codes with company logos

## 🚀 Advanced Features

### ID Management
- **Automatic Sequencing**: Continues from last generated ID
- **Gap Detection**: Identifies missing sequence numbers
- **Manual Override**: Set custom starting points
- **Category Isolation**: Separate ID sequences per product/size

### Batch Operations
- **Unlimited Generation**: No arbitrary limits on quantity
- **Pagination**: Efficient display of large batches
- **Bulk Export**: Single PDF for entire batch
- **Progress Monitoring**: Real-time generation status

### Print Optimization
- **Commercial Labels**: Optimized for Avery and similar brands
- **Bleed Margins**: Proper spacing for cutting
- **Print Registration**: Accurate alignment marks
- **Quality Control**: High-resolution output

## 🔧 Troubleshooting

### Common Issues

**QR Codes Not Generating:**
- Check that all fields are filled
- Verify product exists in selected category
- Ensure valid year/month format (YYMM)

**PDF Export Failing:**
- Clear browser cache and retry
- Reduce batch size if memory issues
- Check browser popup blocker settings

**Logo Not Appearing:**
- Verify logo is uploaded and enabled
- Check logo size settings (10-30%)
- Ensure image format is supported

**Data Loss:**
- Data is stored in browser localStorage
- Clearing browser data will reset application
- Export configurations before browser maintenance

### Browser Compatibility
**Chrome/Chromium:** Full feature support
**Firefox:** Full feature support
**Safari:** Full feature support (macOS/iOS)
**Edge:** Full feature support (Chromium-based)

### Performance Tips
- Generate large batches in smaller chunks (500-1000 at a time)
- Use Medium error correction for optimal performance
- Clear print history periodically for better performance
- Close other browser tabs during large PDF exports

## 📞 Support

### Getting Help
- **Issues**: Report bugs via GitHub Issues
- **Features**: Request enhancements through GitHub
- **Documentation**: Refer to this README for guidance

### Contributing
1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

## 📝 License

This project is open source. Feel free to use, modify, and distribute according to your needs.

## 🎯 Roadmap

### Planned Features
- [ ] Cloud storage integration
- [ ] Batch import from CSV/Excel
- [ ] Additional QR code formats
- [ ] Template system for different industries
- [ ] Multi-language support
- [ ] Advanced reporting and analytics

### Version History
- **v1.0** - Initial release with core functionality
- Complete product and category management
- QR code generation with logo support
- PDF export with professional layouts
- Configuration system and print history

---

**Built with ❤️ using Claude Code**

For the latest updates and support, visit: [GitHub Repository](https://github.com/pv2447407/bulkqr)