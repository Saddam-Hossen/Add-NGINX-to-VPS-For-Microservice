# Add-NGINX-to-VPS-For-Microservice
Certainly! Below is a comprehensive guide for setting up Nginx for your project, including how to configure Nginx to work with your `snvn.conf` file and ensure everything is working properly.

### **Step 1: Install Nginx**

If Nginx is not installed on your server, you can install it using the following commands:

1. **Update your system:**
   ```bash
   sudo apt update
   ```

2. **Install Nginx:**
   ```bash
   sudo apt install nginx
   ```

3. **Start and enable Nginx:**
   ```bash
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```

4. **Check Nginx status:**
   ```bash
   sudo systemctl status nginx
   ```

   Ensure Nginx is running correctly.

---

### **Step 2: Create Nginx Configuration for Your Site**

You already have a configuration file named `snvn.conf`. You need to ensure that it’s properly linked and included in the Nginx setup.

#### **1. Create Configuration Files**
   In your case, the main configuration for the site is stored in `/www/wwwroot/snvn.deepseahost.com/snvn.conf`.

   The Nginx configuration for a site usually includes the server block that defines how requests should be handled. Your configuration file (`snvn.conf`) should look something like this:

```nginx
server {
    listen 3082;
    server_name 159.89.172.251;  # Use your actual server IP or domain  (server_name attendance.deepseahost.com;)

    # Proxy requests for /api/developer/**, /api/user/**, /api/role/**
    location /api/developer/ {
        proxy_pass http://localhost:3080;  # Forward to userservice (running on port 3080)
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/user/ {
        proxy_pass http://localhost:3080;  # Forward to userservice
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/role/ {
        proxy_pass http://localhost:3080;  # Forward to userservice
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Proxy requests for /api/attendance/**, /api/auth/**, etc.
    location /api/attendance/ {
        proxy_pass http://localhost:3081;  # Forward to attendance (running on port 3081)
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/auth/ {
        proxy_pass http://localhost:3081;  # Forward to attendance
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/globalSetting/ {
        proxy_pass http://localhost:3081;  # Forward to attendance
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/localSetting/ {
        proxy_pass http://localhost:3081;  # Forward to attendance
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/positionSetting/ {
        proxy_pass http://localhost:3081;  # Forward to attendance
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /api/userAtAGlance/ {
        proxy_pass http://localhost:3081;  # Forward to attendance
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### **2. Create Symbolic Link in `sites-enabled` Directory**

1. **Create a symbolic link in `/etc/nginx/sites-enabled/`:**
   ```bash
   sudo ln -s /www/wwwroot/snvn.deepseahost.com/snvn.conf /etc/nginx/sites-enabled/snvn.conf
   ```

   This will allow Nginx to include your site configuration during startup.

2. **Check if the link was created properly:**
   ```bash
   ls -l /etc/nginx/sites-enabled/
   ```

   You should see a symbolic link pointing to your `snvn.conf` file.

---

### **Step 3: Modify `nginx.conf` (if necessary)**

1. **Check if `nginx.conf` includes the `sites-enabled` directory.**

   Open your `nginx.conf` file:
   ```bash
   sudo nano /www/server/nginx/conf/nginx.conf
   ```

2. **Ensure it includes the following line (or add it if missing):**
   ```nginx
   include /etc/nginx/sites-enabled/*;
   ```

   This will make Nginx load configurations from the `sites-enabled` folder.

---

### **Step 4: Test the Nginx Configuration**

1. **Test the Nginx configuration:**
   ```bash
   sudo nginx -t
   ```

   You should see a message saying:
   ```bash
   nginx: the configuration file /www/server/nginx/conf/nginx.conf syntax is ok
   nginx: configuration file /www/server/nginx/conf/nginx.conf test is successful
   ```

2. **If there are errors, check the logs and fix them.**

---

### **Step 5: Restart Nginx**

1. **Restart Nginx to apply the changes:**
   ```bash
   sudo systemctl restart nginx
   ```

2. **Verify that Nginx is running:**
   ```bash
   sudo systemctl status nginx
   ```

---

### **Step 6: Verify Your Site**

- **Check your site by visiting your server’s IP address or domain in a browser.**
  For example, if your server’s IP address is `159.89.172.251`, go to `http://159.89.172.251` in a browser.
  
- **Check Nginx logs for any issues:**
  - **Error log**: `/www/wwwlogs/nginx_error.log`
  - **Access log**: `/www/wwwlogs/nginx-access.log`

  You can tail the logs to check for errors in real-time:
  ```bash
  sudo tail -f /www/wwwlogs/nginx_error.log
  ```

---

### **Step 7: Final Adjustments**

1. **If using HTTPS**, ensure SSL certificates are set up properly:
   - You'll need to modify your `snvn.conf` file to listen on port `443` and add SSL configurations.
   - Install SSL certificates from Let's Encrypt or any other certificate authority.

2. **Security Hardening**: 
   - Ensure your server is secure by using security headers, limiting HTTP methods, and configuring firewalls.

---

### **Conclusion**

By following this guide, you have:
- Set up Nginx to handle requests for your project.
- Configured reverse proxying to forward requests to the appropriate backend services (e.g., userservice and attendance).
- Tested and verified that everything is running correctly.

You can now manage and scale your project more efficiently using Nginx!

Let me know if you have any further questions or need additional assistance!
