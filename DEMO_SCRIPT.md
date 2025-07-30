# Demo Video Script (Max 2 Minutes)

## Opening (15 seconds)
"Hello! This is my Currency Exchange Dashboard - a real-time application that fetches live exchange rates and allows currency conversion."

## Local Demo (45 seconds)
1. **Show the application running locally**
   - Open browser to `http://localhost:8080`
   - Demonstrate key features:
     - Live exchange rates display
     - Currency search functionality
     - Currency conversion (e.g., 100 USD to EUR)
     - Conversion history

2. **Highlight technical aspects:**
   - "Uses CurrencyAPI for real-time data"
   - "Built with vanilla JavaScript, containerized with Docker"

## Deployment Demo (45 seconds)
1. **Show Docker Hub image**
   - Open Docker Hub repository
   - Show the published image

2. **Show load balancer access**
   - Access application through load balancer
   - Demonstrate that it works the same way
   - "This is now running on two web servers with load balancing"

3. **Show load balancing in action**
   - Multiple requests showing traffic distribution
   - "Traffic is being distributed between Web01 and Web02"

## Closing (15 seconds)
"The application successfully demonstrates API integration, containerization, and load-balanced deployment. Thank you!"

## Key Points to Mention:
- Real-time API integration (CurrencyAPI)
- User interaction features (search, convert, history)
- Containerized with Docker
- Deployed on multiple servers with load balancing
- Proper security practices (API key management)
