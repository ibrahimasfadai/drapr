# Product Requirements Document: Drapr

## Subject
Drapr - AI-Powered Personal Styling Assistant for Busy Fashion Enthusiasts

---

## Introduction

Drapr is a mobile-first web application that democratizes access to professional-level styling by combining AI-powered outfit curation from Instagram with virtual try-on technology. The platform bridges the gap between fashion inspiration and personal application, allowing users to visualize real-world outfits on themselves and discover where to purchase similar items.

---

## Problem Statement

**Current State:**
Many individuals struggle to stay fashionable due to several pain points:
- Lack of styling expertise or confidence in putting together outfits
- Time investment required to browse, curate, and coordinate clothing
- Uncertainty about whether trending outfits will suit their body type or style
- Difficulty translating Instagram inspiration into actionable purchases
- Reliance on friends with fashion knowledge for styling advice (not scalable)

**Consequence:**
This results in decision fatigue, repeated purchases of similar items, unused wardrobe pieces, and missed opportunities to express personal style confidently.

**Gap:**
While virtual try-on tools exist and outfit inspiration is abundant, no platform combines curated real-world outfit discovery with personalized virtual try-on and intelligent shopping recommendations in a seamless experience.

---

## Goals and Objectives

### Primary Goal
Create an MVP that validates the core value proposition: users can discover curated Instagram outfits and virtually try them on within 60 seconds.

### Success Metrics (MVP)
- **Engagement:** 70% of users who land on the platform try on at least one outfit
- **Time-to-Value:** Average time from landing to first try-on < 90 seconds
- **Outfit Database:** 200+ diverse, high-quality outfits across categories
- **Technical Performance:** Virtual try-on processing time < 10 seconds
- **User Satisfaction:** 4+ star rating on outfit curation quality

### Objectives by Phase

**MVP (Phase 1):**
- Build functional outfit feed with filtering capability
- Implement AI-powered outfit detection and cataloging from Instagram sources
- Deploy virtual try-on technology with acceptable quality threshold
- Achieve 80% accuracy in outfit piece identification

**MVP 2 (Phase 2):**
- Segment outfits into Leisure, Office, and Evening categories
- Improve personalization based on user preferences
- Reduce virtual try-on latency to <5 seconds

**Proof of Concept (Phase 3):**
- Enable mix-and-match: users combine pieces from multiple outfits
- Introduce "wardrobe" feature for saved combinations

**Final Vision (Phase 4):**
- AI shopping assistant recommends purchase options for outfit pieces
- Price comparison and availability tracking
- One-click purchase flow via affiliate partnerships

---

## User Stories

### Core User Journey (MVP)
1. **As a busy professional**, I want to quickly browse styled outfits so I can find fashion inspiration without spending hours on social media.

2. **As someone unsure about my style**, I want to see how an outfit looks on me before committing, so I can make confident fashion decisions.

3. **As a fashion enthusiast**, I want AI to curate trending outfits from Instagram so I stay current without constant scrolling.

### Enhanced Features (Post-MVP)
4. **As a user planning for different occasions**, I want to filter outfits by category (work, casual, evening) so I can find appropriate looks quickly.

5. **As a creative dresser**, I want to mix pieces from different outfits to create my own unique combinations.

6. **As a practical shopper**, I want to know where to buy items from outfits I like, with price ranges that fit my budget.

7. **As a repeat user**, I want to save my favorite outfits and combinations so I can reference them when shopping or getting dressed.

### Technical Stories
8. **As the system**, I need to identify and extract individual clothing pieces from Instagram images with 80%+ accuracy.

9. **As the system**, I need to process user photos for virtual try-on while preserving privacy and deleting images after processing.

---

## Technical Requirements

### MVP Architecture

**1. The Trend Spotters (Data Collection)**
- AI agents monitor curated Instagram accounts for outfit content
- Image processing pipeline identifies full-body outfit photos where all pieces are clearly visible
- Criteria for selection:
  - All clothing items clearly visible and distinguishable
  - Full-body shot or clear view of complete outfit
  - High image quality (minimum resolution threshold)
  - Diverse representation (body types, styles, occasions)
- Store metadata: source, date discovered, preliminary categorization

**2. The Stylists (Content Processing)**
- Computer vision model segments outfits into individual pieces:
  - Tops (shirts, blouses, sweaters, jackets)
  - Bottoms (pants, skirts, shorts)
  - Footwear
  - Accessories (visible items like hats, bags, jewelry)
