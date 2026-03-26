# Mihomo Dashboard Specification

## Overview
A lightweight web dashboard for managing and monitoring the Mihomo proxy client via its REST API.

---

## 1. Architecture

### 1.1 Technology Stack
- **Frontend**: Vanilla HTML5, CSS3, JavaScript (ES6+)
- **Backend API**: Mihomo REST API (external service)
- **Communication**: Fetch API (HTTP requests)

### 1.2 API Configuration
| Property | Value | Notes |
|----------|-------|-------|
| Default Base URL | `http://127.0.0.1:9090` | Configurable via Settings |
| Storage | `localStorage` | Key: `mihomo_api_url` |
| Content-Type | `application/json` | All requests |

---

## 2. UI Design

### 2.1 Color Palette
| Name | Value | Usage |
|------|-------|-------|
| Background | `#1a1a2e` | Page background |
| Card BG | `#16213e` | Card containers |
| Accent | `#00d4aa` | Primary accent (buttons, headings, status) |
| Secondary BG | `#0f3460` | Status values, buttons, connection items |
| Text Primary | `#eee` | Main text |
| Text Secondary | `#888` | Labels, descriptions |
| Error | `#e94560` | Error messages |
| Warning | `#f0a500` | Proxy groups, medium latency |
| Info | `#6496ff` | Sub-groups |

### 2.2 Layout
- **Container**: Max-width 1600px, centered
- **Padding**: 20px body, 20px card internal
- **Cards**: Rounded (12px), shadow (0 4px 6px rgba(0,0,0,0.3))
- **Responsive**: CSS Grid with multi-column layout for proxy groups
- **Proxy Tree**: Multi-column grid (minmax 380px, 1fr))

### 2.3 Typography
- Font Family: System fonts (`-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif`)
- Heading 1: Centered, accent color
- Heading 2: 1.2em, accent color, 15px bottom margin

---

## 3. Features

### 3.1 Settings
**Storage**: `localStorage.setItem('mihomo_api_url', url)`

Features:
- Configurable API endpoint URL input field
- URL validation before saving
- Test connection button to verify connectivity
- Persists across sessions

### 3.2 Mode Control
**Endpoint**: `PATCH /configs`

Available modes:
| Mode | Button | Emoji | Description |
|------|--------|-------|-------------|
| rule | Rule | 📋 | Route traffic based on rules configuration |
| global | Global | 🌍 | All traffic through GLOBAL proxy selection |
| direct | Direct | ➡️ | All traffic direct (no proxy) |

### 3.3 Proxy Selection (Hierarchical Tree View)
**Endpoint**: `PUT /proxies/GLOBAL`

Features:
- **Hierarchical display**: Top-level groups, sub-groups, and leaf proxies
- **All groups folded by default**: Click to expand/collapse
- **Visual distinction**:
  - Top-level groups: Orange/gold styling (`#f0a500`)
  - Sub-groups: Blue styling (`#6496ff`)
  - Leaf proxies: Standard dark styling
- **Group info displayed**: Type (Selector/URLTest/LoadBalance/Fallback), item count, currently selected proxy (`now`)
- **Latency display**: Groups show latency of their selected proxy (`now` node)
- **Selection**: Click group/proxy header to select (highlighted when active)
- **Test buttons**: Individual test button on each proxy and group

### 3.4 Latency Testing
**Endpoints**: `GET /proxies/{name}/delay`

Test modes:
| Button | Description |
|--------|-------------|
| 🚀 Test All Latency | Test all leaf proxies + all groups (their `now` nodes) |
| 📁 Test Group Latency | Test only proxy groups (their `now` nodes) |
| 🧪 Test (individual) | Test single proxy or group |

Features:
- Progress bar with percentage
- Status counter (good/medium/bad/error)
- Real-time latency badge updates
- Results cached per item

### 3.5 Rules Info (Rule Mode)
**Endpoint**: `GET /rules`

Features:
- Displays which proxy groups are used in rules
- Only visible when in Rule mode
- Helps users understand which groups actually matter
- Shows count of unique proxy groups used

### 3.6 Active Connections
**Endpoint**: `GET /connections`

