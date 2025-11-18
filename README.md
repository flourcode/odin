# 🛰️ ODIN MAP - DIY Common Operational Picture Dashboard

A single-file web application that visualizes real-time air, sea, and emergency services data in a military-inspired command center interface. Built as an educational full-stack learning project demonstrating public API integration, geospatial visualization, and real-time data streaming.

![ODIN MAP Interface](https://img.shields.io/badge/Status-Educational%20Demo-blue) ![No Dependencies](https://img.shields.io/badge/Dependencies-Zero%20Build%20Required-green) ![Single File](https://img.shields.io/badge/Architecture-Single%20HTML%20File-orange)

---

## 🚨 CRITICAL DISCLAIMER

**This is an educational demonstration only.**

- ❌ **DO NOT** use for actual navigation, flight safety, or emergency response
- ❌ **DO NOT** rely on this data for operational decision-making
- ⚠️ Public data sources have latency, rate limits, and potential errors
- ✅ **DO** use this to learn real-time data integration and visualization techniques

---

## ✨ Features

### Real-Time Data Visualization
- **Aircraft Tracking**: Live ADSB data showing commercial and general aviation
- **Maritime Traffic**: Real-time AIS vessel positions and movements
- **Emergency Incidents**: 911 dispatch data (Seattle only)
- **Weather Radar**: NEXRAD radar overlay from US airports

### Interactive Interface
- **Multiple Airport Views**: Pre-configured for SEA, LAX, JFK, ORD, ATL, DFW, DEN, SFO, MIA, LAS
- **Live ATC Audio**: Stream air traffic control communications
- **Click-to-Follow**: Select any aircraft, vessel, or incident for detailed information
- **Historical Trails**: Visual path history for moving objects
- **Responsive Design**: Works on desktop and mobile devices

### Technical Capabilities
- **Zero Build Process**: Runs directly in any modern browser
- **No Server Required**: All proxy routing handled through public endpoints
- **Real-Time Updates**: Combines polling (3s for aircraft) and WebSocket streaming (vessels)
- **Military Aesthetics**: High-contrast, command-center inspired UI

---

## 🚀 Quick Start

### Installation

1. **Download the application**
   ```bash
   # Clone or download index.html
   curl -O https://your-repo/index.html
   ```

2. **Open in browser**
   ```bash
   # Simply double-click index.html or
   open index.html  # macOS
   start index.html # Windows
   xdg-open index.html # Linux
   ```

3. **That's it!** No npm install, no build process, no configuration.

### First Run

1. The map will load centered on Seattle (SEA) by default
2. Aircraft will appear as colored triangular markers within seconds
3. Ships will stream in via WebSocket connection
4. Click the **ATC OFFLINE** button to start live audio

---

## 🎯 User Interface Guide

### Header Controls (Top Right)

| Control | Function |
|---------|----------|
| **Location Dropdown** | Switch between 10 major US airports |
| **RADAR Toggle** | Show/hide NEXRAD weather radar overlay |
| **Time Display** | Current local time (updates every second) |

### Metrics Panel (Left Side)

- **AIRCRAFT**: Total aircraft currently tracked
- **VESSELS**: Total ships in the selected region
- **INCIDENTS**: 911 calls (Seattle only, shows 0 for other cities)

### Legend Panel (Right Side)

Visual reference for:
- Aircraft categories (commercial, general aviation, military, helicopters)
- Vessel types (cargo, tanker, passenger)
- Incident markers

### Audio Controls (Bottom Left)

- **ATC LIVE/OFFLINE**: Toggle live air traffic control audio
- **Volume Slider**: Adjust audio level
- Audio source automatically switches when changing locations

### Information Panel (Bottom Center)

Appears when clicking any map object:
- **Aircraft**: Callsign, type, altitude, speed, climb/descent status
- **Vessel**: Name, type, speed, heading, destination
- **Incident**: Type, address, ID number, timestamp

---

## 🛠️ Customization Guide

### Changing Default Location

In the `CONFIG` object, modify the initial location:

```javascript
const CONFIG = {
    INITIAL_LOCATION: 'LAX',  // Change from 'SEA' to any configured airport
    // ...
};
```

### Adding New Airports

Add to `CONFIG.LOCATIONS`:

```javascript
LOCATIONS: {
    YYZ: {
        name: 'YYZ',
        lat: 43.6777,
        lon: -79.6248,
        zoom: 10,
        audio: 'https://d.liveatc.net/cyyz',
        bbox: [[43.5, -79.8], [43.8, -79.4]]  // [SW corner, NE corner]
    }
}
```

### Adjusting Trail Length

Modify the historical path length for aircraft/vessels:

```javascript
// In fetchPlanes() function, find:
if (trail.length > 200) trail.shift();
// Change 200 to your preferred value (higher = longer trails)
```

### Customizing Colors

All colors are defined in `CONFIG.COLORS`:

```javascript
COLORS: {
    COMM: '#FFFF00',      // Commercial aircraft (currently blue)
    GA: '#90EE90',        // General aviation (currently green)
    HELI: '#FFB6C1',      // Helicopters (currently pink)
    MIL: '#FF6B6B',       // Military (currently red)
    CARGO: '#1E90FF',     // Cargo ships (currently bright blue)
    TANKER: '#FFD700',    // Tankers (currently gold)
    PASSENGER: '#32CD32'  // Passenger vessels (currently lime)
}
```

### Changing Map Theme

The map appearance is controlled by CSS filters on `.map-tiles`:

```css
.map-tiles {
    /* Current: Dark mode with high contrast */
    filter: invert(100%) grayscale(90%) hue-rotate(130deg) brightness(90%) contrast(130%);
    
    /* Try these alternatives: */
    /* Sepia: invert(100%) sepia(60%) hue-rotate(20deg); */
    /* High contrast: invert(100%) contrast(200%) brightness(80%); */
    /* Minimal: invert(100%) grayscale(100%) brightness(95%); */
}
```

### Setting Radar Default State

To have radar ON by default, ensure these three elements are configured:

```javascript
// 1. In state object:
const state = {
    isRadarOn: true,  // Set to true
    // ...
};

// 2. In init() function, add:
function init() {
    // ... other initialization ...
    initializeRadarOn();  // Call this function
}

// 3. In HTML, add 'active' class:
<button id="radar-control" class="radar-control active">RADAR: ON</button>
```

---

## 🧭 Architecture Overview

### Data Flow

```
┌─────────────────────────────────────────────────────────────┐
│                       ODIN MAP                              │
│                    (Single HTML File)                       │
└─────────────────────────────────────────────────────────────┘
         │                    │                    │
         ▼                    ▼                    ▼
    ┌────────┐          ┌─────────┐         ┌──────────┐
    │ ADSB   │          │   AIS   │         │  NEXRAD  │
    │ API    │          │ WebSocket│        │  Tiles   │
    │Polling │          │Streaming│         │ Polling  │
    └────────┘          └─────────┘         └──────────┘
    Every 3s             Real-time           On demand
```

### Key Technologies

- **Leaflet.js**: Map rendering and marker management
- **Fetch API**: HTTP polling for aircraft and incidents
- **WebSocket**: Real-time ship position streaming
- **HTML5 Audio**: Live ATC audio streaming
- **CSS Filters**: Dark theme map transformation

### File Structure

Despite being a single file, the code is organized into clear sections:

```
index.html
├── HEAD
│   ├── Meta tags (mobile optimization, PWA support)
│   ├── Font imports (Share Tech Mono, Rajdhani)
│   └── Leaflet CSS
├── STYLE
│   ├── Core layout & reset
│   ├── Map tile filters
│   ├── Header & controls
│   ├── Metrics panel (left sidebar)
│   ├── Legend panel (right sidebar)
│   └── Info panel (bottom)
├── BODY
│   ├── Map container
│   ├── Header (location title, controls)
│   ├── Metrics panel
│   ├── Legend panel
│   ├── Audio controls
│   └── Info panel (hidden by default)
└── SCRIPT
    ├── CONFIG (locations, colors, API endpoints)
    ├── State management
    ├── Leaflet initialization
    ├── Aircraft polling (fetchPlanes)
    ├── Vessel streaming (connectWebSocket)
    ├── Incident polling (fetchFireIncidents)
    ├── Marker rendering & updates
    ├── UI event handlers
    └── Application initialization
```

---

## 📡 Data Sources

| Type | Source | Method | Update Rate | Notes |
|------|--------|--------|-------------|-------|
| **Aircraft** | api.airplanes.live | HTTP Polling | 3 seconds | Requires lat/lon center point |
| **Vessels** | ports-production.up.railway.app | WebSocket | Real-time | Requires bounding box subscription |
| **Weather** | mesonet.agron.iastate.edu | Tile Layer | On-demand | US NEXRAD radar data |
| **ATC Audio** | d.liveatc.net | Audio Stream | Live | Public air traffic control feeds |
| **Incidents** | data.seattle.gov | HTTP Polling | 30 seconds | Seattle 911 dispatch only |

### API Rate Limits

- **Aircraft API**: Generally permissive for reasonable polling (3-5s intervals)
- **AIS WebSocket**: No strict limits, but may disconnect after inactivity
- **Radar Tiles**: Cached by Leaflet, no per-request limits
- **Seattle 911**: Public data portal, no authentication required

---

## 🔧 Advanced Modifications

### Adding New Data Sources

To integrate a new data type:

1. **Add to CONFIG**:
   ```javascript
   API_ENDPOINTS: {
       PLANES: 'https://api.airplanes.live/v2/point/{lat}/{lon}/150',
       SHIPS: 'wss://ports-production-8906.up.railway.app',
       YOUR_DATA: 'https://your-api-endpoint.com/data'  // Add here
   }
   ```

2. **Create Fetch Function**:
   ```javascript
   async function fetchYourData() {
       try {
           const response = await fetch(CONFIG.API_ENDPOINTS.YOUR_DATA);
           const data = await response.json();
           // Process and render your data
       } catch (error) {
           console.error('Your data fetch failed:', error);
       }
   }
   ```

3. **Add Polling in startTracking()**:
   ```javascript
   function startTracking() {
       fetchPlanes();
       setInterval(fetchPlanes, 3000);
       
       connectWebSocket();
       
       fetchYourData();  // Initial call
       setInterval(fetchYourData, 10000);  // Every 10 seconds
   }
   ```

### Custom Marker Icons

Create new SVG icons in the `CONFIG.ICONS` object:

```javascript
ICONS: {
    CUSTOM: `<svg>...</svg>`
}
```

Then assign in your rendering logic:

```javascript
const icon = L.divIcon({
    html: CONFIG.ICONS.CUSTOM,
    className: 'custom-marker',
    iconSize: [24, 24]
});
```

### Persistent Data Storage

To save user preferences:

```javascript
// Save location preference
function changeLocation(newLocation) {
    state.location = newLocation;
    localStorage.setItem('preferred_location', newLocation);
    // ... rest of location change logic
}

// Load on init
function init() {
    const saved = localStorage.getItem('preferred_location');
    if (saved && CONFIG.LOCATIONS[saved]) {
        state.location = saved;
    }
    // ... rest of initialization
}
```

---

## 🐛 Troubleshooting

### Aircraft Not Appearing

- **Check console**: Open browser DevTools (F12) and look for API errors
- **Verify location**: Some areas have sparse ADSB coverage
- **Wait 10 seconds**: Initial data fetch may take time
- **Try different airport**: Switch to LAX or JFK (high traffic areas)

### Ships Not Loading

- **WebSocket connection**: Check console for "WS Connected" message
- **Bounding box**: Verify bbox coordinates include water (not landlocked)
- **Maritime traffic**: Some areas have minimal ship activity
- **Reconnect**: Change location to re-establish WebSocket

### Audio Not Playing

- **Browser autoplay**: Click the ATC button (browsers block auto-play)
- **Stream offline**: LiveATC feeds occasionally go down
- **Volume**: Check both in-app and system volume settings
- **Try different airport**: Switch locations to test different feeds

### Radar Not Showing

- **NEXRAD coverage**: Only available in the United States
- **Time of day**: Radar may show clear skies (no precipitation)
- **Toggle twice**: Click RADAR button off then on to refresh
- **Check zoom level**: Radar tiles load at specific zoom ranges

### Map Not Loading

- **Internet connection**: Requires active connection for tiles
- **Ad blocker**: May interfere with OpenStreetMap tile requests
- **Browser compatibility**: Requires modern browser (Chrome 90+, Firefox 88+, Safari 14+)
- **Console errors**: Check DevTools for specific tile loading errors

---

## 📱 Mobile Optimization

The application is fully responsive and touch-optimized:

- **Viewport settings**: Prevents unwanted zooming on input focus
- **Touch gestures**: Pinch-to-zoom, two-finger pan on map
- **Tap-to-select**: Touch any marker to view details
- **Adaptive panels**: Metrics and legend auto-hide on smaller screens
- **PWA support**: Can be added to home screen on iOS/Android

---

## 🎓 Educational Use Cases

### For Students Learning Web Development

- **API Integration**: Real-world examples of REST and WebSocket
- **Asynchronous JavaScript**: Promises, async/await, error handling
- **State Management**: Complex application state without frameworks
- **DOM Manipulation**: Dynamic UI updates and event handling
- **CSS Filters**: Advanced visual effects without image editing

### For Federal Sales Professionals

- **Data Visualization**: Presenting complex information clearly
- **Real-time Systems**: Understanding operational data flows
- **Decision Support**: How COP dashboards aid situational awareness
- **Technology Demo**: Engaging way to discuss capabilities with stakeholders

### For Aviation Enthusiasts

- **Flight Tracking**: Understanding ADSB technology
- **ATC Communications**: Listen to live controller-pilot conversations
- **Aircraft Identification**: Learn type codes and callsign formats
- **Traffic Patterns**: Observe approach/departure procedures

---

## 🔐 Security & Privacy

### No Personal Data Collection

- **No analytics**: Zero tracking scripts or data collection
- **No accounts**: No login, registration, or user profiles
- **No cookies**: Application state exists only in memory
- **Public data only**: All sources are already publicly accessible

### Safe to Host

- **Static HTML**: No server-side processing or database
- **No API keys**: All endpoints are public (no secrets to expose)
- **CORS handled**: Proxies built into data sources
- **Client-side only**: Runs entirely in the user's browser

---

## 🚢 Deployment Options

### Option 1: Local File

Simply open `index.html` in any browser. No server needed.

### Option 2: GitHub Pages

```bash
# Create a repository
git init
git add index.html README.md
git commit -m "Initial commit"

# Push to GitHub
git remote add origin https://github.com/yourusername/odin-map.git
git push -u origin main

# Enable GitHub Pages in repository settings
# Your app will be live at: https://yourusername.github.io/odin-map/
```

### Option 3: Netlify Drop

1. Go to https://app.netlify.com/drop
2. Drag and drop `index.html`
3. Get instant public URL

### Option 4: Vercel

```bash
# Install Vercel CLI
npm i -g vercel

# Deploy
vercel --prod
```

---

## 🤝 Contributing

This is an educational project. If you create modifications or improvements:

1. **Fork the concept**: Create your own version
2. **Share learnings**: Document what you discovered
3. **Respect data sources**: Don't abuse public APIs with excessive polling
4. **Give credit**: Link back to original sources and inspirations

---

## 📄 License

This project is provided as-is for educational purposes. 

**External Resources**:
- Leaflet.js: BSD 2-Clause License
- OpenStreetMap data: ODbL
- Aircraft data: Public ADSB sources
- AIS data: Public maritime sources
- NEXRAD data: US Government (public domain)
- LiveATC: Public ATC communications

---

## 🙏 Acknowledgments

- **ADS-B Exchange** and **airplanes.live** for aircraft data
- **LiveATC.net** for air traffic control audio streams
- **Iowa State University** for NEXRAD radar tile service
- **OpenStreetMap** contributors for base map data
- **Leaflet.js** team for the mapping library
- **Air Traffic Controllers** everywhere for their critical work

---

## 📞 Support

### Questions?

- **Technical issues**: Check browser console (F12) for errors
- **Feature requests**: Document your use case and desired functionality
- **Learning resources**: See "Educational Use Cases" section above

### Not a Navigation Tool

**Remember**: This is a learning project demonstrating API integration and data visualization. Always use official sources for actual navigation, flight planning, or emergency information.

---

**Version**: 1.0  
**Last Updated**: November 2025  
**Compatibility**: Modern browsers (Chrome 90+, Firefox 88+, Safari 14+, Edge 90+)  
**File Size**: ~50KB (single HTML file)  
**Dependencies**: Zero (runs in any browser)

---

Built with respect for Air Traffic Controllers and the professionals who keep our skies safe. 🛩️