- Generate outfit "recipe" with tagged pieces
- Quality assurance: manual review queue for low-confidence segmentations
- Publish approved outfits to user-facing database

**3. The Website (User Interface)**
- **Mobile-first responsive design** with desktop support
- **Outfit Feed:**
  - Infinite scroll with lazy loading
  - Quick filters (MVP2: Leisure/Office/Evening)
  - Save/favorite functionality
- **Virtual Try-On Flow:**
  - Clear CTA on outfit cards
  - Photo upload with guidance (lighting, pose, framing)
  - Processing indicator with estimated wait time
  - Result display with before/after comparison
- **Performance:** <3s initial load, <1s filter transitions

**4. The Photographer (Virtual Try-On)**
- Integration with virtual try-on AI service
- Privacy-first approach:
  - No permanent storage of user photos
  - Processing happens server-side with immediate deletion post-processing
  - Clear privacy messaging and consent
- Output: Realistic visualization of outfit on user's body

**5. The Personal Shopper (Phase 4)**
- Google Lens API for visual search to identify similar items
- Google Shopping API for product matching and price comparison
- Availability tracking across retailers
- Affiliate link management

### Tech Stack

**Frontend:**
- React with Next.js (mobile-first, SEO-friendly, server-side rendering)
- Tailwind CSS for responsive design
- Progressive Web App (PWA) capabilities for app-like experience

**Backend:**
- Node.js with Express or Next.js API routes
- Python microservices for heavy AI/ML processing

**Database & Storage:**
- **Supabase** for:
  - PostgreSQL database (outfit metadata, user profiles, saved looks)
  - Authentication (social login, email/password)
  - Storage (outfit images, temporary user photo uploads)
  - Real-time subscriptions (live feed updates)
  - Row Level Security (RLS) for data privacy

**AI/ML (Google Cloud Platform):**
- **Google Cloud Vision API:**
  - Object detection for identifying clothing items
  - Label detection for outfit categorization
  - Image properties for quality assessment
  
- **Google Vertex AI:**
  - Custom trained models for outfit segmentation
  - Fashion-specific object detection fine-tuning
  - Embedding generation for outfit similarity

- **Virtual Try-On Options:**
  - Custom model hosted on Vertex AI (Stable Diffusion fine-tuned)
  - Google MediaPipe for pose detection and body segmentation
  - Alternative: Third-party API with Google Cloud Run deployment

- **Google Lens API:**
  - Visual product search for shopping recommendations
  - Similar item discovery across web

- **Google Shopping API:**
  - Product matching and availability
  - Price comparison
  - Merchant integration

**Instagram Content Collection:**
- Manual curation to start (avoid ToS violations)
- Future: Apify with proper permissions or influencer partnerships
- Google Cloud Storage for image pipeline

**Infrastructure:**
- **Hosting:** Vercel (Next.js) or Google Cloud Run
- **CDN:** Vercel Edge Network or Google Cloud CDN
- **Monitoring:** Google Cloud Monitoring & Logging
- **CI/CD:** GitHub Actions with Google Cloud Build

### Data Schema (Supabase/PostgreSQL)

```sql
-- Core tables
outfits (
  id uuid PRIMARY KEY,
  source_url text,
  image_url text,
  category text, -- leisure, office, evening
  created_at timestamp,
  metadata jsonb -- season, style tags, color palette
)

outfit_pieces (
  id uuid PRIMARY KEY,
  outfit_id uuid REFERENCES outfits,
  piece_type text, -- top, bottom, footwear, accessory
  bounding_box jsonb,
  labels text[],
  confidence float
)

users (
  id uuid PRIMARY KEY,
  email text UNIQUE,
  created_at timestamp,
  preferences jsonb -- style preferences, sizes
)

saved_outfits (
  user_id uuid REFERENCES users,
  outfit_id uuid REFERENCES outfits,
  saved_at timestamp,
  PRIMARY KEY (user_id, outfit_id)
)

try_on_history (
  id uuid PRIMARY KEY,
  user_id uuid REFERENCES users,
  outfit_id uuid REFERENCES outfits,
  result_url text, -- temporary, expires after 24h
  created_at timestamp
)
```

---

## User Experience Flow

### First-Time User Flow
1. **Landing:** Value proposition + example before/after
2. **Browse:** Immediately show outfit feed (no signup wall)
3. **Select:** Tap outfit → See details + "Try It On" CTA
4. **Upload:** Guided photo upload with tips
5. **Process:** Fun loading state (fashion tips, style facts)
6. **Result:** Side-by-side comparison, share/save options
7. **Soft conversion:** "Save this look?" → Optional signup

