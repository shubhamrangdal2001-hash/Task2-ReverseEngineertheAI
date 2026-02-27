Basic Calculator App — 6-Layer Architectural Teardown
Stress test: Does the framework know when to say "this layer is minimal"?

Pre-Analysis: Chain-of-Thought
Core value proposition: Parse a mathematical expression entered by a user, evaluate it correctly, display the result. That's it.
Which layer creates unique value? None of the 6 layers in the ML/AI sense. This is a deterministic arithmetic engine. No training data, no models, no LLM, no scale challenge.
Hardest technical constraint: Floating point precision errors (0.1 + 0.2 ≠ 0.3 in IEEE 754). That's genuinely the hardest problem in this product. It's a 1970s computer science problem.
Counterintuitive insight: There isn't one. This product is as simple as it looks. The framework should reflect that honestly.

Layer 1: Data Foundation
What's happening: There is no data pipeline. User input is a string expression ("42 × 7") parsed in memory, evaluated, and discarded. No persistence, no collection, no storage. If the app has a calculation history feature, it writes to local device storage (SQLite on mobile, localStorage on web) — no backend required.
Key technologies: SQLite (local history, if implemented), localStorage/IndexedDB (web version), no cloud storage layer required.
Engineering challenge: None at this layer. Expression parsing edge cases (malformed input, division by zero, overflow) are handled by client-side validation logic, not data infrastructure. This is a solved problem with off-the-shelf parsing libraries.
Skill required: "Basic mobile or web development; familiarity with local storage APIs."
Honesty check: MINIMAL — there is no data foundation in any meaningful engineering sense. A calculator does not collect, clean, store, or pipeline data. Rating it anything higher would be dishonest.

Layer 2: Statistics & Analysis
What's happening: A commercial calculator app (iOS/Android) may run basic product analytics — DAU/MAU, session length, feature usage (did users use the scientific mode?). This is instrumentation, not a statistical system. No hypothesis testing, no A/B experimentation framework needed unless the team is testing UI layouts.
Key technologies: Possibly Firebase Analytics or Mixpanel for usage telemetry (1–2 events per session), no experimentation platform needed at typical calculator app scale.
Engineering challenge: There is no meaningful statistical engineering challenge in a calculator app. If the team is mature enough to A/B test button color, they're using off-the-shelf tools that require zero custom engineering.
Skill required: "Basic product analytics instrumentation."
Honesty check: MINIMAL — analytics exist at the product level but require no custom statistical infrastructure. This layer could be removed entirely with zero impact on the core product.

Layer 3: Machine Learning Models
What's happening: None. A calculator is a deterministic rule-based system. Expression evaluation is not a prediction problem — it has a correct answer computable by an algorithm, not a model. There is no training, no inference, no ranking, no classification.
Key technologies: None applicable. A recursive descent parser or the Shunting-yard algorithm handles expression evaluation. This is a CS101 data structures problem, not an ML problem.
Engineering challenge: IEEE 754 floating point representation causes precision errors in decimal arithmetic. The canonical fix is using arbitrary-precision arithmetic libraries (e.g., BigDecimal in Java/Kotlin, decimal module in Python) rather than native float types — but this is a language-level decision, not an ML engineering challenge.
Skill required: "Basic algorithmic programming; familiarity with floating point arithmetic."
Honesty check: MINIMAL — this layer is genuinely inapplicable. Any teardown that invents ML complexity here (e.g., "uses NLP to parse natural language expressions") is fabricating scope that doesn't exist in a standard calculator.

Layer 4: LLM / Generative AI
What's happening: Not applicable for a standard calculator. The one exception: a "natural language calculator" (e.g., "what's 15% tip on ₹850?") — this would use a small LLM or a rule-based NLU layer to parse intent before handing off to the arithmetic engine. But this is a feature extension, not the core product.
Key technologies: If natural language input exists: possibly on-device ML Kit (Google) or Core ML (Apple) for lightweight intent classification. Otherwise, none.
Engineering challenge: For standard calculator: none. For NL extension: mapping ambiguous natural language ("split this equally") to deterministic arithmetic operations without hallucinating the calculation itself — the LLM must parse intent, not do the math, because LLMs are unreliable arithmetic engines.
Skill required: "Not required for standard calculator. NLU intent classification experience if building conversational math features."
Honesty check: MINIMAL — LLMs are not involved in a standard calculator and shouldn't be. If a teardown rates this layer highly, the framework is over-applying AI framing to a non-AI product.

