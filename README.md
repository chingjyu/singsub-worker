<div align="center">
  <h1><b>singsub Worker</b></h1>
  
  <!-- <p>
    <a href="https://sublink-worker.sageer.me">https://sublink-worker.sageer.me</a>
  </p> -->
  <br>

  <p>
    <a href="https://dash.cloudflare.com/?to=/:account/workers-and-pages/create">
      <img src="https://deploy.workers.cloudflare.com/button" alt="Deploy to Cloudflare Workers"/>
    </a>
  </p>
  
  <p><a href="/docs/README_CN.md">中文文档</a></p>
</div>

## 🚀 Quick Start

### Quick Deployment
- Fork this project, click the `Deploy to Cloudflare` button above
- Select your repository in the `Import Repository` section (you need to link your GitHub account)
- Change the `Deploy Command` as follows, then select `Save and Deploy`
``` bash
npm run deploy
```

## ✨ Features

### Supported Protocols
- ShadowSocks
- VMess
- VLESS
- Hysteria2
- Trojan
- TUIC

### Core Features
- Support for importing Base64 http/https subscription links and various protocol sharing URLs
- Pure JavaScript + Cloudflare Worker implementation, one-click deployment, ready to use
- Support for fixed/random short link generation (based on KV)
- Light/Dark theme toggle
- Flexible API, supporting script operations
- Support for Chinese, English, and Persian languages

### Client Support
- Sing-Box
- Xray/V2Ray

### Web Interface Features
- User-friendly operation interface
- Various predefined rule sets
- Customizable policy groups for geo-site, geo-ip, ip-cidr, and domain-suffix

## 📖 API Documentation

For detailed API documentation, please refer to [APIDoc.md](/docs/APIDoc.md)

### Main Endpoints
- `/singbox` - Generate Sing-Box configuration
- `/xray` - Generate Xray configuration
- `/shorten` - Generate short links

## 📝 Recent Updates

### 2025-11-08

- feat(config): Added support for proxy configuration grouped by country, updated related translations

## 🔧 Project Structure

```
.
├── index.js                 # Main server logic, handles request routing
├── BaseConfigBuilder.js     # Build base configuration
├── SingboxConfigBuilder.js  # Build Sing-Box configuration
├── ClashConfigBuilder.js    # Build Clash configuration
├── ProxyParsers.js          # Parse URLs of various proxy protocols
├── utils.js                 # Provide various utility functions
├── htmlBuilder.js           # Generate Web interface
├── style.js                 # Generate CSS for Web interface
├── config.js                # Store configuration information
└── docs/
    ├── APIDoc.md            # API documentation
    ├── UpdateLogs.md        # Update logs
    ├── FAQ.md               # Frequently asked questions
    └── BaseConfig.md        # Basic configuration feature introduction
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ⚠️ Disclaimer

This project is for learning and exchange purposes only. Please do not use it for illegal purposes. All consequences resulting from the use of this project are solely the responsibility of the user and are not related to the developer.