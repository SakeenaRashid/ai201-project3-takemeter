# TakeMeter | Planning Doc

## 👥 Community Choice

**Community:** r/Coffee ☕ (Reddit)

r/Coffee is an active, text-heavy community where discourse ranges from equipment recommendations and brewing technique debates to sensory descriptions and emotional reactions. The distinctions between these post types are meaningful to community members — a beginner needs gear advice, an enthusiast wants technique discussion, and both are different from someone simply sharing a great cup.

---

## 🏷️ Label Taxonomy

### Labels

| Label | Definition |
|---|---|
| `gear` | Post focused on equipment — recommendations, comparisons, purchases, or questions about specific tools |
| `brewing_methods` | Post focused on process, technique, or variables — grind size, ratios, water temp, extraction |
| `good_coffee` | Post describing the taste, smell, or experience of a specific coffee, with no specific gear mentioned |
| `coffee_rants` | Emotional reaction — venting frustration or excitement without substantive content |

### Examples per Label

**gear**
1. "Just picked up a Timemore C3 — anyone else think it punches way above its price point?"
2. "Is the Breville Barista Express worth it for a beginner or should I buy the grinder and machine separately?"

**brewing_methods**
1. "I dropped my water temp from 96°C to 91°C and my pour over is so much less bitter — ratio stayed at 1:16."
2. "Anyone else do a longer pre-infusion on their espresso? I've been doing 8 seconds and it's changed everything."

**good_coffee**
1. "Had a Kenyan natural from my local roaster this morning — stone fruit and blackberry, incredibly clean finish."
2. "First time trying a properly dialed-in cortado. I don't think I can go back to drip."

**coffee_rants**
1. "I cannot believe I wasted $40 on beans from that subscription service. Stale and flavorless. Never again."
2. "Why do coffee shops charge $7 for a latte and then hand it to you lukewarm??"

---

## 🤔 Edge Case & Decision Rule

**Edge case:** A post mentions a specific piece of gear AND describes a positive coffee experience.

*Example: "I just got a new Comandante grinder and my pour over has never tasted better!"*

This post could reasonably belong to `gear` or `good_coffee`.

**Decision rule:** If gear is mentioned anywhere in the post, label it `gear`. Equipment recommendations have unique practical value to the community regardless of experiential framing. `good_coffee` is reserved for posts where no specific gear is referenced.

---

## ❓Why These Distinctions Matter

These four labels reflect how r/Coffee members actually talk and what they come to the community for. Someone searching for equipment advice needs `gear` posts. Someone troubleshooting a bad brew needs `brewing_methods`. Someone exploring new beans wants `good_coffee`. And `coffee_rants` captures the emotional noise that's real but distinct from substantive discourse. A classifier that can separate these has genuine utility for community navigation.

---

## 🙅‍♀️ Stretch Features Planned

None. Required features only.