### Design Principles (Based on Reference Images)
- **Minimalist aesthetic:** Clean whites, soft pastels, ample whitespace
- **Card-based layouts:** Easy thumb-reach tap targets
- **Personalization:** "Good Morning, [Name]" + contextual recommendations
- **Visual hierarchy:** Large imagery, readable typography
- **Microinteractions:** Smooth transitions, delightful loading states

---

## Benefits

### For Users
- **Time Savings:** Reduces styling research from hours to minutes
- **Confidence:** Virtual try-on eliminates "will this suit me?" uncertainty
- **Inspiration:** Curated feed provides constant fresh ideas without algorithm fatigue
- **Accessibility:** Democratizes access to styling expertise
- **Actionability:** Direct path from inspiration to purchase

### For the Business
- **Differentiation:** Unique curation source (Instagram real-world outfits) vs. stock photography
- **Engagement:** Virtual try-on creates sticky, shareable experiences
- **Data:** User preferences and try-on data inform future features
- **Scalability:** AI-driven curation reduces manual overhead
- **Network Effects:** User-generated combinations expand outfit database organically

### Competitive Advantages Over Existing Solutions

| Competitor | Drapr Advantage |
|------------|-----------------|
| **Combyne/Cladwell** | Real Instagram outfits vs. digital collages; actual try-on vs. mix-board |
| **Retailer Virtual Try-On** | Cross-brand outfit discovery; inspiration before shopping intent |
| **Pinterest/Instagram** | Actionable try-on + shopping; personalized to user's appearance |
| **Personal Stylists** | Instant access; scalable; no hourly fees |

---

## Risks and Mitigations

### Legal & Ethical
- **Risk:** Instagram content scraping violates ToS
  - **Mitigation:** Pivot to user-generated content, influencer partnerships, or licensing agreements; start with manual curation of Creative Commons content

- **Risk:** Privacy concerns with photo uploads
  - **Mitigation:** Transparent data handling, immediate deletion policy (Supabase Storage with TTL), optional on-device processing (future)

### Technical
- **Risk:** Virtual try-on quality doesn't meet expectations
  - **Mitigation:** Set user expectations; continuous model improvement; A/B test providers; leverage Google Vertex AI for custom model training

- **Risk:** Outfit segmentation accuracy <80%
  - **Mitigation:** Human-in-the-loop validation; start with simpler outfits; fine-tune Google Cloud Vision models over time

- **Risk:** Supabase Storage costs for high-volume image uploads
  - **Mitigation:** Implement aggressive cleanup policies; compress images; use CDN caching; monitor usage closely

### Product-Market Fit
- **Risk:** Users prefer manual styling over AI recommendations
  - **Mitigation:** Hybrid approach; allow users to edit/customize; gather feedback loops

---

## Implementation Phases

### Phase 1: MVP (Weeks 1-8)
**Week 1-2: Setup & Infrastructure**
- Initialize Next.js project with Tailwind CSS
- Set up Supabase project (database, auth, storage)
- Configure Google Cloud Platform project
- Set up Google Cloud Vision API
- Design database schema and implement migrations

**Week 3-4: Outfit Curation Pipeline**
- Manually curate 50 initial outfits from Creative Commons sources
- Build admin interface for outfit upload and tagging
- Implement Google Cloud Vision API integration for auto-tagging
- Create outfit feed UI component

**Week 5-6: Virtual Try-On**
- Research and select virtual try-on solution (Vertex AI or third-party)
- Build photo upload flow with guidance
- Implement processing pipeline with Google Cloud
- Create result display UI

**Week 7-8: Polish & Testing**
- Implement Supabase auth (Google, email)
- Build saved outfits feature
- Performance optimization
- User testing with 20-30 beta users
- Bug fixes and refinements

**MVP Launch Criteria:**
- 200 curated outfits
- <10s try-on processing
- Mobile-responsive on iOS and Android
- Working save/favorite functionality

### Phase 2: MVP 2 (Weeks 9-12)
- Implement category filtering (Leisure/Office/Evening)
- Expand outfit database to 500+
- Add user preference settings
- Improve try-on quality based on feedback
- Implement basic analytics (Google Analytics 4)

### Phase 3: POC (Weeks 13-16)
- Mix-and-match piece selection
- Wardrobe management system
- Advanced filtering (color, style, season)
- Social sharing features

