# WebVM Setup Guide

A browser-based Linux environment using CheerpX technology.

## Prerequisites
- Node.js (https://nodejs.org)

## Quick Start

1. Create project directory and files:
```bash
mkdir webvm-project
cd webvm-project
```

2. Create `index.html`:
```html
<!DOCTYPE html>
<html lang="en" style="height: 100%; margin: 0;">
  <head>
    <meta charset="utf-8" />
    <title>WebVM</title>
    <script src="https://cxrtnc.leaningtech.com/1.0.6/cx.js"></script>
    <script type="module" src="main.js"></script>
  </head>
  <body style="height: 100%; margin: 0; background: black; overflow: hidden;">
    <pre id="console" style="height: 100%; margin: 0; color: white; font-family: monospace;"></pre>
  </body>
</html>
```

3. Create `main.js`:
```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
    res.setHeader('Cross-Origin-Embedder-Policy', 'require-corp');
    res.setHeader('Cross-Origin-Opener-Policy', 'same-origin');
    next();
});

app.use(express.static('.'));
app.listen(8080);
```

4. Install dependencies:
```bash
npm init -y
npm install express
```

5. Start server:
```bash
node main.js
```

6. Open http://localhost:8080 in your browser

## Features
- Full Linux environment in browser
- Persistent storage using IndexedDB
- Access to bash shell and common Linux tools

## Troubleshooting

If you see `SharedArrayBuffer` errors:
- Ensure the COOP/COEP headers are set correctly
- Check that your server is running and accessible
- Make sure you're using a modern browser that supports SharedArrayBuffer

## License
This project uses CheerpX from Leaning Technologies which has its own licensing terms.
