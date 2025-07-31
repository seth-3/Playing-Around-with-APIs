# Currency Exchange Dashboard

A real-time currency exchange dashboard that provides live exchange rates, currency conversion, and conversion history tracking using the CurrencyAPI.

## Features

- **Live Exchange Rates**: Real-time currency rates from CurrencyAPI
- **Currency Conversion**: Convert between different currencies with live rates
- **Search & Filter**: Search through available currencies
- **Conversion History**: Track your recent conversions (stored locally)
- **Responsive Design**: Works on desktop and mobile devices
- **Flag Icons**: Visual currency representation with country flags

## API Used

- **CurrencyAPI**: https://currencyapi.com/
- **Flags API**: https://flagsapi.com/ (for country flag icons)

## Prerequisites

- Docker installed and running
- Docker Hub account (for deployment)
- Access to lab servers (Web01, Web02, Lb01)

## Local Development

### Option 1: Direct Browser Testing
1. Simply open `index.html` in your browser
2. The application will work directly without a server

### Option 2: Docker Testing
1. **Start Docker Desktop** (if not running)

2. **Build the Docker image:**
   ```bash
   docker build -t sethira/currency-exchange:v1 .
   ```

3. **Run the container:**
   ```bash
   docker run -p 8080:8080 sethira/currency-exchange:v1
   ```

4. **Test locally:**
   ```bash
   curl http://localhost:8080
   ```
   Or open `http://localhost:8080` in your browser

## Docker Hub Deployment

1. **Login to Docker Hub:**
   ```bash
   docker login
   ```

2. **Push to Docker Hub:**
   ```bash
   docker push sethira/currency-exchange:v1
   docker tag sethira/currency-exchange:v1 sethira/currency-exchange:latest
   docker push sethira/currency-exchange:latest
   ```

## Lab Server Deployment

### Step 1: Deploy on Web Servers

**SSH into web-01:**
```bash
ssh user@web-01
```

**Pull and run the image:**
```bash
docker pull sethira/currency-exchange:v1
docker stop app 2>/dev/null || true
docker rm app 2>/dev/null || true
docker run -d --name app --restart unless-stopped \
  -p 8080:8080 \
  sethira/currency-exchange:v1
```

**Repeat the same steps for web-02:**
```bash
ssh user@web-02
# Same docker commands as above
```

**Verify each instance:**
```bash
# From web-01:
curl http://localhost:8080

# From web-02:
curl http://localhost:8080
```

### Step 2: Configure Load Balancer

**SSH into lb-01:**
```bash
ssh user@lb-01
```

**Update HAProxy configuration:**
```bash
sudo nano /etc/haproxy/haproxy.cfg
```

**Add/Update the backend section:**
```
backend webapps
    balance roundrobin
    server web01 172.20.0.11:8080 check
    server web02 172.20.0.12:8080 check
```

**Reload HAProxy:**
```bash
docker exec -it lb-01 sh -c 'haproxy -sf $(pidof haproxy) -f /etc/haproxy/haproxy.cfg'
```

### Step 3: Test End-to-End

**Test load balancing:**
```bash
# Run multiple times to see traffic alternating
curl http://localhost  # or whatever port lb-01 exposes
curl http://localhost
curl http://localhost
```

**Check which server responded:**
```bash
curl -v http://localhost | grep -i server
```

## Security Features

- **API Key Management**: API key can be injected via environment variable `CURRENCY_API_KEY`
- **Security Headers**: X-Frame-Options, X-Content-Type-Options, X-XSS-Protection
- **No Hardcoded Secrets**: API key is not baked into the Docker image

## Architecture

- **Frontend**: HTML5, CSS3, Vanilla JavaScript
- **Container**: Nginx Alpine (lightweight)
- **Load Balancer**: HAProxy (round-robin)
- **Deployment**: Docker containers on 2 web servers + 1 load balancer

## Troubleshooting

1. **Docker not running**: Start Docker Desktop
2. **Port conflicts**: Change port mapping in docker run command
3. **API rate limits**: The free tier has rate limits; wait or upgrade
4. **Load balancer not working**: Check HAProxy logs and server connectivity

## Docker Hub Information

- **Repository URL**: https://hub.docker.com/r/sethira/currency-exchange
- **Image Name**: `sethira/currency-exchange`
- **Available Tags**: `v1`, `latest`
- **Image Size**: ~79.3MB (Alpine-based)

## Build Instructions

### Local Build
```bash
# Build the image locally
docker build -t sethira/currency-exchange:v1 .

# Test locally
docker run -p 8080:8080 sethira/currency-exchange:v1

# Verify it works
curl http://localhost:8080
# Or open http://localhost:8080 in your browser
```

### Push to Docker Hub
```bash
# Login to Docker Hub
docker login

# Push the image
docker push sethira/currency-exchange:v1
docker push sethira/currency-exchange:latest
```

## Lab Server Deployment Instructions

