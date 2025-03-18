```md
# MERN Deployment Cheat Sheet

Below is a concise cheat sheet of common commands used when deploying and managing a MERN (MongoDB, Express, React, Node) app with **PM2** and **Nginx** on a Linux server.

---

## 1. Git & Node Setup

**Pull latest code and install dependencies**  
```bash
git pull
npm install
```

**Build frontend** (if you keep React in the same repo)  
```bash
npm run build
```
*(Make sure your frontend’s build script is set up in `package.json`.)*

---

## 2. PM2 (Process Manager)

### Starting / Stopping Processes

- **Start the Node app** (if `index.js` is your entry point):
  ```bash
  pm2 start index.js --name "myapp"
  ```
- **Stop the Node app**:
  ```bash
  pm2 stop myapp
  ```
- **Restart the Node app**:
  ```bash
  pm2 restart myapp
  ```

### Monitoring & Logs

- **View running processes**:
  ```bash
  pm2 list
  ```
- **View real-time logs** (press Ctrl+C to quit):
  ```bash
  pm2 logs myapp
  ```
- **Detailed process monitor** (CPU, memory, etc.):
  ```bash
  pm2 monit
  ```

### Other Commands

- **Delete a process** from PM2’s list:
  ```bash
  pm2 delete myapp
  ```
- **Save PM2 process list** so it restarts on reboot:
  ```bash
  pm2 save
  ```
- **Startup script** (for auto-start on reboot):
  ```bash
  pm2 startup
  ```
  (Follow the instructions PM2 prints for your OS.)

---

## 3. Nginx

### Basic Commands

- **Check config syntax**:
  ```bash
  sudo nginx -t
  ```
- **Reload Nginx** (use this after editing config files):
  ```bash
  sudo systemctl reload nginx
  ```
- **Restart Nginx** (completely stops & starts):
  ```bash
  sudo systemctl restart nginx
  ```
- **View Nginx status**:
  ```bash
  sudo systemctl status nginx
  ```

### Common Config Patterns

- **Proxy pass** to Node (e.g., `http://127.0.0.1:4000`) for backend routes:
  ```nginx
  location /api/ {
      proxy_pass http://127.0.0.1:4000;
      # plus other headers...
  }
  ```
- **Serve React app** on port 3000:
  ```nginx
  location / {
      proxy_pass http://127.0.0.1:3000;
  }
  ```
- **Check for SSL** (Certbot-managed):
  ```nginx
  server {
      listen 443 ssl;
      ssl_certificate /etc/letsencrypt/live/yourdomain/fullchain.pem;
      ssl_certificate_key /etc/letsencrypt/live/yourdomain/privkey.pem;
      ...
  }
  ```

---

## 4. Typical Deployment Flow

1. **SSH** into your server.  
2. **Pull latest changes**:
   ```bash
   git pull
   ```
3. **Install dependencies**:
   ```bash
   npm install
   ```
4. **Build React** (if using create-react-app in the same repo):
   ```bash
   npm run build
   ```
5. **Restart Node app** via PM2:
   ```bash
   pm2 restart myapp
   ```
6. **Check logs** to confirm no errors:
   ```bash
   pm2 logs myapp
   ```
7. **Validate Nginx** config if changed:
   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```
8. **Verify site** in your browser.

---

That’s it! These commands cover the most common tasks in a typical MERN deployment workflow with PM2 and Nginx.
```
