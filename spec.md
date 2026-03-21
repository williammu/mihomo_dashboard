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

### 2.2 Layout
- **Container**: Max-width 800px, centered
- **Padding**: 20px body, 20px card internal
- **Cards**: Rounded (12px), shadow (0 4px 6px rgba(0,0,0,0.3))
- **Responsive**: Flexbox with wrap for controls

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
- Shows current API URL in status display

### 3.2 Status Display
**Endpoint**: `GET /configs`, `GET /proxies/GLOBAL`, `GET /proxies/自动选择`

Displays:
- Current API endpoint URL being used
- Current mode (rule / global / direct)
- Current GLOBAL proxy selection
- Auto-select node (when applicable)

### 3.3 Mode Control
**Endpoint**: `PATCH /configs`

Available modes:
| Mode | Button | Emoji |
|------|--------|-------|
| rule | Rule | 📋 |
| global | Global | 🌍 |
| direct | Direct | ➡️ |

### 3.4 Proxy Selection
**Endpoint**: `PUT /proxies/GLOBAL`

Available proxies:
| Proxy Name | Button ID | Emoji |
|------------|-----------|-------|
| DIRECT | proxy-direct | ➡️ |
| 翻墙机场 GFWAirPort | proxy-gfw | ✈️ |
| Singapore V2 - B Group | proxy-sg | 🇸🇬 |
| Tokyo V2 - B Group | proxy-jp | 🇯🇵 |
| HongKong V2 - B Group | proxy-hk | 🇭🇰 |

### 3.5 Active Connections
**Endpoint**: `GET /connections`

- Displays up to 20 most recent connections
- Shows: Host + Proxy chain
- Auto-scroll container (max-height: 300px)

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
Response: { now: string, type: string, all: string[] }
```

### 4.3 Update Mode
```
PATCH /configs
Body: { mode: "rule" | "global" | "direct" }
```

### 4.4 Update Proxy
```
PUT /proxies/GLOBAL
Body: { name: string }
```

### 4.5 Get Connections
```
GET /connections
Response: { connections: Array<{ metadata: {...}, chains: string[] }> }
```

---

## 5. Behavior

### 5.1 Auto-Refresh
- Interval: 5 seconds
- Triggered by: `setInterval(refreshAll, 5000)`
- Initial load: On page load

### 5.2 Manual Refresh
- Button: "🔄 Refresh" in status card
- Calls `refreshAll()` function

### 5.3 Message Display
- Success: Green background (`#00d4aa33`), green border
- Error: Red background (`#e9456033`), red border
- Duration: 3 seconds (auto-hide)

### 5.4 Button States
- Default: Blue background (`#0f3460`), accent border
- Hover: Accent background, dark text
- Active: Accent background, dark text (selected state)
- Disabled: 50% opacity, not-allowed cursor

### 5.5 Error Handling
- Network errors display in status area
- Error message includes: "Make sure mihomo is running on 127.0.0.1:9090"

---

## 6. Component Hierarchy

```
body
├── h1 (title)
├── .card (Status)
│   ├── h2 + refresh button
│   ├── #status-content
│   └── #status-message
├── .card (Mode Control)
│   ├── h2
│   ├── description
│   ├── .controls (3 mode buttons)
│   └── #mode-message
├── .card (Proxy Selection)
│   ├── h2
│   ├── description
│   ├── .controls (5 proxy buttons)
│   └── #proxy-message
└── .card (Active Connections)
    ├── h2
    └── #connections (.connections)
```

---

## 7. Browser Compatibility

- Modern browsers supporting:
  - Fetch API
  - ES6+ (async/await, arrow functions, template literals)
  - CSS Flexbox
  - CSS Custom Properties (not used, but compatible)

---

## 8. Future Enhancements (Optional)

- [ ] WebSocket support for real-time updates
- [ ] Connection filtering/search
- [ ] Traffic statistics charts
- [x] Configurable API endpoint
- [ ] Dark/Light theme toggle
- [ ] Mobile app wrapper
- [ ] Authentication support
