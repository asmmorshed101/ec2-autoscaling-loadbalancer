# ec2-autoscaling-loadbalancer

### This project on Load balancer auto scaling 

```bash
sudo apt update -y
sudo apt install -y nodejs npm
```
```bash
node -v
npm -v
```
```bash
sudo mkdir -p /var/www/myapp
sudo chown ubuntu:ubuntu /var/www/myapp
cd /var/www/myapp
```

```bash
npm init -y
npm install express
```
```bash
vim index.js
vim index.html
```
```bash
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Node.js EC2 App</title>
<style>
  body {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background: linear-gradient(135deg, #74ebd5 0%, #ACB6E5 100%);
    font-family: Arial, sans-serif;
    color: #333;
    text-align: center;
  }
  h1 { font-size: 2.5rem; color: #222; margin-bottom: 1rem; }
  p { font-size: 1.2rem; margin: 0.5rem 0; }
  button {
    padding: 10px 20px;
    font-size: 1.1rem;
    margin: 10px;
    cursor: pointer;
    border: none;
    background-color: #333;
    color: white;
    border-radius: 5px;
  }
  #progress {
    width: 80%;
    height: 25px;
    margin-top: 20px;
    background-color: #eee;
    border-radius: 5px;
    overflow: hidden;
  }
  #progress-bar {
    height: 100%;
    width: 0%;
    background-color: #4caf50;
    text-align: center;
    color: white;
    line-height: 25px;
    transition: width 0.2s;
  }
</style>
</head>
<body>

<h1>🚀 Node.js EC2 App</h1>
<p>Server Private IP: <b id="ip">Loading...</b></p>

<button id="startBtn">Start CPU Load</button>
<button id="stopBtn">Stop CPU Load</button>

<div id="progress">
  <div id="progress-bar">0%</div>
</div>

<script>
async function fetchIP() {
  const res = await fetch('/ip-json');
  const data = await res.json();
  document.getElementById('ip').textContent = data.ip;
}

let interval = null;

// Start CPU load
document.getElementById('startBtn').addEventListener('click', async () => {
  if (interval) return;
  interval = setInterval(async () => {
    const res = await fetch('/start-load');
    const data = await res.json();
    console.log(data.message);

    // Fake progress bar
    const bar = document.getElementById('progress-bar');
    let width = parseInt(bar.style.width) || 0;
    if (width >= 100) width = 0;
    width += 10;
    bar.style.width = width + '%';
    bar.textContent = width + '%';
  }, 500);
});

// Stop CPU load
document.getElementById('stopBtn').addEventListener('click', async () => {
  clearInterval(interval);
  interval = null;
  await fetch('/stop-load');
  const bar = document.getElementById('progress-bar');
  bar.style.width = '0%';
  bar.textContent = '0%';
  console.log("CPU load stopped");
});

// Load IP on page load
fetchIP();
</script>

</body>
</html>
```

```bash
const express = require("express");
const os = require("os");
const path = require("path");

const app = express();
const PORT = 3000;

let loadInterval = null;

// Serve index.html
app.get("/", (req, res) => {
  res.sendFile(path.join(__dirname, "index.html"));
});

// Get private IP JSON
app.get("/ip-json", (req, res) => {
  res.json({ ip: getPrivateIP() });
});

let loadRunning = false;
// Start CPU load
app.get("/start-load", (req, res) => {
  if (loadRunning) return res.json({ message: "Load already running" });
  loadRunning = true;

  function burnCPU() {
    if (!loadRunning) return;
    for (let i = 0; i < 1e7; i++) Math.sqrt(i);
    setImmediate(burnCPU); // keep CPU busy continuously
  }

  burnCPU();
  res.json({ message: "CPU load started" });
});

// Stop CPU load
app.get("/stop-load", (req, res) => {
  loadRunning = false;
  res.json({ message: "CPU load stopped" });
});

// Function to get private IP
function getPrivateIP() {
  const ifaces = os.networkInterfaces();
  for (let dev in ifaces) {
    for (let net of ifaces[dev]) {
      if (net.family === "IPv4" && !net.internal) return net.address;
    }
  }
  return "IP not found";
}

// Start server
app.listen(PORT, "0.0.0.0", () => {
  console.log(`Server running at http://0.0.0.0:${PORT}`);
});
```
Paste the code

```bash
sudo npm install -g pm2
```
```bash
pm2 start index.js
```
```bash
sudo npm install -g pm2
```
```bash
pm2 start index.js
```


```bash
pm2 save
pm2 startup systemd
```

It will print a command — copy-paste and run it



