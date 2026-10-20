# Decisions

Ten of the 175 decisions recorded while building MenuVision. I made or approved each one.

## 1. Hold the launch until the allergy answers are right
*September 2026*

- **Situation:** feature-complete and in TestFlight. On September 20 an independent audit returned NO-GO. The app sometimes read a dish's typical recipe as fact, and it couldn't tell "the menu says so" from "this dish usually has it" from "unknown." In my testing, it caught about 95-96% of allergens.
- **Decision:** don't launch on "pretty good." I set two bars for my testing: catch over 99% of allergens, and keep false alarms under 5%. They're uneven on purpose. A false alarm costs a question to the server; a miss can cost far more. Submission stays closed until both are met.
- **Rejected:** shipping and patching after launch.
- **Result:** two more weeks of fix rounds, each followed by a fresh audit. Misses like soy hiding in "Soy-Free Noodles with Teriyaki Chicken," or a custom pineapple allergy missed on "Salsa + Pineapple," were fixed as a class and locked in with tests. Both bars cleared in my testing, with no known open misses.

## 2. "Contains" only when the menu says so
*September 2026*

- **Situation:** every allergy line read as fact. "Sometimes made with shrimp" became "Contains shellfish."
- **Decision:** "Contains" only when the menu itself names the ingredient. Everything inferred (a dish's usual recipe, a sauce's typical base, a cached definition) says "May contain." A line never claims more certainty than the evidence supports.
- **Why:** an allergy app that sounds certain about a guess loses trust the first time it's wrong. One that sounds unsure about everything gets ignored.
- **Rejected:** a rewrite. I approved a small add-on that can only soften "contains" to "may contain" or add a hint, so nothing stored changes.
- **Result:** the rule runs from the warning text to the ingredient chips and popups. A "May contain" chip has a dashed outline and says so to VoiceOver. A regression run over 208 real saved menus found 0 lost warnings.

## 3. When it's wrong, be wrong in the safe direction
*September 2026*

- **Situation:** a fix removed false alarms on cake-flavored cocktails ("Birthday Cake Hard Seltzer" warning about wheat). An audit found it also created missed warnings on real desserts.
- **Decision:** revert it the same day and keep the false alarms until a fix exists that can't cause a miss.
- **Rejected:** keeping the fix.
- **Also:** the launch bars in decision 1 put numbers on this rule. And peanuts are never removed by a guessing rule. A spurious "may contain" costs the user a question to the server. A miss can cost far more.

## 4. Safe defaults, real control, one floor
*July 2026*

- **Situation:** users can tailor what they see. In the original design, the "Show possible (assumed) warnings" switch hid inferred allergy warnings too. An audit showed that a terse menu line like "Dragon Roll, $16" could then look safe to someone with a shellfish allergy.
- **Decision:** that switch only narrows diet and dislike warnings to ingredients printed on the menu. Allergy warnings always include likely ingredients. The only way to hide them is the "Show allergy warnings" switch, which says exactly what it does. Every warning type is on by default, and the settings say plainly that a missing warning doesn't mean a dish is safe.
- **Rejected:** the original design.

## 5. One rule for allergies, diets and dislikes
*September 2026*

- **Decision:** a dish offered "with chicken or shrimp" is a choice, not a fact. The same option and certainty rules apply to all three. A vegetarian sees "Chicken may not be Vegetarian," not "is not."
- **Why:** if allergy lines say "may" and diet lines say "is" for the same evidence, users stop trusting both.
- **Rejected:** the lead engineer's recommendation to push diet and dislike "may contain" hints past launch. I included them.

## 6. Say the user's word
*September 2026*

- **Situation:** testing a menu with a "vodka" dislike, every warning said "Contains Finn's Vodka."
- **Decision:** a dislike warning names the user's own word: "Contains vodka," or "May contain vodka" when it's one choice of several. Category preferences like "alcohol" still name the item, because there the item is the useful part.
- **Related:** one house style for the ingredient explanations (4,776 by October 2026): what the food is, what it's made of, and one helpful touch. No trivia, and no allergy call-outs in the prose, because the warnings do that job. About 1,800 were rewritten to it in September 2026.

## 7. Fair billing
*January and September 2026*

- **Decision:** one free scan per new account on an eligible iPhone, then small credit packs.
- **Rejected:** a subscription.
- **How it stays fair:** the free scan is limited per iPhone with Apple's DeviceCheck, so new accounts on the same phone can't farm it. If Apple can't confirm, nobody gets an unearned free scan. If the AI provider or our server fails, the scan is refunded, with limits so refunds can't be farmed. Every credit change happens on the server in one transaction, so the app can't grant itself credits.

## 8. Privacy as a product feature
*September 2026*

- **Consent first:** before the first scan, the app asks permission to send menu photos to OpenAI. Users can withdraw it in Settings.
- **Health data stays out of analytics:** allergies and diets are never sent to analytics or crash tools, and an automated test fails the build if code tries. Analytics starts only after sign-in and has an off switch.
- **Deletion that finishes:** account deletion is in the app and revokes Sign in with Apple. If that step fails, it stops and asks the user to retry instead of silently skipping it.
- **The law:** I had Washington's My Health My Data Act researched. Food allergies count as consumer health data, the law reaches small apps, and consumers can sue. A US-only launch still includes Washington, so I treated it as a launch requirement, not an edge case.

## 9. All AI runs on the server
*January 2026*

- **Decision:** the app never holds an AI key. The phone uploads the photos and a server job does the work, so a scan survives the user closing the app, and a push notification says when it's ready.
- **Rejected:** the original pipeline, which ran on the phone.

## 10. Run the AI steps in parallel
*January 2026, my proposal*

- **Situation:** a four-photo menu took about 5 minutes and was close to the server's time limit.
- **Decision:** process menu pages and ingredient batches in parallel instead of one after another.
- **Result:** ~5 minutes to ~80 seconds. The cap went back up from 4 photos per scan to 6.
