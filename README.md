# Mihomo Dashboard

A lightweight, web-based dashboard for managing and monitoring the [Mihomo](https://github.com/MetaCubeX/mihomo) (Clash.Meta) proxy client.

![Dashboard Preview](https://img.shields.io/badge/Mihomo-Dashboard-00d4aa?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)

## Features

- 🔧 **Configurable API URL** - Connect to any mihomo instance (default: `http://127.0.0.1:9090`)
- 🎨 **Dark Theme** - Easy on the eyes with a modern dark UI
- 🌐 **Mode Control** - Switch between Rule / Global / Direct modes
- 🌳 **Hierarchical Proxy Tree** - View proxy groups, sub-groups, and servers in a tree structure
- 📊 **Rules Info** - See which proxy groups are actually used in Rule mode
- 🚀 **Latency Testing** - Test individual proxies, groups, or all at once
- 🔌 **Active Connections** - Monitor real-time connections
- 💾 **Persistent Settings** - API URL saved in browser localStorage

## Quick Start

1. **Start mihomo** with external-controller enabled:
   ```yaml
   external-controller: 127.0.0.1:9090
   ```

2. **Open the dashboard** - Simply open `mihomo-dashboard.html` in your browser:
   ```bash
   # On Linux/macOS
   open mihomo-dashboard.html
   
   # On Windows
   start mihomo-dashboard.html
   ```

3. **Configure API URL** (if needed) - Default is `http://127.0.0.1:9090`

## Screenshots

| Feature | Description |
|---------|-------------|
| Settings | Configure API endpoint URL |
| Mode Control | Switch between Rule / Global / Direct |
| Proxy Tree | Hierarchical view of groups and servers |
| Rules Info | See which groups are used in Rule mode |
| Latency Testing | Test proxies with progress bar |
| Connections | Real-time active connection monitoring |

## Usage

### Mode Control

| Mode | Description |
|------|-------------|
| **Rule** | Route traffic based on rules configuration (most common) |
| **Global** | All traffic through GLOBAL proxy selection |
| **Direct** | All traffic bypass proxy |

### Proxy Tree View

The dashboard displays proxies in a hierarchical tree structure:

- **📁 Top-level Groups** - Main proxy groups (Selector, URLTest, LoadBalance, Fallback)
- **📁 Sub-groups** - Nested proxy groups
- **🌐 Leaf Proxies** - Individual proxy servers

**Interactions:**
- Click **header** → Select the proxy/group
- Click **▶/▼** → Expand/collapse group
- Click **🧪 Test** → Test latency

**Group Display:**
- Type (Selector/URLTest/LoadBalance/Fallback)
- Item count
- Currently selected proxy (`now`)
- Latency of selected proxy

### Latency Testing

| Button | Description |
|--------|-------------|
| **🚀 Test All Latency** | Test all leaf proxies + all groups (their `now` nodes) |
| **📁 Test Group Latency** | Test only proxy groups (their `now` nodes) |
| **🧪 Test** (individual) | Test single proxy or group |

**Features:**
- Progress bar with percentage
- Status counter (good/medium/bad/error)
- Real-time latency badge updates
- Color-coded results

**Latency Colors:**
- 🟢 Green (< 300ms) - Good
- 🟡 Yellow (300-800ms) - Medium
- 🔴 Red (> 800ms) - Bad

### Rules Info (Rule Mode)

When in **Rule** mode, the dashboard shows which proxy groups are actually used by your rules configuration. This helps you understand:

- Which groups matter in Rule mode
- Whether GLOBAL selection has any effect
- Which groups you should focus on testing

## API Configuration

The dashboard connects to mihomo's REST API. Configure the API URL in Settings:

```
Default: http://127.0.0.1:9090
Custom:  http://192.168.1.100:9090
```

The URL is saved to browser localStorage and persists across sessions.

## Requirements

- **Mihomo** (Clash.Meta) running with `external-controller` enabled
- Modern web browser with JavaScript enabled
- No server required - runs entirely client-side

## Mihomo Configuration

Ensure your mihomo `config.yaml` has:

```yaml
# API endpoint (required)
external-controller: 127.0.0.1:9090

# Optional: Allow LAN access
allow-lan: true
external-controller: 0.0.0.0:9090

# Optional: Set secret for authentication
secret: "your-secret"
```

## API Endpoints Used

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/configs` | GET | Get current configuration |
| `/configs` | PATCH | Update mode |
| `/proxies` | GET | List all proxy groups |
| `/proxies/{name}` | GET | Get proxy group details |
| `/proxies/{name}` | PUT | Select proxy in GLOBAL |
| `/proxies/{name}/delay` | GET | Test proxy latency |
| `/connections` | GET | List active connections |
| `/rules` | GET | List routing rules |

## Browser Compatibility

- Chrome / Edge (latest)
- Firefox (latest)
- Safari (latest)

Requires:
- Fetch API
- ES6+ (async/await, arrow functions)
- CSS Grid & Flexbox
- localStorage

## Development

The dashboard is a single HTML file with embedded CSS and JavaScript. No build process required.

```
mihomo-dashboard.html  # Main dashboard file
spec.md                # Technical specification
README.md              # This file
```

## Architecture Highlights

### Hierarchical Proxy Tree
- Multi-column grid layout for efficient space usage
- Recursive rendering of nested proxy groups
- All groups collapsed by default
- Visual distinction between group types

### Latency Testing
- Groups tested via their `now` node (selected proxy)
- Recursive collection of leaf proxies from nested groups
- Duplicate detection (same proxy in multiple groups)
- Progress tracking with visual feedback

### Rules Analysis
- Fetches rules from `/rules` endpoint
- Extracts unique proxy groups used in rules
- Displays only in Rule mode
- Helps users understand routing behavior

## Troubleshooting

### "Error: Connection failed"
- Check if mihomo is running
- Verify `external-controller` is configured correctly
- Check the API URL in Settings

### "JSON.parse: unexpected end of data"
- Fixed in latest version - the dashboard now handles empty responses properly

### CORS errors
- The dashboard must be opened as a file or served from the same origin
- Use a local HTTP server if needed: `python3 -m http.server 8080`

### Proxy groups not showing
- Ensure mihomo is fully loaded with your config
- Check browser console for API errors
- Verify the API URL is correct

## License

MIT License - feel free to use and modify.

## Related Projects

- [Mihomo](https://github.com/MetaCubeX/mihomo) - The proxy kernel
- [MetaCubeX](https://github.com/MetaCubeX) - Official mihomo organization
- [Clash](https://github.com/Dreamacro/clash) - Original Clash proxy

## Contributing

Contributions welcome! This is a simple single-file dashboard, keep it lightweight.
