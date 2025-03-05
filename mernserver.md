# Server Deployment & Maintenance Cheat Sheet

This cheat sheet provides common commands and steps for managing your server applications, NGINX, and MongoDB. Adjust commands as needed for your environment.

---

## General Server & Git Deployment

- **List Running Processes (filter by Node or custom app):**
  ```bash
  ps aux | grep node
  ```
- **Clone or Pull Your Git Repository:**
  ```bash
  # Clone (if not already done)
  git clone https://github.com/yourusername/your-repo.git /var/www/yourapp

  # Change to the app directory and update:
  cd /var/www/yourapp
  git pull origin main
  ```

- **Restart Your Application:**
  - **If using PM2:**
    ```bash
    pm2 list
    pm2 restart <app-name>
    pm2 logs <app-name>
    ```
  - **If using systemd:**
    ```bash
    sudo systemctl restart yourapp.service
    sudo systemctl status yourapp.service
    sudo journalctl -u yourapp.service -f
    ```

---

## Environment Variables & Configuration

- **List & Set Environment Variables (for Node.js apps):**
  - Use a `.env` file in your application root (or your preferred config location).  
  - Example `.env` file:
    ```dotenv
    PORT=4001
    MONGODB_URI=mongodb://username:password@127.0.0.1:27017/yourdb?authSource=admin
    API_KEY=your_api_key_here
    ```
- **Find All `.env` Files in a Directory:**
  ```bash
  find /var/www/yourapp -type f -name ".env*"
  ```

---

## NGINX Management

- **Show NGINX Configuration for Your App:**
  ```bash
  sudo nano /etc/nginx/sites-available/yourapp.conf
  ```
- **Test NGINX Configuration:**
  ```bash
  sudo nginx -t
  ```
- **Reload / Restart NGINX:**
  ```bash
  sudo systemctl reload nginx
  sudo systemctl restart nginx
  ```
- **View NGINX Logs:**
  ```bash
  sudo tail -n 50 /var/log/nginx/yourapp-error.log
  sudo tail -n 50 /var/log/nginx/access.log
  ```

---

## MongoDB Management

- **Check MongoDB Service Status:**
  ```bash
  sudo systemctl status mongod
  ```
- **Restart MongoDB:**
  ```bash
  sudo systemctl restart mongod
  ```
- **View MongoDB Logs:**
  ```bash
  sudo tail -n 50 /var/log/mongodb/mongod.log
  ```
- **Connect Using Mongosh (from server):**
  ```bash
  mongosh -u <username> -p <password> --authenticationDatabase admin
  ```
- **Secure MongoDB:**
  - Enable authentication (set in `/etc/mongod.conf` under `security`):
    ```yaml
    security:
      authorization: "enabled"
    ```
  - Create an admin user (if not already done) and then restart mongod.

---

## Health Checks & Debugging

- **Add a Health Check Endpoint (in your Node.js server code):**
  ```javascript
  app.get("/health", async (req, res) => {
    try {
      await mongoose.connection.db.admin().ping();
      res.status(200).send("Healthy");
    } catch (err) {
      res.status(500).send("Database not reachable");
    }
  });
  ```
- **Test Health Check:**
  ```bash
  curl http://yourdomain/health
  ```

---

## Networking & Ports

- **Check Which Ports Are Being Used:**
  ```bash
  sudo netstat -tulpn | grep node
  sudo ss -tulpn | grep :<port>
  ```
- **Find Application Listening Ports (via logs or environment variables):**
  - Your Node.js app will log the port it is using (e.g., "Server ready at http://0.0.0.0:4001/graphql").
  - Check your `.env` file for `PORT` variables.

---

## Docker (if applicable)

- **List Running Containers:**
  ```bash
  docker ps
  ```
- **Inspect a Container:**
  ```bash
  docker inspect $(docker ps -q --filter "name=yourapp.web")
  ```

---

## Backups & Rollbacks

- **Backup MongoDB Database:**
  ```bash
  mongodump --uri="mongodb://username:password@127.0.0.1:27017/yourdb?authSource=admin" --out=/path/to/backup
  ```
- **Restore MongoDB Database:**
  ```bash
  mongorestore --uri="mongodb://username:password@127.0.0.1:27017/yourdb?authSource=admin" /path/to/backup/yourdb
  ```

---

## Security & Maintenance

- **Disable External SSH Logins for Root:**  
  Edit `/etc/ssh/sshd_config`:
  ```bash
  PermitRootLogin no
  ```
  Then reload SSH:
  ```bash
  sudo systemctl reload sshd
  ```
- **Run Applications as Non-Root Users:**  
  Make sure your services and cron jobs run under dedicated service or application users.
- **Ensure File and Directory Permissions:**  
  Verify that configuration files (like `.env`) and application directories have proper permissions to prevent unauthorized access.

---

> **Note:**  
> When making changes to environment variables, NGINX configurations, or service files, remember to reload or restart the affected services. Always test configuration changes (using commands like `nginx -t`) before reloading/restarting to avoid downtime.

---

Use this cheat sheet as a starting point and adjust paths, service names, and configuration settings as necessary for your server environment.

Happy deploying and maintaining!
