# JRNY Unified Platform Architecture
**Version:** April 2026 — Post Lovable Audit  
**Architect:** POS (CFO Agent) based on Lovable project audit  
**Lead Dev:** Lorenz (AWS/Node.js)  
**Backend:** pineappledash (Cloudflare Worker) — Rezdy + Stripe + GA4 connectors live  

---

## Platform Vision

> **TikTok-style content discovery → frictionless in-context booking**  
> Region-adaptive UI per country. B2B2C: operators list inventory, consumers discover + book.

```
VISITOR ARRIVES IN QLD
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 1: DISCOVERY (content → intent)                          │
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────┐  │
│  │ Brisbane Explorer │  │ qld-route-advs   │  │ geo-story-hub│  │
│  │ (Tour Brisbane)   │  │ (Airport → Routes)│  │ (TBD: geo    │  │
│  │ Full tour listing │  │ 8 airports +     │  │  stories     │  │
│  │ with Stripe       │  │ 50+ destinations │  │  + auth)     │  │
│  └──────────────────┘  └──────────────────┘  └──────────────┘  │
│                                                                  │
│  Input signals: airport arrival, location, search intent         │
│  Output: experiences ranked by proximity + demand               │
└─────────────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 2: PLANNING (intent → trip)                              │
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────┐  │
│  │ translink-journey│  │ Journey Genie     │  │ Route Intel  │  │
│  │ -map             │  │ (Group flight     │  │ Hub          │  │
│  │ (QLD Transit GTFS│  │  booking/arrival  │  │ (Explore     │  │
│  │  Rail/Bus/Ferry) │  │  signals)         │  │  Experiences)│  │
│  └──────────────────┘  └──────────────────┘  └──────────────┘  │
│                                                                  │
│  Input: visitor intent + arrival airport + group size           │
│  Output: complete itinerary with transport + experiences        │
└─────────────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 3: BOOKING (trip → commerce)                             │
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────┐  │
│  │ jrny-event-rover │  │ hop-on-hop-off   │  │ hop-on-      │  │
│  │ ⭐ CORE PRODUCT  │  │ (Journey Hub     │  │ template     │  │
│  │ Event transfers  │  │  consumer app    │  │ (CityTours   │  │
│  │ via Rezdy        │  │  live map + nav) │  │  → rebrand)  │  │
│  └──────────────────┘  └──────────────────┘  └──────────────┘  │
│                                                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  pineappledash (Cloudflare Worker — already LIVE)        │   │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────────────┐   │   │
│  │  │  Rezdy   │  │  Stripe  │  │  GA4 Analytics       │   │   │
│  │  │ connector│  │ connector│  │  connector           │   │   │
│  │  └──────────┘  └──────────┘  └──────────────────────┘   │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
│  Input: trip plan + group + dates                                │
│  Output: confirmed booking with payment + Rezdy confirmation    │
└─────────────────────────────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 4: OPERATOR B2B                                          │
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────┐  │
│  │ HOHO Partner     │  │ Tour Site Builder│  │ Hopio        │  │
│  │ Portal           │  │ (SEO microsite   │  │ Investor Hub │  │
│  │ (jrnyau.lovable  │  │  generator for   │  │ (separate    │  │
│  │  .app)           │  │  tour operators) │  │  investor    │  │
│  │ Full B2B pitch   │  │ GoldCoast demo   │  │  brand)      │  │
│  └──────────────────┘  └──────────────────┘  └──────────────┘  │
│                                                                  │
│  Input: operator sign-up / partner interest                     │
│  Output: onboarded operator with Rezdy integration              │
└─────────────────────────────────────────────────────────────────┘
```

---

## Domain Architecture

```
jrny.au                    → Brisbane Explorer (primary consumer entry)
jrny.au/hop-on             → hop-on-template (CityTours rebrand)
jrny.au/events             → jrny-event-rover (event transfers)
jrny.au/explore            → Route Intelligence Hub (experience marketplace)
jrny.au/plan               → translink-journey-map + Journey Genie
app.jrny.au                → hop-on-hop-off (Journey Hub mobile app/PWA)
partners.jrny.au           → HOHO Partner Portal (B2B acquisition)
hopio.com.au               → Investor/Government brand (keep separate)
```