- Displays up to 20 most recent connections
- Shows: Host + Proxy chain
- Auto-scroll container (max-height: 300px)

### 3.7 Debug Tool
**Endpoints**: Various `/` endpoints

Features:
- Collapsible debug panel
- Fetch and display raw API responses
- Syntax-highlighted JSON
- Available endpoints: /configs, /proxies/GLOBAL, /proxies, /rules, /connections, /providers/proxies

---

## 4. API Endpoints

### 4.1 Get Configuration
```
GET /configs
Response: { mode: string, ... }
```

### 4.2 Get Proxy Info
```
GET /proxies/{name}
Response: { name: string, type: string, now: string, all: string[], history: [...] }
```

### 4.3 Get All Proxies
```
GET /proxies
Response: { proxies: { [name]: ProxyInfo } }
```

### 4.4 Update Mode
```
PATCH /configs
Body: { mode: "rule" | "global" | "direct" }
```

### 4.5 Update Proxy
```
PUT /proxies/GLOBAL
Body: { name: string }
```

### 4.6 Test Latency
```
GET /proxies/{name}/delay?url=http://www.gstatic.com/generate_204&timeout=5000
Response: { delay: number }
```

### 4.7 Get Connections
```
GET /connections
Response: { connections: Array<{ metadata: {...}, chains: string[] }> }
```

### 4.8 Get Rules
```
GET /rules
Response: { rules: Array<[type, value, proxy]> }
```

---

## 5. Behavior

### 5.1 Auto-Refresh
- Interval: 5 seconds
- Triggered by: `setInterval(refreshAll, 5000)`
- Initial load: On page load
- Paused during latency testing

### 5.2 Proxy Group Types
| Type | Behavior | Has `now`? |
|------|----------|------------|
| Selector | User manually picks one proxy | ✅ Yes |
| URLTest | Auto-picks lowest latency proxy | ✅ Yes |
| LoadBalance | Distributes traffic across proxies | ✅ Yes |
| Fallback | Picks first working proxy | ✅ Yes |

### 5.3 Latency Test Logic
- **Groups**: Test their `now` node (currently selected proxy)
- **Leaf proxies**: Test directly
- **DIRECT**: Skipped (no latency test needed)
- **Caching**: Results cached under item name

### 5.4 Message Display
- Success: Green background (`#00d4aa33`), green border
- Error: Red background (`#e9456033`), red border
- Duration: 3 seconds (auto-hide)

### 5.5 Error Handling
- Network errors display in status area
- Error message includes: "Make sure mihomo is running on {API_BASE}"

---

## 6. Component Hierarchy

```
body
├── h1 (title)
├── .card (Settings)
│   ├── API URL input
│   ├── Save/Test buttons
│   └── #settings-message
├── .card (Mode Control)
│   ├── h2
│   ├── .controls (3 mode buttons)
│   └── #mode-message
├── .card (Proxy Selection)
│   ├── Current Proxy Group display
│   ├── Actual Server Chain display
│   ├── Rules Info (Rule mode only)
│   ├── Test buttons (Test All, Test Group Latency)
│   ├── Progress bar & status
│   └── .proxy-tree (hierarchical proxy list)
│       ├── .proxy-tree-group (top-level groups)
│       │   ├── Header (toggle, icon, name, type, latency, test btn)
│       │   └── .proxy-tree-children (nested items)
│       └── .proxy-tree-standalone-section
└── .card (Active Connections)
    ├── h2 + refresh button
    └── #connections
```

---

## 7. Browser Compatibility

- Modern browsers supporting:
  - Fetch API
  - ES6+ (async/await, arrow functions, template literals)
  - CSS Flexbox & CSS Grid
  - CSS Custom Properties

---

## 8. Future Enhancements (Optional)

- [ ] WebSocket support for real-time updates
- [ ] Connection filtering/search
- [ ] Traffic statistics charts
- [x] Configurable API endpoint
- [x] Hierarchical proxy tree view
- [x] Rules info display
- [x] Group latency testing
- [ ] Dark/Light theme toggle
- [ ] Mobile app wrapper
- [ ] Authentication support
