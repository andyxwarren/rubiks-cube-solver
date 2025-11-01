# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A web-based Rubik's Cube Solver that uses camera detection to scan cube faces and provides step-by-step solving instructions. The application is built as a **single standalone HTML file** using pure vanilla JavaScript (no frameworks), making it ideal for rapid prototyping and testing.

## Architecture

### Single-File Application Structure

The entire application (index.html, 1435 lines) contains:
- **HTML structure** for 7 main screens (home, calibration, scanning, editing, solving, guided, complete)
- **Embedded CSS** (~430 lines) with responsive mobile-first design
- **Vanilla JavaScript application** (~990 lines) with state management

### Key Components

**Screen Management System** (index.html:739-743)
- Single-page application with screen switching
- 7 screens: `home-screen`, `calibration-screen`, `scanning-screen`, `editing-screen`, `solving-screen`, `guided-screen`, `complete-screen`
- `app.showScreen(screenId)` controls navigation

**Color Detection Engine** (index.html:676-724)
- HSV-based color matching for higher accuracy than RGB
- `rgbToHsv()` converts camera RGB to HSV color space
- `calculateHsvDistance()` finds closest color match
- `matchColor()` returns color + confidence score
- Supports color calibration to adapt to specific cubes

**Camera Systems** (index.html:749-878, 915-996)
- Calibration camera: samples center colors for 6 cube faces
- Scanning camera: real-time detection with 3x3 grid overlay
- Uses `navigator.mediaDevices.getUserMedia()` for camera access
- Lighting quality analysis to guide users

**Cube State Management** (index.html:643-650)
- 6 faces stored as arrays: F (Front), B (Back), U (Up), D (Down), L (Left), R (Right)
- Each face is 9 colors: `['W', 'W', 'W', 'W', 'W', 'W', 'W', 'W', 'W']`
- Standard cube notation: W=White, Y=Yellow, R=Red, O=Orange, B=Blue, G=Green

**3D Visualization** (index.html:1295-1399)
- Three.js used for interactive 3D cube rendering
- Loaded from CDN: `https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js`
- Animates automatically with rotation
- Updates in real-time during editing

**Solving Algorithm** (index.html:1181-1225)
- Layer-by-layer solving approach
- Returns sequence of moves with descriptions
- Move notation: R (Right), U (Up), F (Front), with ' for counter-clockwise

## Testing & Deployment

### Local Testing
```bash
# Desktop testing - double-click index.html
# Opens in default browser immediately
```

### Local Server (for camera access on mobile)
```bash
# Python
python -m http.server 8000

# Node.js
npx http-server -p 8000
```

### GitHub Pages Deployment
1. File must be named `index.html` for root deployment
2. Enable Pages in repository settings: Settings → Pages → main branch
3. URL format: `https://USERNAME.github.io/rubiks-cube-solver/`
4. HTTPS is automatically enabled (required for camera access)

### Camera Requirements
- **Desktop:** Requires local server or HTTPS (file:// protocol won't work)
- **Mobile:** Requires HTTPS (GitHub Pages provides this automatically)
- Browsers: Chrome/Safari recommended; must support getUserMedia API

## Development Guidelines

### Code Organization
The application follows a single-object pattern with `app` as the global namespace:
- State variables at top (index.html:642-656)
- Utility functions for color matching (index.html:676-737)
- Screen management (index.html:739-743)
- Feature-specific methods grouped together

### Making Changes

**Modifying UI/Text:**
- All UI is in HTML section (lines 1-637)
- Text strings are inline (no i18n system)
- CSS uses gradient theme: `linear-gradient(135deg, #667eea 0%, #764ba2 100%)`

**Modifying Behavior:**
- All logic in JavaScript section (lines 638-1432)
- State changes trigger UI updates
- Camera operations are async (use try-catch)

**Adding Features:**
1. Add HTML for new screen/component
2. Add CSS styling if needed
3. Add JavaScript method to `app` object
4. Wire up with onclick or event listeners

### Color Detection Tuning
- Calibration samples 50x50px area from camera center (index.html:790-797)
- Detection samples 5 random points per square with median (index.html:926-945)
- HSV distance weighting: Hue × 2, Saturation × 1, Value × 1 (index.html:703)
- Confidence threshold: 70% triggers auto-capture (index.html:910)

### Cube Validation
The `validateCube()` method (index.html:1139-1161) checks:
- Each color appears exactly 9 times (54 total stickers)
- All 6 center colors are different

## Common Tasks

### Update Three.js Version
Change CDN URL at line 7:
```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
```

### Change Color Scheme
Modify gradient variables in CSS (multiple locations):
```css
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
```

### Improve Solving Algorithm
Replace `generateSolution()` method (index.html:1181-1225) with:
- Kociemba algorithm implementation
- External API call (requires adding backend)
- Alternative solving methods (CFOP, Roux, etc.)

### Add Analytics
Insert before `</head>` tag (around line 431):
```html
<script async src="https://www.googletagmanager.com/gtag/js?id=YOUR_ID"></script>
```

## Technical Constraints

### Single-File Architecture
- Everything embedded: no separate CSS/JS files
- External dependencies loaded via CDN
- No build process or compilation
- No module system (ES5 compatible)

### Browser Compatibility
- Requires ES6 features (arrow functions, let/const, template literals)
- Three.js requires WebGL support
- Camera requires getUserMedia API (2018+ devices)

### Performance Considerations
- File size: ~95KB total (loads in <2 seconds on 4G)
- Color detection runs every 200ms during scanning (index.html:873)
- 3D rendering at 60fps (index.html:1323-1328)

## Migration Path

### Converting to React (if needed)
1. Extract screen components from HTML sections
2. Convert `app` object methods to hooks/components
3. Move state to React state management
4. Keep same workflow/screens

### Adding Backend (if needed)
1. Create Express/Node.js server
2. Move solving algorithm to backend API
3. Add database for user accounts/history
4. Keep frontend mostly unchanged

### Converting to Mobile App (if needed)
1. Wrap in Cordova or Capacitor
2. Use WebView to run existing HTML
3. Add native camera plugins if needed
4. Package for app stores

## Documentation Files

- **README-START-HERE.txt** - Overview and quick start guide
- **TESTING-GUIDE.md** - Detailed testing and deployment instructions
- **VERSION-RECOMMENDATION.md** - Comparison of different versions

## Key Workflows

### User Flow
1. Home → Start Solving
2. Calibration → Sample 6 colors (skippable)
3. Scanning → Capture 6 faces with camera
4. Editing → Manually correct any mistakes
5. Solving → Algorithm computes solution
6. Guided → Step-by-step instructions
7. Complete → Celebration screen

### Development Flow
1. Edit index.html directly
2. Save changes
3. Refresh browser (Ctrl+R / Cmd+R)
4. Test immediately (no build step)
5. Commit and push to GitHub
6. GitHub Pages updates automatically (1-2 minutes)

## Important Implementation Details

### Camera Stream Management
- Always stop camera streams when switching screens to prevent memory leaks
- Use `getTracks().forEach(track => track.stop())` pattern
- Separate streams for calibration and scanning (index.html:671-672, 834-839, 1013-1022)

### 3D Cube Coordinate System
- Position indices map to visual grid positions
- Face mapping: Right(x=1), Left(x=-1), Up(y=1), Down(y=-1), Front(z=1), Back(z=-1)
- Each face uses different index calculations (index.html:1355-1378)

### Move Notation
- Standard Rubik's notation: R, U, F, L, D, B
- Prime (') means counter-clockwise: R', U', F'
- 2 means 180 degrees: U2, R2