### Phase 4: Shopping Integration (Weeks 17-20)
- Google Lens API integration for product matching
- Google Shopping API for price comparison
- Affiliate program setup
- Purchase flow optimization

---

## Success Metrics & KPIs

### Engagement Metrics
- Daily Active Users (DAU) / Monthly Active Users (MAU)
- Try-on conversion rate (% of visitors who try on)
- Average outfits viewed per session
- Time spent in app
- Return visitor rate

### Product Metrics
- Try-on completion rate
- Try-on satisfaction rating (post-result survey)
- Outfit save rate
- Share/referral rate

### Technical Metrics
- Try-on processing latency (p50, p95, p99)
- API error rates (Google Cloud Vision, virtual try-on)
- Page load time (Core Web Vitals)
- Supabase query performance

### Business Metrics (Post-MVP)
- Affiliate click-through rate
- Purchase conversion rate
- Average order value from referrals
- Customer acquisition cost (CAC)

---

## Future Considerations (Post-MVP)

1. **Wardrobe Management:** Users upload their own clothes; AI suggests combinations using Google Cloud Vision
2. **Social Features:** Share outfits, follow friends, community voting
3. **AR Try-On:** Real-time mobile camera integration using Google MediaPipe
4. **Personalization Engine:** ML-driven recommendations based on try-on history (Vertex AI)
5. **Sustainability:** Suggest rental or secondhand options alongside new purchases
6. **Influencer Partnerships:** Verified creator accounts with exclusive collections
7. **Body Positivity:** Diverse model representation in curation algorithm
8. **Mobile Apps:** Native iOS/Android apps for better camera integration
9. **Voice Stylist:** Google Cloud Speech-to-Text for conversational styling
10. **Google Gemini Integration:** Natural language styling advice and outfit explanations

---

## Conclusion

Drapr addresses a genuine pain point for individuals who want to look stylish but lack the time or expertise to curate outfits confidently. By combining AI-powered Instagram curation with virtual try-on technology, the platform transforms passive scrolling into actionable fashion decisions.

The MVP focuses on validating the core hypothesis: **Can we deliver a delightful "discover → visualize → decide" experience in under 90 seconds?** Success here unlocks subsequent phases that add personalization, mix-and-match creativity, and seamless shopping.

**Technology Choice Rationale:**
- **Supabase** provides a complete backend-as-a-service with auth, database, and storage in one platform, accelerating development
- **Google Cloud AI/ML** offers enterprise-grade computer vision and ML capabilities with excellent fashion-specific models and seamless integration
- **Next.js + Vercel** enables rapid iteration with excellent mobile performance out of the box

**Recommendation:** Begin with a tightly scoped MVP focusing on 200 manually curated outfits (to avoid Instagram legal issues initially) and Google Cloud Vision for outfit analysis. Test virtual try-on quality early with multiple providers before committing. Measure engagement metrics religiously and iterate based on user feedback before expanding to automated curation and additional features.

The mobile-first, web-based approach minimizes development complexity while maximizing accessibility, making Drapr an ideal showcase of modern product strategy: solve one problem exceptionally well, then expand.

---

## Appendix: Open Questions for Stakeholder Alignment

1. What is the acceptable latency for virtual try-on processing? (Current target: <10s MVP, <5s MVP2)
2. Should we prioritize outfit diversity or style consistency in curation?
3. What is the manual curation budget for MVP? (Target: 200 outfits)
4. Are there preferred influencer accounts to target for partnerships?
5. What is the acceptable cost per try-on for Google Cloud services?
6. Should we implement Google Analytics 4 from day one or wait until Phase 2?
7. What privacy compliance requirements must we meet (GDPR, CCPA)?

---

## Resources & References

### Design Inspiration
- Reference images provided showcase modern fashion app UX patterns
- Key themes: Minimalism, card-based layouts, personalization, large imagery

### Technical Documentation
- [Supabase Documentation](https://supabase.com/docs)
- [Google Cloud Vision API](https://cloud.google.com/vision/docs)
- [Google Vertex AI](https://cloud.google.com/vertex-ai/docs)
- [Google Lens API](https://developers.google.com/lens)
- [Next.js Documentation](https://nextjs.org/docs)

### Competitive Analysis Tools
- SimilarWeb for traffic analysis
- App Annie for mobile app metrics
- Product Hunt for emerging competitors

---

**Document Version:** 1.0  
**Last Updated:** January 2026  
**Owner:** Product Team  
**Status:** Draft for Review
