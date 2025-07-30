# Assignment Submission Checklist

## Part One: Local Implementation ✅
- [x] HTML/CSS/JavaScript application created
- [x] External API integrated (CurrencyAPI)
- [x] User interaction features implemented:
  - [x] Search/filter functionality
  - [x] Currency conversion
  - [x] Data display (exchange rates)
  - [x] History tracking
- [x] Error handling implemented
- [x] Application serves practical purpose

## Part Two: Deployment Preparation
### Docker Setup
- [ ] Docker Desktop running
- [ ] Docker image built successfully
- [ ] Docker image tested locally
- [ ] Docker Hub account created
- [ ] Image pushed to Docker Hub

### Lab Server Deployment
- [ ] Access to lab servers (Web01, Web02, Lb01)
- [ ] Application deployed on Web01
- [ ] Application deployed on Web02
- [ ] Load balancer configured
- [ ] End-to-end testing completed
- [ ] Load balancing verified

## Documentation
- [x] README.md comprehensive and complete
- [x] .gitignore file created
- [x] API credits included
- [x] Security practices documented
- [ ] Demo script prepared

## Final Submission
- [ ] GitHub repository created
- [ ] All code pushed to GitHub
- [ ] Demo video recorded (max 2 minutes)
- [ ] GitHub repository link ready
- [ ] Demo video link ready

## Commands You'll Need to Execute

### Once Docker is Running:
```bash
# 1. Build the image
docker build -t <your-dockerhub-username>/currency-exchange:v1 .

# 2. Test locally
docker run -p 8080:8080 <your-dockerhub-username>/currency-exchange:v1

# 3. Login to Docker Hub
docker login

# 4. Push to Docker Hub  
docker push <your-dockerhub-username>/currency-exchange:v1
docker tag <your-dockerhub-username>/currency-exchange:v1 <your-dockerhub-username>/currency-exchange:latest
docker push <your-dockerhub-username>/currency-exchange:latest
```

### For Git (after creating GitHub repo):
```bash
git init
git add .
git commit -m "Initial commit: Currency Exchange Dashboard"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

## Next Immediate Steps:
1. Start Docker Desktop
2. Build and test Docker image
3. Create GitHub repository
4. Create Docker Hub account
5. Push to both platforms
