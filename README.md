# Mihomo Dashboard

A lightweight, web-based dashboard for managing and monitoring the [Mihomo](https://github.com/MetaCubeX/mihomo) (Clash.Meta) proxy client.

![Dashboard Preview](https://img.shields.io/badge/Mihomo-Dashboard-00d4aa?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-blue?style=flat-square)

## Features

- 🔧 **Configurable API URL** - Connect to any mihomo instance (default: `http://127.0.0.1:9090`)
- 🎨 **Dark Theme** - Easy on the eyes with a modern dark UI
- 🌐 **Mode Control** - Switch between Rule / Global / Direct modes
- 🚀 **Proxy Selection** - Quick switch between common proxies
- 📊 **Proxy Groups & Latency Testing** - View all servers with latency tests
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
| Status | Current mode, proxy, and auto-select status |
| Mode Control | Switch between Rule / Global / Direct |
| Proxy Selection | Quick switch GLOBAL proxy group |
| Proxy Groups | Expandable section with all servers & latency |
| Connections | Real-time active connection monitoring |

## Usage

### Mode Control

| Mode | Description |
|------|-------------|
| **Rule** | Route traffic based on rules (most common) |
| **Global** | All traffic through proxy |
| **Direct** | All traffic bypass proxy |

### Proxy Selection

Quick buttons for common proxies:
- DIRECT - Bypass proxy
- 翻墙机场 GFWAirPort - Airport proxy group
- Singapore / Tokyo / HongKong - Direct server selection

### Latency Testing

- **🚀 Test All Latency** - Test all proxies in all groups
- **🧪 Test** (group) - Test all proxies in a specific group
- **Test** (server) - Test a single server

Latency colors:
- 🟢 Green (< 300ms) - Good
- 🟡 Yellow (300-800ms) - Medium
- 🔴 Red (> 800ms) - Bad

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
| `/proxies/{name}` | PUT | Select proxy in group |
| `/proxies/{name}/delay` | GET | Test proxy latency |
| `/connections` | GET | List active connections |

## Browser Compatibility

- Chrome / Edge (latest)
- Firefox (latest)
- Safari (latest)

Requires:
- Fetch API
- ES6+ (async/await, arrow functions)
- localStorage

## Development

The dashboard is a single HTML file with embedded CSS and JavaScript. No build process required.

```
mihomo-dashboard.html  # Main dashboard file
spec.md                # Technical specification
README.md              # This file
```

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

## License

MIT License - feel free to use and modify.

## Related Projects

- [Mihomo](https://github.com/MetaCubeX/mihomo) - The proxy kernel
- [MetaCubeX](https://github.com/MetaCubeX) - Official mihomo organization
- [Clash](https://github.com/Dreamacro/clash) - Original Clash proxy

## Contributing

Contributions welcome! This is a simple single-file dashboard, keep it lightweight.