### Prerequisites
- Access to lab servers: web-01, web-02, lb-01
- Docker installed on all servers
- SSH access to servers

### Step-by-Step Deployment

#### 1. Deploy on Web Servers

**On web-01:**
```bash
ssh user@web-01
docker pull sethira/currency-exchange:v1
docker stop app 2>/dev/null || true
docker rm app 2>/dev/null || true
docker run -d --name app --restart unless-stopped \
  -p 8080:8080 \
  sethira/currency-exchange:v1

# Verify
curl http://localhost:8080
```

**On web-02:**
```bash
ssh user@web-02
docker pull sethira/currency-exchange:v1
docker stop app 2>/dev/null || true
docker rm app 2>/dev/null || true
docker run -d --name app --restart unless-stopped \
  -p 8080:8080 \
  sethira/currency-exchange:v1

# Verify
curl http://localhost:8080
```

#### 2. Configure Load Balancer

**On lb-01:**
```bash
ssh user@lb-01

# Edit HAProxy configuration
sudo nano /etc/haproxy/haproxy.cfg
```

**Add this backend configuration:**
```
backend webapps
    balance roundrobin
    option httpchk GET /
    server web01 172.20.0.11:8080 check
    server web02 172.20.0.12:8080 check
```

**Reload HAProxy:**
```bash
# Method 1: Docker container reload
docker exec -it lb-01 sh -c 'haproxy -sf $(pidof haproxy) -f /etc/haproxy/haproxy.cfg'

# Method 2: Service reload (if using systemd)
sudo systemctl reload haproxy
```

#### 3. End-to-End Testing

**Test load balancing:**
```bash
# From your local machine or lb-01
for i in {1..6}; do
  curl -s http://localhost | grep -o "Currency Exchange Dashboard" && echo " - Request $i"
done
```

**Check server responses:**
```bash
# Test individual servers
curl -v http://web-01:8080 2>&1 | grep "HTTP/1.1 200"
curl -v http://web-02:8080 2>&1 | grep "HTTP/1.1 200"

# Test through load balancer
curl -v http://lb-01 2>&1 | grep "HTTP/1.1 200"
```

## Testing Evidence

To verify load balancing works:

1. **Server Health Check:**
   - Both web-01:8080 and web-02:8080 should return HTTP 200
   - HAProxy status page should show both servers as UP

2. **Load Distribution:**
   - Multiple requests to lb-01 should alternate between servers
   - Check HAProxy stats: `http://lb-01:8404/stats` (if enabled)

3. **Expected Behavior:**
   ```
   Request 1 → web-01
   Request 2 → web-02
   Request 3 → web-01
   Request 4 → web-02
   ```

## Security & Hardening

### API Key Management
The application is designed to handle API keys securely:

```bash
# Deploy with environment variable (recommended)
docker run -d --name app --restart unless-stopped \
  -p 8080:8080 \
  -e CURRENCY_API_KEY=your_actual_api_key_here \
  sethira/currency-exchange:v1
```

### Security Headers
The application includes security headers:
- `X-Frame-Options: DENY`
- `X-Content-Type-Options: nosniff`
- `X-XSS-Protection: 1; mode=block`

## Troubleshooting

### Common Issues
1. **Port conflicts**: Change port mapping `-p 8080:8080` to `-p 8081:8080`
2. **API rate limits**: Free tier has limits, wait or upgrade plan
3. **Docker connectivity**: Ensure Docker daemon is running
4. **Network issues**: Check firewall rules and server connectivity

### Debugging Commands
```bash
# Check container logs
docker logs app

# Check HAProxy logs
docker logs lb-01

# Test server connectivity
telnet web-01 8080
telnet web-02 8080
```

## Architecture Overview

```
[Client] → [Load Balancer (lb-01)] → [Web-01:8080 | Web-02:8080]
                                     [Currency Exchange App]
                                            ↓
                                     [CurrencyAPI + FlagsAPI]
```

## Performance Considerations

- **Container Size**: 79.3MB (Alpine-based for efficiency)
- **Memory Usage**: ~50MB per container
- **API Caching**: Consider implementing Redis for API response caching
- **CDN**: Consider using CDN for flag images

## Challenges & Solutions

### Challenge 1: Flag Display Issues
**Problem**: Not all currency flags were displaying
**Solution**: Expanded currency-to-country mapping from 14 to 60+ currencies

### Challenge 2: API Key Security
**Problem**: Hardcoded API key in source code
**Solution**: Implemented environment variable injection for deployment

### Challenge 3: Docker Image Size
**Problem**: Large base images slow deployment
**Solution**: Used Alpine Linux base (79MB vs 200MB+ for Ubuntu)

## Credits

- **CurrencyAPI**: https://currencyapi.com/ - for real-time exchange rates
- **FlagsAPI**: https://flagsapi.com/ - for country flag icons
- **Nginx**: https://nginx.org/ - web server
- **Docker**: https://docker.com/ - containerization platform
