# Load-Balancer (NGINX)

Load Balancer is a server that distributes traffic to different servers. It can also act as a reverse proxy to redirect different APIs to different servers.

Implemented for NGINX server to act as a load balancer.# Load-Balancer (NGINX)

## Installation & Setup
### **macOS**

```bash
# Install NGINX using Homebrew
brew install nginx

# Start NGINX service
brew services start nginx

# Verify NGINX is running
brew services list
```

### Configuration:

- NGINX config location: /usr/local/etc/nginx/nginx.conf
- Default logs: /usr/local/var/log/nginx/
- Copy the provided nginx.conf to NGINX config directory

```bash
cp nginx.conf /usr/local/etc/nginx/
brew services restart nginx
```
### **Linux/Ubuntu**

```bash
# Install NGINX
sudo apt update
sudo apt install nginx

# Start and enable NGINX
sudo systemctl start nginx
sudo systemctl enable nginx

# Verify NGINX is running
sudo systemctl status nginx
```

### Configuration
- NGINX config location: /etc/nginx/nginx.conf
- Default logs: /var/log/nginx/
- Copy the provided nginx.conf:
```bash
sudo cp nginx.conf /etc/nginx/
sudo nginx -t  # Test configuration
sudo systemctl restart nginx
```

### **Windows**
```bash
# Option 1: Using Chocolatey
choco install nginx

# Option 2: Download from nginx.org
# 1. Download nginx-1.x.x.zip from http://nginx.org/en/download.html
# 2. Extract to C:\nginx

# Start NGINX
cd C:\nginx
nginx.exe

# For services (Admin PowerShell):
nssm install nginx C:\nginx\nginx.exe
net start nginx
```

### configuration
- NGINX config location: C:\nginx\conf\nginx.conf
- Update log paths in nginx.conf:
```bash
access_log C:/nginx/logs/access.log;
error_log C:/nginx/logs/error.log;
ssl_certificate C:/nginx/ssl/server.crt;
ssl_certificate_key C:/nginx/ssl/server.key;
```
- Reload configuration:
```bash
cd C:\nginx
nginx -s reload
```

### For SSL on Windows:

Use OpenSSL for Windows or generate certificates elsewhere
Place .crt and .key files in C:\nginx\ssl\


## Running the Load Balancer
### Step 1: Start Backend API Servers
Start multiple instances on different ports (Terminal/PowerShell):
```bash
# Terminal 1
python api.py  # Runs on port 8081 (default)

# Terminal 2
python api.py --port 5000

# Terminal 3
python api.py --port 5001

# Terminal 4
python api.py --port 5002
```

### Step 2: Verify nginx.conf is Updated
Edit the paths in nginx.conf for your OS:
macOS example:
```bash
access_log /usr/local/var/log/nginx/access.log;
error_log /usr/local/var/log/nginx/error.log;
ssl_certificate /usr/local/etc/nginx/ssl/server.crt;
ssl_certificate_key /usr/local/etc/nginx/ssl/server.key;
```
Linux example:
```bash
access_log /var/log/nginx/access.log;
error_log /var/log/nginx/error.log;
ssl_certificate /etc/nginx/ssl/server.crt;
ssl_certificate_key /etc/nginx/ssl/server.key;
```
Windows example:
```bash
access_log C:/nginx/logs/access.log;
error_log C:/nginx/logs/error.log;
ssl_certificate C:/nginx/ssl/server.crt;
ssl_certificate_key C:/nginx/ssl/server.key;
```

### Step 3: Reload NGINX
```bash
# macOS
brew services restart nginx

# Linux
sudo systemctl restart nginx

# Windows
cd C:\nginx
nginx -s reload
```

## Testing the Load Balancer
```bash
# Test health endpoint
curl http://localhost/health

# Test API (HTTP - will redirect to HTTPS)
curl http://localhost/hello

# Test with HTTPS (if SSL configured)
curl --insecure https://your-domain.com/hello

# Test POST endpoint
curl -X POST http://localhost/auth/ \
  -H "Content-Type: application/json" \
  -d '{"user":"test"}'
```

## SSL Configuration
macOS with Certbot
```bash
brew install certbot
brew install certbot-nginx
sudo certbot --nginx -d your-domain.com
```
Linux with Certbot
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com
```
Windows - Generate Self-Signed Certificate
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout C:\nginx\ssl\server.key \
  -out C:\nginx\ssl\server.crt
```

## Troubleshooting
Issue	Solution
Port 80/443 already in use	Change listen port in nginx.conf or kill existing process
Permission denied	Use sudo on Linux/macOS or run PowerShell as Admin on Windows
nginx: command not found	Reinstall NGINX or add to PATH
SSL certificate not found	Verify certificate paths match your OS in nginx.conf
502 Bad Gateway	Check if backend servers (api.py) are running

## Configuration Notes
- Load balancing algorithm: least_conn (can change to round_robin, ip_hash, etc.)
- Backend servers: Update the upstream backend_servers block with your server IPs/ports
- Proxy headers: Already configured for proper X-Forwarded headers
- Timeouts: proxy_connect_timeout 5s, proxy_read_timeout 30s (adjustable)
