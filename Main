from fastapi import FastAPI, HTTPException
from fastapi.middleware.cors import CORSMiddleware
from pydantic import BaseModel
from typing import Optional, List
import anthropic
import os
import json

app = FastAPI(
    title="ShopBoost E-commerce API",
    description="""
## 🛒 ShopBoost — The Ultimate E-commerce API for Small Businesses

Power your online store with AI-driven product intelligence.

### Endpoints
| Feature | Endpoint | Description |
|---|---|---|
| 🤖 AI Description | `/product/describe` | Generate SEO-ready product descriptions |
| 💰 Price Analysis | `/product/price-analysis` | Smart pricing recommendations |
| 🔍 Barcode Lookup | `/product/barcode` | Get product info from barcode |
| ⭐ Review Analyzer | `/product/reviews/analyze` | Sentiment + insights from reviews |
| 📦 Product Bundler | `/product/bundle-suggest` | AI bundle & upsell suggestions |
| 🏷️ Title Optimizer | `/product/title-optimize` | SEO-optimized product titles |
| 🌍 Competitor Intel | `/product/competitor-analysis` | Pricing & positioning insights |

### Pricing Plans
- **Free**: 20 requests/day
- **Starter** ($9.99/mo): 500 requests/day
- **Growth** ($29.99/mo): 2000 requests/day
- **Scale** ($79.99/mo): Unlimited
    """,
    version="1.0.0",
    contact={"name": "ShopBoost Support", "email": "support@shopboost.api"},
)

app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)

# ─────────────────────────────────────────
# Request / Response Models
# ─────────────────────────────────────────

class DescribeRequest(BaseModel):
    product_name: str
    category: str
    key_features: List[str]
    tone: Optional[str] = "professional"        # professional, casual, luxury, playful
    target_audience: Optional[str] = "general"
    word_count: Optional[int] = 150

class PriceAnalysisRequest(BaseModel):
    product_name: str
    category: str
    your_cost: float                             # your cost price in USD
    competitor_prices: Optional[List[float]] = []
    condition: Optional[str] = "new"             # new, used, refurbished

class BarcodeRequest(BaseModel):
    barcode: str                                 # EAN-13, UPC-A, etc.
    format: Optional[str] = "auto"

class ReviewAnalysisRequest(BaseModel):
    product_name: str
    reviews: List[str]                           # list of review strings

class BundleRequest(BaseModel):
    product_name: str
    category: str
    current_price: float
    existing_inventory: Optional[List[str]] = []

class TitleOptimizeRequest(BaseModel):
    current_title: str
    category: str
    platform: Optional[str] = "general"         # amazon, shopify, etsy, general

class CompetitorRequest(BaseModel):
    product_name: str
    category: str
    your_price: float
    your_features: Optional[List[str]] = []


# ─────────────────────────────────────────
# Helper
# ─────────────────────────────────────────

def claude(prompt: str, system: str = "", max_tokens: int = 1024) -> str:
    api_key = os.environ.get("ANTHROPIC_API_KEY")
    if not api_key:
        raise HTTPException(status_code=500, detail="ANTHROPIC_API_KEY not configured on server.")
    client = anthropic.Anthropic(api_key=api_key)
    msgs = [{"role": "user", "content": prompt}]
    kwargs = dict(model="claude-sonnet-4-20250514", max_tokens=max_tokens, messages=msgs)
    if system:
        kwargs["system"] = system
    resp = client.messages.create(**kwargs)
    return resp.content[0].text

def parse_json(raw: str) -> dict:
    try:
        cleaned = raw.strip().removeprefix("```json").removeprefix("```").removesuffix("```").strip()
        return json.loads(cleaned)
    except Exception:
        return {"raw_response": raw}


# ─────────────────────────────────────────
# Routes
# ─────────────────────────────────────────

@app.get("/", tags=["Info"])
def root():
    return {
        "api": "ShopBoost E-commerce API",
        "version": "1.0.0",
        "endpoints": [
            "/product/describe",
            "/product/price-analysis",
            "/product/barcode",
            "/product/reviews/analyze",
            "/product/bundle-suggest",
            "/product/title-optimize",
            "/product/competitor-analysis",
        ]
    }


# 1. ── AI PRODUCT DESCRIPTION GENERATOR ──────────────────────────────────────

