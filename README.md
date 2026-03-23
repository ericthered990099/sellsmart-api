# 🛒 ShopBoost E-commerce API

> AI-powered product intelligence for small e-commerce businesses.  
> Built with **FastAPI** + **Claude AI** — ready to publish on **RapidAPI**.

---

## 🚀 7 Powerful Endpoints

| # | Endpoint | Feature | Value |
|---|----------|---------|-------|
| 1 | `POST /product/describe` | AI Product Description Generator | Write SEO-ready copy instantly |
| 2 | `POST /product/price-analysis` | Smart Price Analyzer | Know your optimal price |
| 3 | `POST /product/barcode` | Barcode Lookup | Get product info from any barcode |
| 4 | `POST /product/reviews/analyze` | Review Sentiment Analyzer | Turn reviews into insights |
| 5 | `POST /product/bundle-suggest` | ✨ Bundle & Upsell Suggester | Increase average order value |
| 6 | `POST /product/title-optimize` | ✨ SEO Title Optimizer | Rank higher, get more clicks |
| 7 | `POST /product/competitor-analysis` | ✨ Competitor Intelligence | Beat your competition |

---

## ▶️ Run Locally

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Set API key
cp .env.example .env
# Edit .env → add your Anthropic API key

# 3. Run
uvicorn main:app --reload
```

- API: http://localhost:8000  
- Interactive docs: http://localhost:8000/docs

---

## 🌐 Deploy (Required for RapidAPI)

### Railway (Recommended — Free tier)
```bash
# 1. Push to GitHub
git init && git add . && git commit -m "init"
git remote add origin https://github.com/YOUR_USERNAME/shopboost-api
git push -u origin main

# 2. Go to https://railway.app → New Project → Deploy from GitHub
# 3. Add environment variable: ANTHROPIC_API_KEY=your_key
# 4. Get your URL: https://shopboost-api.railway.app
```

### Render
1. https://render.com → New Web Service
2. Build: `pip install -r requirements.txt`
3. Start: `uvicorn main:app --host 0.0.0.0 --port 8000`
4. Add env var: `ANTHROPIC_API_KEY`

---

## 💰 Publish on RapidAPI & Earn

### Step-by-step
1. Go to https://rapidapi.com/provider → **Add New API**
2. Name: `ShopBoost E-commerce API`
3. Category: `eCommerce` + `Artificial Intelligence`
4. Base URL: your deployed URL
5. Add all 7 endpoints with descriptions
6. Set pricing:

| Plan | Price | Requests/day | Target |
|------|-------|--------------|--------|
| Free | $0 | 20 | Hook developers |
| Starter | $9.99/mo | 500 | Solo sellers |
| Growth | $29.99/mo | 2,000 | Small stores |
| Scale | $79.99/mo | Unlimited | Growing brands |

### 💡 Income Estimate
- 50 Starter subscribers = **$499/mo**
- 20 Growth subscribers = **$599/mo**
- 5 Scale subscribers = **$399/mo**
- **Total potential: ~$1,500/mo** with 75 subscribers

---

## 📋 API Usage Examples

### 1. Generate Product Description
```bash
curl -X POST "https://your-api.railway.app/product/describe" \
  -H "Content-Type: application/json" \
  -d '{
    "product_name": "Wireless Noise-Cancelling Headphones",
    "category": "Electronics",
    "key_features": ["40hr battery", "Active noise cancellation", "Foldable design"],
    "tone": "professional",
    "target_audience": "remote workers",
    "word_count": 150
  }'
```

### 2. Price Analysis
```bash
curl -X POST "https://your-api.railway.app/product/price-analysis" \
  -H "Content-Type: application/json" \
  -d '{
    "product_name": "Ceramic Coffee Mug Set",
    "category": "Kitchen",
    "your_cost": 8.50,
    "competitor_prices": [24.99, 19.99, 29.99],
    "condition": "new"
  }'
```

### 3. Barcode Lookup
```bash
curl -X POST "https://your-api.railway.app/product/barcode" \
  -H "Content-Type: application/json" \
  -d '{"barcode": "8901030871924", "format": "auto"}'
```

### 4. Review Sentiment Analysis
```bash
curl -X POST "https://your-api.railway.app/product/reviews/analyze" \
  -H "Content-Type: application/json" \
  -d '{
    "product_name": "Yoga Mat",
    "reviews": [
      "Great mat, very grippy and thick!",
      "Arrived damaged, poor packaging.",
      "Perfect for my morning routine, highly recommend."
    ]
  }'
```

### 5. Bundle Suggester ✨
```bash
curl -X POST "https://your-api.railway.app/product/bundle-suggest" \
  -H "Content-Type: application/json" \
  -d '{
    "product_name": "Yoga Mat",
    "category": "Fitness",
    "current_price": 35.00,
    "existing_inventory": ["resistance bands", "foam roller", "water bottle"]
  }'
```

### 6. SEO Title Optimizer ✨
```bash
curl -X POST "https://your-api.railway.app/product/title-optimize" \
  -H "Content-Type: application/json" \
  -d '{
    "current_title": "Blue Yoga Mat",
    "category": "Fitness",
    "platform": "amazon"
  }'
```

### 7. Competitor Intelligence ✨
```bash
curl -X POST "https://your-api.railway.app/product/competitor-analysis" \
  -H "Content-Type: application/json" \
  -d '{
    "product_name": "Stainless Steel Water Bottle",
    "category": "Kitchen",
    "your_price": 22.99,
    "your_features": ["500ml", "leak-proof", "BPA-free", "keeps cold 24hr"]
  }'
```

---

## 🔑 Get API Keys
- **Anthropic (Claude AI)**: https://console.anthropic.com
- **RapidAPI Provider**: https://rapidapi.com/provider

---

## 📁 Project Structure
```
ecommerce-api/
├── main.py          ← All 7 API endpoints
├── requirements.txt ← Python dependencies
├── Procfile         ← For Railway/Heroku deploy
├── .env.example     ← Environment variable template
└── README.md        ← This file
```
