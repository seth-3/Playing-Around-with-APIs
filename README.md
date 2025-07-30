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

## Credits

- **CurrencyAPI**: https://currencyapi.com/ - for real-time exchange rates
- **FlagsAPI**: https://flagsapi.com/ - for country flag icons