@app.post("/product/describe", tags=["AI Tools"])
def generate_description(req: DescribeRequest):
    """
    Generate SEO-optimised product descriptions using AI.
    Supports multiple tones: professional, casual, luxury, playful.
    """
    features_str = "\n".join(f"- {f}" for f in req.key_features)
    prompt = f"""
Write a product description for an e-commerce listing.

Product: {req.product_name}
Category: {req.category}
Key features:
{features_str}
Tone: {req.tone}
Target audience: {req.target_audience}
Target word count: {req.word_count} words

Requirements:
- Include natural SEO keywords
- Use the specified tone throughout
- End with a subtle call-to-action
- Do NOT use bullet points — write in flowing paragraphs
- Return ONLY the description text, nothing else
"""
    description = claude(prompt)
    word_count = len(description.split())
    return {
        "product_name": req.product_name,
        "description": description,
        "word_count": word_count,
        "tone": req.tone,
        "seo_tip": f"Use '{req.product_name}' in your page title and first 100 characters of your listing."
    }


# 2. ── PRICE ANALYSIS & RECOMMENDATION ───────────────────────────────────────

@app.post("/product/price-analysis", tags=["Pricing"])
def price_analysis(req: PriceAnalysisRequest):
    """
    AI-powered pricing strategy. Recommends optimal price, margin, and
    positioning based on your cost and competitor prices.
    """
    comp_str = ", ".join([f"${p}" for p in req.competitor_prices]) if req.competitor_prices else "not provided"
    prompt = f"""
You are a pricing strategist for small e-commerce businesses.

Product: {req.product_name}
Category: {req.category}
My cost price: ${req.your_cost}
Competitor prices: {comp_str}
Condition: {req.condition}

Respond ONLY with this JSON (no markdown):
{{
  "recommended_price": <float>,
  "minimum_viable_price": <float>,
  "premium_price": <float>,
  "suggested_margin_percent": <float>,
  "pricing_strategy": "<penetration|competitive|premium|value>",
  "strategy_explanation": "<2 sentences>",
  "psychological_price_tip": "<e.g. use .99 pricing>",
  "profit_at_recommended": <float>
}}
"""
    raw = claude(prompt)
    result = parse_json(raw)
    result["your_cost"] = req.your_cost
    result["competitor_prices"] = req.competitor_prices
    return result


# 3. ── BARCODE LOOKUP ─────────────────────────────────────────────────────────

@app.post("/product/barcode", tags=["Product Data"])
def barcode_lookup(req: BarcodeRequest):
    """
    Look up product information by barcode (EAN-13, UPC-A, ISBN, etc.).
    Returns category, likely product type, and listing suggestions.
    """
    if not req.barcode.strip().isdigit():
        raise HTTPException(status_code=400, detail="Barcode must be numeric digits only.")

    prompt = f"""
A user scanned barcode: {req.barcode} (format: {req.format})

Based on the barcode structure and common product databases, respond ONLY with this JSON (no markdown):
{{
  "barcode": "{req.barcode}",
  "barcode_format": "<EAN-13|UPC-A|ISBN-13|EAN-8|unknown>",
  "likely_category": "<best guess category>",
  "country_of_origin_code": "<first 2-3 digits indicate country>",
  "listing_title_suggestion": "<suggested product title for e-commerce>",
  "recommended_categories": ["<cat1>", "<cat2>", "<cat3>"],
  "note": "<any useful note about this barcode>"
}}
"""
    raw = claude(prompt)
    return parse_json(raw)


# 4. ── REVIEW SENTIMENT ANALYZER ─────────────────────────────────────────────

@app.post("/product/reviews/analyze", tags=["AI Tools"])
def analyze_reviews(req: ReviewAnalysisRequest):
    """
    Deep analysis of customer reviews. Returns sentiment breakdown,
    top praised features, top complaints, and an improvement roadmap.
    """
    if not req.reviews:
        raise HTTPException(status_code=400, detail="Provide at least one review.")

    reviews_str = "\n".join([f"{i+1}. {r}" for i, r in enumerate(req.reviews)])
    prompt = f"""
Analyze these customer reviews for "{req.product_name}":

{reviews_str}

Respond ONLY with this JSON (no markdown):
{{
  "overall_sentiment": "<positive|neutral|negative|mixed>",
  "sentiment_score": <0.0 to 10.0>,
  "total_reviews_analyzed": {len(req.reviews)},
  "positive_count": <int>,
  "neutral_count": <int>,
  "negative_count": <int>,
  "top_praised_features": ["<feature1>", "<feature2>", "<feature3>"],
  "top_complaints": ["<complaint1>", "<complaint2>"],
  "improvement_suggestions": ["<suggestion1>", "<suggestion2>"],
  "marketing_angle": "<one powerful sentence to use in product marketing based on reviews>",
  "response_to_negative_template": "<a professional template reply to negative reviews>"
}}
"""
    raw = claude(prompt, max_tokens=1500)
    return parse_json(raw)


