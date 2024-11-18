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
    <script type="module">
      // The read-only disk image from Leaning Technologies' fast cloud backend
      const cloudDevice = await CheerpX.CloudDevice.create(
        "wss://disks.webvm.io/debian_large_20230522_5044875331.ext2"
      );
      // Read-write local storage for disk blocks
      const idbDevice = await CheerpX.IDBDevice.create("block1");
      // Device to overlay local changes with remote read-only image
      const overlayDevice = await CheerpX.OverlayDevice.create(
        cloudDevice,
        idbDevice
      );
      // Direct access to files in HTTP server
      const webDevice = await CheerpX.WebDevice.create("");
      // Access to JavaScript binary data and strings
      const dataDevice = await CheerpX.DataDevice.create();
      const cx = await CheerpX.Linux.create({
        mounts: [
          { type: "ext2", path: "/", dev: overlayDevice },
          { type: "dir", path: "/app", dev: webDevice },
          { type: "dir", path: "/data", dev: dataDevice },
          { type: "devs", path: "/dev" },
        ],
      });
      // Setup console
      cx.setConsole(document.getElementById("console"));
      // Run bash shell
      await cx.run("/bin/bash", ["--login"], {
        env: [
          "HOME=/home/user",
          "USER=user",
          "SHELL=/bin/bash",
          "EDITOR=vim",
          "LANG=en_US.UTF-8",
          "LC_ALL=C",
        ],
        cwd: "/home/user",
        uid: 1000,
        gid: 1000,
      });
    </script>
  </head>
  <body style="height: 100%; margin: 0; background: black; overflow: hidden;">
    <pre id="console" style="height: 100%; margin: 0; color: white; font-family: monospace;"></pre>
  </body>
</html>
```

3. Create `server.js`:
```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
    res.setHeader('Cross-Origin-Embedder-Policy', 'require-corp');
    res.setHeader('Cross-Origin-Opener-Policy', 'same-origin');
    next();
});

app.use(express.static('.'));
app.listen(8080, () => {
    console.log('Server running at http://localhost:8080');
});
```

4. Install dependencies:
```bash
npm init -y
npm install express
```

5. Start server:
```bash
node server.js
```

6. Open http://localhost:8080 in your browser

## Features
- Full Linux environment in browser
- Persistent storage using IndexedDB
- Access to bash shell and common Linux tools
- Debian-based Linux distribution
- File system persistence between sessions

## Troubleshooting

If you see `SharedArrayBuffer` errors:
- Ensure the COOP/COEP headers are set correctly in server.js
- Check that your server is running and accessible at http://localhost:8080
- Make sure you're using a modern browser that supports SharedArrayBuffer
- Check browser console for detailed error messages

## Notes
- The system uses a read-only Debian disk image with a writable overlay
- Changes are persisted locally using IndexedDB
- File system changes are preserved between browser sessions

## License
This project uses CheerpX from Leaning Technologies which has its own licensing terms.
