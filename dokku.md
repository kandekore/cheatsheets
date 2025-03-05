
---

# Dokku Cheat Sheet

### General Commands
- List all apps:  
  `dokku apps:list`
- Create a new app:  
  `dokku apps:create <app-name>`
- Delete an app:  
  `dokku apps:destroy <app-name> --force`
- Show Dokku version:  
  `dokku version`

---

### Deployment
- Push code to deploy:  
  `git push dokku <branch>:main`
- Rebuild an app:  
  `dokku ps:rebuild <app-name>`
- Restart an app:  
  `dokku ps:restart <app-name>`

---

### Logs and Debugging
- View app logs:  
  `dokku logs <app-name>`
- Stream logs in real-time:  
  `dokku logs <app-name> --tail`
- Check NGINX error logs:  
  `sudo tail -n 50 /var/log/nginx/<app-name>-error.log`

---

### Networking
- Check container IP:  
  `docker inspect $(docker ps -q --filter "name=<app-name>.web.1") | grep IPAddress`
- Curl app internally:  
  `curl http://<container-ip>:<port>`

---

### NGINX
- Show NGINX configuration for an app:  
  `dokku nginx:show-config <app-name>`
- Reload NGINX:  
  `sudo systemctl reload nginx`
- Restart NGINX:  
  `sudo systemctl restart nginx`

---

### Configuration
- Set environment variables:  
  `dokku config:set <app-name> KEY=VALUE`
- View environment variables:  
  `dokku config <app-name>`
- Unset an environment variable:  
  `dokku config:unset <app-name> KEY`

---

### Database
- Connect to MongoDB Atlas:  
  `dokku config:set <app-name> MONGODB_URI="mongodb+srv://<username>:<password>@<cluster>.mongodb.net/<database>?retryWrites=true&w=majority"`
- Check database connectivity from inside the container:  
  ```bash
  docker exec -it $(docker ps -q --filter "name=<app-name>.web.1") bash
  mongo --host <db-host> --port <db-port>
  ```

---

### DNS and Domains
- Set a custom domain:  
  `dokku domains:add <app-name> <domain>`
- List app domains:  
  `dokku domains <app-name>`
- Remove a custom domain:  
  `dokku domains:remove <app-name> <domain>`

---

### System Management
- Update Dokku:  
  ```bash
  wget https://dokku.com/install/v0.30.2/bootstrap.sh
  sudo DOKKU_TAG=v0.30.2 bash bootstrap.sh
  ```
- Check server status:  
  `sudo systemctl status nginx`
- Remove default NGINX site:  
  `sudo rm /etc/nginx/sites-enabled/default`  
  `sudo systemctl reload nginx`

---

### Healthcheck
- Add a healthcheck endpoint in server.js:
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
- Test the healthcheck:  
  `curl http://<domain>/health`

---

### Docker Commands
- List running containers:  
  `docker ps`
- Inspect a container:  
  `docker inspect $(docker ps -q --filter "name=<app-name>.web.1")`
- Fix Docker permissions:  
  `sudo usermod -aG docker $USER`  
  `newgrp docker`

---
