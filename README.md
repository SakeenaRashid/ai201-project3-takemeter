# TakeMeter ☕

A fine-tuned text classifier that evaluates discourse quality in r/Coffee on Reddit. TakeMeter categorizes posts and comments into four labels that reflect how the community actually talks about coffee.

---

## Community Choice

**Community:** r/Coffee (Reddit)

r/Coffee is an active, text-heavy community where discourse ranges from equipment recommendations and brewing technique debates to sensory descriptions and emotional reactions. These distinctions are meaningful to community members — a beginner needs gear advice, an enthusiast wants technique discussion, and both are different from someone sharing a great cup or venting about the espresso rabbit hole. The variety and volume of posts made it a strong fit for a classification task.

---

## Label Taxonomy

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
1. "I dropped my water temp from 96C to 91C and my pour over is so much less bitter — ratio stayed at 1:16."
2. "Anyone else do a longer pre-infusion on their espresso? I've been doing 8 seconds and it's changed everything."

**good_coffee**
1. "Had a Kenyan natural from my local roaster this morning — stone fruit and blackberry, incredibly clean finish."
2. "First time trying a properly dialed-in cortado. I don't think I can go back to drip."

**coffee_rants**
1. "I cannot believe I wasted $40 on beans from that subscription service. Stale and flavorless. Never again."
2. "Why do coffee shops charge $7 for a latte and then hand it to you lukewarm??"

---

## Data Collection

**Source:** r/Coffee on Reddit, collected manually via browser console script across multiple threads.

**Threads used:**
- Moka pot rice cooker hack thread (brewing_methods)
- V60 ratio discussion thread (brewing_methods)
- Gaggia Classic recommendation thread (gear)
- "Help, can't make good coffee at home" thread (gear + brewing_methods + coffee_rants)
- Variables that make good coffee thread (good_coffee)
- Tasting notes and flavor perception thread (good_coffee)
- Best coffee you've ever had thread (good_coffee)
- Espresso rabbit hole thread (coffee_rants)

**Labeling process:** Each post was read individually and assigned one label based on definitions in planning.md. The edge case decision rule (gear takes priority over good_coffee when equipment is mentioned) was applied consistently throughout.

**Label distribution:**

| Label | Count |
|---|---|
| good_coffee | 62 |
| coffee_rants | 49 |
| brewing_methods | 47 |
| gear | 42 |
| **Total** | **200** |

### Difficult-to-Label Examples

**1. Gear + good_coffee overlap**
"I just got a new Comandante grinder and my pour over has never tasted better!"
Could be gear (mentions a specific grinder) or good_coffee (positive coffee experience). Decision: labeled gear — equipment recommendations have unique practical value to the community regardless of experiential framing. good_coffee is reserved for posts where no specific gear is referenced.

**2. Gear + brewing_methods overlap**
"If you don't have one already get a good grinder. The Timemore C2 and 1zpresso JX have been total game changers for me paired with my V60. Been making some of the best coffee I've ever had."
Could be gear (specific grinders named) or good_coffee (describes great coffee experience). Decision: labeled gear — specific equipment is named and recommended.

**3. Brewing_methods + good_coffee overlap**
"I accidentally used 1:18 this morning vs. 1:16 that I normally do and I noticed a significant reduction in the bitter/sour notes, and it was a more pleasant experience overall."
Could be brewing_methods (ratio discussion) or good_coffee (describing a pleasant experience). Decision: labeled brewing_methods — the post explains a technique variable and its effect, not primarily a sensory description.

---

## Fine-Tuning Approach

**Base model:** `distilbert-base-uncased` from HuggingFace

**Training setup:** Google Colab free tier with T4 GPU. Libraries used: transformers, datasets, scikit-learn.

**Train/val/test split:** 70% / 15% / 15% (140 train, 30 val, 30 test), stratified by label.

**Hyperparameter decision:** The default number of training epochs was 3, which produced only 40% validation accuracy. This was increased to 6 epochs, which improved validation accuracy to 80%. With only 200 examples and 4 labels, the model needed more passes through the data to learn meaningful boundaries. All other hyperparameters were kept at defaults: learning rate 2e-5, batch size 16, weight decay 0.01.

---

## Baseline

**Model:** Groq `llama-3.3-70b-versatile`, zero-shot (no task-specific training)

**Prompt used:**