---

## Integration Priority Queue for Lorenz

### Sprint 1 — May 1 Launch (NOW)
| Project | Action | Backend Connection |
|---------|--------|-------------------|
| jrny-event-rover | Deploy to jrny.au/events | pineappledash Rezdy connector (already live) |
| hop-on-template | Rebrand "CityTours" → JRNY | pineappledash Stripe + Rezdy |
| Brisbane Explorer | Verify Stripe keys, deploy | pineappledash Stripe connector |
| HOHO Partner Portal | Deploy to partners.jrny.au | Static (no backend needed) |

### Sprint 2 — May Post-Launch
| Project | Action | Backend Connection |
|---------|--------|-------------------|
| hop-on-hop-off | Mobile PWA deploy | pineappledash real-time Rezdy |
| qld-route-adventures | Add Mapbox API key | New: Mapbox + pineappledash GA4 |
| translink-journey-map | Connect QLD GTFS feed | New: GTFS data pipeline |
| Route Intelligence Hub | Connect to live Rezdy inventory | pineappledash Rezdy |

### Sprint 3 — June
| Project | Action | Backend Connection |
|---------|--------|-------------------|
| Journey Genie | Connect to inbound flight data | New: aviation API |
| Tour Site Builder | Build as Pineapple Tours SEO generator | pineappledash Rezdy |
| geo-story-hub | Investigate + integrate if valid | TBD |

---

## Platform Data Flow

```
Visitor lands on jrny.au (Brisbane Explorer)
        │
        ├─ Browses tours by category
        ├─ Search filters by location/date/price
        ├─ Selects tour → Stripe payment via pineappledash
        │
        ▼
        Rezdy booking confirmed (pineappledash)
        GA4 event tracked (pineappledash)
        Operator notified via Rezdy
        │
        ▼
Day of travel:
        │
        └─ Opens hop-on-hop-off (Journey Hub app)
                ├─ Live bus tracking (Explore tab)
                ├─ Transit routes (Transit tab)
                ├─ Booking reference (Bookings tab)
                └─ Support chat (Messaging tab)
```

---

## Archive List (Kill Candidates — Remove from Active Lovable)

| Project | Reason |
|---------|--------|
| Apex Flow (align-pro-ai) | B2B KPI dashboard — no JRNY relevance |
| Conversion Catalyst | "Killer Influence" sales course — completely off-brand |
| Goal Circle (GoalSync) | Social goal tracker — no relevance |
| ari-spark-learn | Personal (Ari's learning app) |
| glass-flow-spark (Float OS) | Productivity/wellness — no relevance |
| opportunity-alchemy-engine | Business CRM — no JRNY relevance |

---

## Key Technical Notes

- **pineappledash** is the Cloudflare Worker serving as the data backbone — it already has live Rezdy + Stripe + GA4 connectors. All Lovable frontend projects should point to pineappledash for transactional operations.
- **Rezdy** is the booking/inventory system. All tour inventory lives here. pineappledash exposes it to the frontend.
- **Mapbox** is needed for qld-route-adventures and translink-journey-map — get API key from Pete.
- **Supabase** is used by at least geo-story-hub and opportunity-alchemy-engine for auth. Check if the same Supabase project is used across all — consolidate if so.
- All Lovable projects should have GitHub repos. Verify with `gh repo list Pineapples100` before cancelling Lovable subscription.

---

## $300/month Lovable — Cancel Checklist

- [ ] Verify all INTEGRATE projects are synced to GitHub (`Sync with GitHub` in Lovable editor)
- [ ] Export any projects NOT in GitHub (check: Conversion Catalyst, Goal Circle, ari-spark-learn)
- [ ] Confirm pineappledash is operational and tested
- [ ] Deploy jrny-event-rover + hop-on-template + Brisbane Explorer (Sprint 1)
- [ ] Cancel Lovable subscription (keep free tier for reference)
- [ ] Re-subscribe for Sprint 2 if Lovable UI iteration is needed (1 month at a time)

---

*Last updated: 21 April 2026 | Auditor: POS*