# 5. ── AI BUNDLE & UPSELL SUGGESTER (BONUS) ──────────────────────────────────

@app.post("/product/bundle-suggest", tags=["Growth Tools"])
def bundle_suggest(req: BundleRequest):
    """
    ✨ BONUS: AI suggests product bundles, upsells, and cross-sells to
    increase average order value (AOV) for your store.
    """
    inventory_str = ", ".join(req.existing_inventory) if req.existing_inventory else "not specified"
    prompt = f"""
You are an e-commerce growth expert helping small businesses increase revenue.

Main product: {req.product_name}
Category: {req.category}
Price: ${req.current_price}
Existing inventory: {inventory_str}

Respond ONLY with this JSON (no markdown):
{{
  "bundle_ideas": [
    {{"bundle_name": "<name>", "items": ["<item1>", "<item2>"], "suggested_bundle_price": <float>, "discount_percent": <float>, "why_it_works": "<reason>"}},
    {{"bundle_name": "<name>", "items": ["<item1>", "<item2>"], "suggested_bundle_price": <float>, "discount_percent": <float>, "why_it_works": "<reason>"}}
  ],
  "upsell_suggestions": ["<premium version or upgrade>", "<another upsell>"],
  "cross_sell_suggestions": ["<complementary product>", "<another>", "<another>"],
  "aov_increase_estimate_percent": <float>,
  "best_bundle_pitch": "<one sentence to pitch the top bundle to customers>"
}}
"""
    raw = claude(prompt)
    return parse_json(raw)


# 6. ── SEO TITLE OPTIMIZER (BONUS) ───────────────────────────────────────────

@app.post("/product/title-optimize", tags=["Growth Tools"])
def title_optimize(req: TitleOptimizeRequest):
    """
    ✨ BONUS: Rewrites your product title to maximise clicks and SEO ranking
    on Amazon, Shopify, Etsy, or any platform.
    """
    prompt = f"""
You are an expert e-commerce SEO specialist.

Current product title: "{req.current_title}"
Category: {req.category}
Target platform: {req.platform}

Respond ONLY with this JSON (no markdown):
{{
  "original_title": "{req.current_title}",
  "optimized_title": "<SEO-optimized title>",
  "title_score_before": <0-100>,
  "title_score_after": <0-100>,
  "improvements_made": ["<improvement1>", "<improvement2>", "<improvement3>"],
  "keywords_added": ["<kw1>", "<kw2>", "<kw3>"],
  "character_count": <int>,
  "platform_tip": "<specific tip for {req.platform}>"
}}
"""
    raw = claude(prompt)
    return parse_json(raw)


# 7. ── COMPETITOR INTELLIGENCE (BONUS) ───────────────────────────────────────

@app.post("/product/competitor-analysis", tags=["Growth Tools"])
def competitor_analysis(req: CompetitorRequest):
    """
    ✨ BONUS: AI-powered competitive positioning. Understand where your product
    stands, how to differentiate, and how to write copy that beats competitors.
    """
    features_str = ", ".join(req.your_features) if req.your_features else "not specified"
    prompt = f"""
You are a competitive intelligence analyst for e-commerce.

My product: {req.product_name}
Category: {req.category}
My price: ${req.your_price}
My features: {features_str}

Respond ONLY with this JSON (no markdown):
{{
  "market_position": "<budget|mid-range|premium|luxury>",
  "competitive_strengths": ["<strength1>", "<strength2>"],
  "potential_weaknesses": ["<weakness1>", "<weakness2>"],
  "differentiation_angles": ["<angle1>", "<angle2>", "<angle3>"],
  "suggested_unique_selling_proposition": "<one powerful USP sentence>",
  "pricing_position_advice": "<should you raise, lower, or hold price and why>",
  "winning_ad_headline": "<a compelling ad headline that beats generic competitors>",
  "target_keywords_to_rank_for": ["<kw1>", "<kw2>", "<kw3>", "<kw4>"]
}}
"""
    raw = claude(prompt)
    return parse_json(raw)