```
You are classifying posts from r/Coffee on Reddit.
Assign each post to exactly one of the following categories.

gear: The post focuses on equipment — recommendations, comparisons, purchases, or questions about specific tools like grinders or espresso machines.
Example: "Just picked up a Timemore C3 — anyone else think it punches above its price point?"

brewing_methods: The post focuses on process, technique, or variables — grind size, ratios, water temperature, or extraction.
Example: "Dropped my water temp to 91C and my pour over is so much less bitter — ratio stayed at 1:16."

good_coffee: The post describes the taste, smell, or experience of a specific coffee, with no specific gear mentioned.
Example: "Had a Kenyan natural this morning — stone fruit and blackberry, incredibly clean finish."

coffee_rants: An emotional reaction — venting frustration or excitement without substantive content.
Example: "Why do coffee shops charge $7 for a latte and then hand it to you lukewarm??"

Respond with ONLY the label name. Do not explain your reasoning.

Valid labels:
gear
brewing_methods
good_coffee
coffee_rants
```

**How results were collected:** The prompt was run on every example in the locked test set (30 examples) via the notebook's Section 5 baseline loop. All 30 responses were parseable.

---

## Evaluation Report 📊

### Overall Accuracy

| Model | Accuracy |
|---|---|
| Zero-shot baseline (Groq) | 0.733 |
| Fine-tuned DistilBERT | 0.667 |
| Difference | -0.067 (regression) |

### Per-Class Metrics

**Baseline (Groq zero-shot)**

| Label | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| brewing_methods | 0.50 | 0.43 | 0.46 | 7 |
| gear | 0.60 | 0.86 | 0.71 | 7 |
| good_coffee | 0.88 | 0.78 | 0.82 | 9 |
| coffee_rants | 1.00 | 0.86 | 0.92 | 7 |
| **accuracy** | | | **0.733** | 30 |

**Fine-tuned DistilBERT**

| Label | Precision | Recall | F1 | Support |
|---|---|---|---|---|
| brewing_methods | 0.80 | 0.57 | 0.67 | 7 |
| gear | 0.67 | 0.57 | 0.62 | 7 |
| good_coffee | 0.67 | 0.89 | 0.76 | 9 |
| coffee_rants | 0.57 | 0.57 | 0.57 | 7 |
| **accuracy** | | | **0.667** | 30 |

### Confusion Matrix (Fine-Tuned Model)

Rows = true label, Columns = predicted label.

| | brewing_methods | gear | good_coffee | coffee_rants |
|---|---|---|---|---|
| **brewing_methods** | 4 | 0 | 2 | 1 |
| **gear** | 1 | 4 | 1 | 1 |
| **good_coffee** | 0 | 0 | 8 | 1 |
| **coffee_rants** | 0 | 2 | 1 | 4 |

### Wrong Predictions Analysis 🔍

**Error 1**
Text: "The coffee was not very good to me. It was not bitter but just harsh and had no sweetness at all - and this was a coffee that at 18:1 brews was sugar syrupy sweet with nice, tart, fruity notes."
True label: brewing_methods | Predicted: good_coffee (confidence: 0.58)

Analysis: This post uses sensory language ("sweetness," "tart," "fruity") that strongly overlaps with good_coffee. The model latched onto the flavor vocabulary and missed that the post is analyzing the effect of a brewing ratio on taste. This is a labeling boundary problem — the definition of brewing_methods didn't explicitly state that posts describing taste outcomes of technique decisions belong to brewing_methods. More training examples showing this pattern would help.

**Error 2**
Text: "Lol I do this everyday just with a pan underneath since the coffee maker doesnt work with induction."
True label: brewing_methods | Predicted: coffee_rants (confidence: 0.41)

Analysis: Without thread context, this short comment reads as casual and reactive — exactly the tone of coffee_rants. The model can't see that it's a reply about an improvised heating method. Short, context-dependent posts are a systematic weakness: the model relies on keywords and can't recover when the post's meaning depends on surrounding conversation.

**Error 3**
Text: "I got the cheaper and simple flair pro 2 and I'm already obsessed with it, can't imagine how hooked I'd be if I got an expensive one."
True label: coffee_rants | Predicted: gear (confidence: 0.46)

Analysis: This post mentions a specific piece of equipment (Flair Pro 2) which pulls it toward gear. But the post's primary content is emotional — excitement and the feeling of being hooked — which makes it coffee_rants by our definition. This reveals a gap in our edge case rules: we defined that gear beats good_coffee when equipment is mentioned, but we didn't define how to handle gear vs. coffee_rants when emotional language dominates.

