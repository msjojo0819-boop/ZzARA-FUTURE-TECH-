# MenuMind - Technical Specification

## Product Overview
**Product Name:** MenuMind
**Version:** 1.0
**Category:** Restaurant Menu Optimization AI
**Priority:** Tier 3 - Vertical AI Solution

## Executive Summary
MenuMind is an AI-powered menu engineering platform that helps restaurants maximize revenue per guest through data-driven menu design, dynamic pricing, item placement optimization, and food cost analysis. It turns menu design from guesswork into science.

## Core Features

### 1. Menu Engineering Engine
- **Item Classification:** Stars (high profit + high popularity), Puzzles (high profit + low popularity), Plowhorses (low profit + high popularity), Dogs (low profit + low popularity)
- **Placement Optimization:** AI-recommended item positioning based on eye-tracking heat maps
- **Description Writer:** AI-generated menu descriptions that increase order rates
- **Photo Impact Analysis:** Measure how item photos affect ordering behavior
- **Cross-Sell Recommender:** Suggest pairings and upsells that increase check averages

### 2. Dynamic Pricing Engine
- **Demand-Based Pricing:** Adjust prices based on time of day, day of week, season
- **Competitor Monitoring:** Track competitor pricing from delivery apps and menus
- **Price Sensitivity Testing:** A/B test price points to find optimal revenue points
- **Bundle Builder:** Create meal deals that feel like value while maintaining margins
- **Happy Hour Optimizer:** Dynamic happy hour pricing for maximum traffic and margin

### 3. Food Cost Analyzer
- **Recipe Costing:** Input recipes → get real-time food cost percentages
- **Ingredient Price Tracker:** Monitor supplier pricing, alert on significant changes
- **Waste Tracker:** Log and analyze food waste by item and ingredient
- **Portion Control:** Photo-based portion verification using computer vision
- **Substitute Finder:** Suggest ingredient substitutions when costs spike

### 4. Analytics Dashboard
- **Revenue per Item:** Track contribution margin for every menu item
- **Menu Mix Analysis:** Visualize what's selling vs. what's profitable
- **Seasonal Trends:** Predict demand shifts by season, weather, local events
- **Guest Behavior Insights:** Average check, dwell time, ordering patterns
- **A/B Test Results:** Track performance of menu changes in real-time

## Technical Architecture

### System Design
```
┌─────────────────────────────────────────┐
│         MenuMind Dashboard              │
│    (React + Chart.js + Menu Editor)     │
├─────────────────────────────────────────┤
│           API Gateway (Express)          │
├──────────┬──────────┬───────────────────┤
│ Menu     │ Pricing  │ Food Cost         │
│ Engine   │ Engine   │ Analyzer          │
├──────────┴──────────┴───────────────────┤
│        POS Integration Layer             │
│  (Toast, Square, Clover, Lightspeed)     │
├──────────┬──────────┬───────────────────┤
│ Postgres │ Redis    │ S3 (Images)       │
└──────────┴──────────┴───────────────────┘
```

### Database Schema
```sql
CREATE TABLE restaurants (
    id UUID PRIMARY KEY,
    name VARCHAR(255),
    cuisine_type VARCHAR(100),
    location JSONB,
    pos_integration JSONB,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE menu_items (
    id UUID PRIMARY KEY,
    restaurant_id UUID REFERENCES restaurants(id),
    name VARCHAR(255),
    description TEXT,
    category VARCHAR(100),
    price DECIMAL(8,2),
    food_cost DECIMAL(8,2),
    food_cost_pct FLOAT,
    classification VARCHAR(20),
    position_data JSONB,
    photo_url TEXT,
    active BOOLEAN DEFAULT TRUE
);

CREATE TABLE recipes (
    id UUID PRIMARY KEY,
    menu_item_id UUID REFERENCES menu_items(id),
    ingredients JSONB NOT NULL,
    total_cost DECIMAL(8,2),
    yield_portions INT,
    cost_per_portion DECIMAL(8,2),
    updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE sales_data (
    id BIGSERIAL PRIMARY KEY,
    restaurant_id UUID REFERENCES restaurants(id),
    menu_item_id UUID REFERENCES menu_items(id),
    quantity INT,
    revenue DECIMAL(8,2),
    day_of_week INT,
    hour_of_day INT,
    weather VARCHAR(50),
    sold_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE price_tests (
    id UUID PRIMARY KEY,
    menu_item_id UUID REFERENCES menu_items(id),
    test_price DECIMAL(8,2),
    control_price DECIMAL(8,2),
    test_orders INT DEFAULT 0,
    control_orders INT DEFAULT 0,
    test_revenue DECIMAL(10,2) DEFAULT 0,
    control_revenue DECIMAL(10,2) DEFAULT 0,
    started_at TIMESTAMP,
    ended_at TIMESTAMP,
    status VARCHAR(20) DEFAULT 'running'
);
```

### POS Integrations
| POS System | Integration Type | Data Synced |
|------------|-----------------|-------------|
| Toast | REST API | Sales, menu, inventory |
| Square | OAuth + Webhooks | Sales, menu, customers |
| Clover | REST API | Sales, menu, orders |
| Lightspeed | REST API | Sales, menu, inventory |
| Aloha | File-based | Sales exports |

### API Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/menu/:restaurantId` | Get current menu with analytics |
| POST | `/api/menu/optimize` | Run menu optimization |
| PUT | `/api/menu/items/:id` | Update menu item |
| POST | `/api/pricing/test` | Start A/B price test |
| GET | `/api/analytics/:restaurantId` | Get sales analytics |
| POST | `/api/recipes` | Add/update recipe costing |
| GET | `/api/food-cost/:restaurantId` | Get food cost report |
| POST | `/api/descriptions/generate` | AI-generate menu descriptions |

## Monetization
- **Starter ($49/mo):** 1 location, menu engineering, basic analytics
- **Growth ($129/mo):** 3 locations, dynamic pricing, POS integration, food cost
- **Chain ($299/mo):** 10 locations, multi-unit comparison, A/B testing
- **Enterprise ($599/mo):** Unlimited locations, API, white-label, custom models

## Target Markets
- Independent restaurants
- Restaurant groups (5-50 locations)
- Ghost kitchens / virtual brands
- Hotel food & beverage operations
- Catering companies