Layer 5: Deployment & Infrastructure
What's happening: A calculator app is a client-side application — it runs entirely on the user's device. There is no server, no backend, no API to call for computation. Deployment is publishing to the App Store or Google Play. Infrastructure is the device's CPU evaluating arithmetic operations locally.
Key technologies: Xcode + Swift/SwiftUI (iOS), Android Studio + Kotlin (Android), or React Native / Flutter for cross-platform. App Store Connect / Google Play Console for distribution. No cloud infrastructure required.
Engineering challenge: App Store review compliance and OS version compatibility — not a systems engineering challenge. Ensuring the app doesn't crash on edge inputs (e.g., computing factorial of a very large number) requires defensive coding, not infrastructure design.
Skill required: "Mobile app development; App Store / Play Store submission process."
Honesty check: MINIMAL — there is no backend infrastructure to deploy or operate. This layer is essentially inapplicable. "24/7 uptime" for a calculator means the device stays on.

Layer 6: System Design & Scale
What's happening: There is no distributed system to design. Each user's calculator operates independently on their device. "Scale" means "many devices running the same app simultaneously" — which requires zero coordination between users because each session is fully isolated. The only shared infrastructure is the app distribution platform (App Store), which the developer doesn't build.
Key technologies: None required beyond CDN delivery of the app binary (handled by App Store/Play Store infrastructure, not the developer).
Engineering challenge: None in the traditional sense. If the calculator app has a cloud sync feature (history across devices), then a lightweight backend (e.g., Firebase Realtime Database or Supabase) is needed — but this is a 1-engineer weekend project, not a distributed systems challenge.
Skill required: "Not required beyond standard mobile development."
Honesty check: MINIMAL — this layer is entirely inapplicable to a client-side, stateless, single-user application. Applying distributed systems thinking here would be architectural theater.

Overall Analysis
Most Critical Layer: None of the 6 AI/ML layers — the core engineering challenge in a calculator is correct arithmetic evaluation, which belongs to classical computer science (parsing algorithms, floating point representation), not any of the 6 layers this framework is designed to analyze.
Complexity Rating: Simple — a calculator is a deterministic, client-side, stateless application with no ML, no LLM, no distributed state, no data pipeline, and no scale challenge. The entire product can be built by one engineer in a week. Applying "Advanced" or "Moderate" ratings to any layer would be framework hallucination.
If rebuilding from scratch, first thing to get right: Floating point representation choice — decide upfront whether to use native float arithmetic (fast, slightly imprecise) or arbitrary-precision decimal arithmetic (precise, marginally slower). Every user who types 0.1 + 0.2 and sees 0.30000000000000004 files a 1-star review. This is the only technical decision with a meaningful user-visible consequence.

Confidence: 95% — calculators are fully understood, fully public technology. The only uncertainty is whether a specific app has added cloud sync or NL features, which would modestly elevate Layers 4–6.

What This Stress Test Actually Proves About the V2 Prompt
Here's the meta-analysis — what the two stress tests reveal together:

Dimension	                    PhonePe Fraud               	Basic Calculator
Layer 4 (LLM) rating	          MINIMAL                     	MINIMAL
Most critical layer         	Layer 3 (ML Models)	          None of the 6
Teardown density	                ~2,800 words	             ~1,100 words
Numbers in challenges	        Yes (5,000 TPS, <200ms)	    Yes (0.1 + 0.2 edge case)
"First thing" counterintuitive	✅ Feature store architecture	✅ Float representation choice
Framework honesty              	Flagged RBI constraints	      Flagged inapplicability

The prompt discriminates correctly on three dimensions:
Density scales with complexity. The calculator teardown is ~40% the length of PhonePe's. The framework didn't pad thin layers with invented complexity.
MINIMAL ratings appear when warranted. Four of six layers rated MINIMAL for the calculator. The prompt's honesty check instruction did its job — it forced an honest "this doesn't apply" rather than fabricating ML complexity in a rule-based system.
The "counterintuitive insight" rule still fires on simple products. Even for a calculator, the framework surfaced a non-obvious first principle (floating point choice) rather than defaulting to "make it fast" or "good UI." The rule generalizes.
One weakness surfaced: The "Overall Analysis" section was designed for AI products and felt slightly strained when applied to a non-AI product — the "Most Critical Layer" answer being "none of the 6" is honest but breaks the template. A V3 improvement would add an applicability gate: "If fewer than 3 layers are HEAVILY USED or above, output a brief 'Not an AI product' summary instead of a full teardown."