### Sample Classifications 🧪

| Post (truncated) | Predicted Label | Confidence |
|---|---|---|
| "Just installed the Auber PID kit last week! Fun project, probably 5 hours in total." | gear | 0.71 |
| "Had a Kenyan natural this morning — stone fruit and blackberry, incredibly clean finish." | good_coffee | 0.84 |
| "I do 15:1 and use a digital scale. It is definitely always in the grind." | brewing_methods | 0.63 |
| "Get ready for relatives to gift you extremely large amounts of really crappy coffee on holidays." | coffee_rants | 0.69 |
| "Gaggia is probably the best bang for your buck at that price point in North America." | gear | 0.77 |

The gear prediction for "Just installed the Auber PID kit last week! Fun project, probably 5 hours in total" is reasonable — the post is about installing a specific piece of equipment (a PID controller) and describes the experience of doing so, with no sensory coffee description and no rant tone. The model correctly identified the equipment focus.

---

## Reflection: What the Model Learned vs. What We Intended 💭

The model learned to associate sensory and flavor vocabulary ("sweet," "fruity," "tart," "notes") with good_coffee, emotional exclamations and frustration language with coffee_rants, and brand/product names with gear. These surface-level signals are real but incomplete.

What it didn't learn is the structural intent of a post. A brewing_methods post that describes the taste outcome of a ratio experiment uses the same flavor vocabulary as a good_coffee post — but the intent is analytical, not experiential. The model couldn't distinguish these because the training data didn't have enough examples showing that pattern explicitly, and because the boundary between "describing what coffee tastes like as part of a technique discussion" vs. "describing what coffee tastes like as an experience" is genuinely subtle.

The fine-tuned model also underperformed the zero-shot baseline overall (66.7% vs 73.3%). This is likely a combination of dataset size (200 examples is small for four-class fine-tuning), label overlap (the four categories share vocabulary), and the model needing more diverse examples at the hard boundaries to generalize. The zero-shot baseline had an advantage: a large language model's general knowledge of coffee discourse helped it classify coffee_rants and good_coffee well without any task-specific training.

---

## Spec Reflection

**One way the spec helped:** The requirement to identify a hard edge case before annotating was the most valuable design step. Writing the gear-vs-good_coffee decision rule before labeling 200 examples prevented a large category of inconsistent labels and gave me a concrete rule to apply whenever a post mentioned equipment alongside a positive coffee experience.

**One way implementation diverged:** The spec suggests collecting data before labeling, ideally reading 30-40 examples first to check that labels apply cleanly. In practice, data collection and label refinement happened somewhat simultaneously — the labels were adjusted slightly as new thread types were encountered. This was a pragmatic tradeoff given time constraints and didn't introduce major inconsistencies, but it meant the label definitions evolved during collection rather than being fully locked before annotation began.

---

## AI Usage 🤖

**Instance 1: Dataset collection and formatting**
I directed Claude to write a JavaScript browser console script to extract comments from Reddit threads, and a Python script (format_dataset.py) to clean and format the collected text into a properly structured CSV. Claude produced both scripts. I overrode Claude's initial suggestion to use the `data-testid="comment"` selector (which returned 0 results on Reddit's current layout) and iterated to a working `p` tag selector. I also directed which label to assign to each thread rather than letting Claude make those decisions.

**Instance 2: Pattern analysis on wrong predictions**
Before writing the evaluation report, I pasted all 10 wrong predictions into Claude and asked it to identify common themes. Claude identified four patterns: sensory language pulling brewing_methods posts toward good_coffee, low confidence scores across most errors, short context-dependent posts misfiring, and equipment mentions pulling coffee_rants posts toward gear. I verified all four patterns by re-reading the examples myself and used three of them as the basis for my wrong prediction analysis. I discarded one suggested pattern (that post length was a primary driver) as I didn't find it consistent across errors.

**Instance 3: README and planning.md drafting**
I directed Claude to write both planning.md and this README using the project details, results, and analysis I provided. I reviewed all content for accuracy, corrected the label examples to match what was actually in my dataset, and added the spec reflection and AI usage sections myself.

**Annotation disclosure:** All 200 examples were labeled by me without LLM pre-labeling. The label assignments reflect my own judgment applied to each post individually.

---

## Dataset

The labeled dataset is included in this repository as `takemeter_dataset.csv`. It contains 200 examples with `text` and `label` columns, collected from public r/Coffee threads.
