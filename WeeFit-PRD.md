# WeeFit — Product Requirements Document

**Product:** WeeFit — social calorie & nutrition tracker
**Document type:** PRD (v1 scope)
**Status:** Draft for review
**Last updated:** June 3, 2026

> **Note on scope:** This PRD captures the requirements for the v1 product as currently defined. Must-Have (P0) items describe the shipped/in-scope v1. Nice-to-Have (P1) and Future Considerations (P2) are forward-looking and not built unless promoted. Several success-metric targets and behaviors are not yet specified in source material and are surfaced in **Open Questions** rather than assumed.

---

## Problem Statement

People who want to manage their weight have plenty of calorie trackers, but most feel like data-entry chores: generic food databases that under-represent regional cuisines (especially Indian food), logging that gives nothing back, and no social accountability to sustain the habit. The hard part of nutrition tracking isn't the math — it's staying motivated past week two.

WeeFit targets habit-forming, culturally-relevant tracking: fast logging from a food set heavy on Indian dishes, a points-and-leaderboard loop that rewards consistency, and friends-based competition for accountability. The cost of not solving it is the same as every abandoned tracker — users log for a few days, see no payoff, and churn.

---

## Goals

1. **Make daily logging a sustained habit.** Drive repeat logging, measured by the share of activated users who log a meal on 4+ days in their first week and maintain multi-day streaks.
2. **Reduce friction in food entry**, especially for Indian cuisine, so a user can find and log a common meal in seconds without creating a custom food.
3. **Use gamification to lift retention**, not just engagement — points and streaks should correlate with users coming back, not merely clicking more.
4. **Make accountability social.** A meaningful share of active users should add at least one friend and appear on a leaderboard.
5. **Get users to a personalized goal fast.** Onboarding should produce an accurate, personalized daily calorie target with minimal drop-off across the 4 steps.

These are user/retention outcomes, not feature outputs — "build a leaderboard" is a means; "social users retain better" is the goal.

---

## Non-Goals

1. **Not a coaching or medical product.** WeeFit calculates a calorie goal via Mifflin-St Jeor but does not give medical advice, meal plans, or clinical guidance. Out of scope to avoid regulatory burden and scope creep.
2. **Not a public/global social network.** The leaderboard is friends-only by design. A global leaderboard, follows, or public profiles are out of scope for v1 — they change the privacy and moderation surface dramatically.
3. **Not a fitness/exercise tracker.** No workout logging, step counts, or wearables integration in v1. Activity level is captured once for BMR math only. Separate initiative if pursued.
4. **Not multi-provider auth.** v1 supports Google OAuth only (plus offline demo). Email/password, Apple, and other providers are deferred — one provider keeps onboarding simple for launch.
5. **Not a barcode/photo-based logger.** No barcode scanning, photo recognition, or external nutrition API in v1. The curated 110+ food set plus custom foods covers the core use case; recognition tech is high-cost, lower-confidence.

---

## Target Users

- **Primary — the consistency-seeker:** Wants to lose, maintain, or gain weight and has tried other trackers but dropped off. Motivated by streaks, points, and friendly competition. Often eats regional (Indian) food poorly served by mainstream apps.
- **Secondary — the social joiner:** Comes in because a friend invited them; the leaderboard and friend graph are the hook more than tracking itself.
- **Tertiary — the offline/privacy-minded user:** Wants to try the app without an account or backend; uses offline demo mode backed by localStorage.

---

## User Stories

### Onboarding & profile
- As a new user, I want to sign in with Google so that I can start without creating a password.
- As a new user, I want to claim a unique username with real-time availability feedback so that I know immediately whether my choice is taken.
- As a new user, I want to enter my body stats and goal so that the app gives me a calorie target tailored to me rather than a generic number.
- As a returning user, I want to update my profile later so that my goal stays accurate as my weight changes. *(see Open Questions on profile editing)*

### Logging (core loop)
- As a user, I want to search foods and see results filter as I type so that I can find a dish in seconds.
- As a user logging Indian meals, I want common dishes (dosa, idli, biryani, paneer) already in the database so that I rarely need to create custom entries.
- As a user, I want to pick a serving unit and quantity and preview calories/macros before saving so that I log the right amount with confidence.
- As a user, I want to create a custom food once and reuse it so that I'm not re-entering my regular meals.
- As a user, I want to delete a meal I logged by mistake so that my daily totals stay accurate.

### Gamification & motivation
- As a user, I want points for logging so that the habit feels rewarding.
- As a user, I want a bonus for hitting my daily goal so that I'm pulled toward the target, not just toward logging.
- As a user, I want a visible logging streak so that I'm motivated not to break the chain.

### Social
- As a user, I want to search for friends by username and send requests so that I can build a friend graph.
- As a user, I want to accept, decline, and re-request friendships so that I control my connections.
- As a user, I want a friends-only leaderboard with my rank so that I can compete without exposing my data publicly.

### History & insight
- As a user, I want to see today's consumed/remaining calories and macro totals so that I can decide what to eat next.
- As a user, I want past days grouped by date so that I can review trends.

### Edge / empty / error states
- As a new user with no friends, I want a clear empty-state prompting me to add friends rather than a blank leaderboard.
- As a user logging early in the day with no meals yet, I want my streak preserved (today shouldn't count against me until the day ends).
- As an offline user, I want the app to work without Supabase credentials so that I can try it with no backend.

---

## Requirements

### Must-Have (P0) — v1 cannot ship without these

**Authentication & onboarding**
- Google OAuth via Supabase Auth with an OAuth callback handler.
- Offline demo mode that creates a temporary demo user when Supabase env vars are absent.
- Username setup: 3–20 chars, alphanumeric + underscore, real-time uniqueness check against the database.
- Profile setup capturing age, gender, height, weight, target weight, activity level, fitness goal.
- Daily calorie goal computed via Mifflin-St Jeor BMR × activity factor, adjusted by goal (−500 loss / 0 maintain / +300 gain).
- Route guards enforce the auth → username → profile → app progression.

*Acceptance criteria:*
- Given a signed-in user without a username, when they hit any app route, then they are redirected to username setup.
- Given a username already taken, when the user types it, then they see unavailability before submitting.
- Given completed profile inputs, when the user finishes onboarding, then a numeric daily calorie goal is stored and shown on the dashboard.

**Food search & meal logging**
- Real-time client-side search across 110+ default foods + the user's custom foods.
- Food detail shows name, serving size, calories, macro breakdown.
- Serving unit dropdown (standard serving + cup/tbsp/tsp/slice/oz/g/lb/kg) with quantity entry and live macro preview.
- Nutrition stored per-100g internally; displayed values computed from unit × quantity.
- Logging a meal awards +10 points, runs the daily-goal check, and returns to the dashboard.

*Acceptance criteria:*
- Given a search query, when the user types, then the list filters without a page reload.
- Given a selected food, unit, and quantity, then the previewed calories/macros match the saved log entry.
- Given a successful log, then the user's point total increases by 10 and the meal appears in today's list.

**Custom food creation**
- Modal with name (required), serving size, calories/100g (required), protein/carbs/fat per 100g.
- Saved to Zustand (localStorage) and the `custom_foods` table; appears immediately with a "Custom" badge.

**Dashboard**
- Points banner with link to leaderboard; calorie progress (consumed/goal/remaining) with animated bar; macro totals (protein/carbs/fat); 2×2 quick-add grid (Breakfast/Lunch/Dinner/Snack); today's meals with per-entry delete.

**Meal deletion & point integrity**
- Deleting a meal removes the entry, deducts 10 points (floored at 0), writes a `FOOD_DELETED` row with negative value to `point_logs`, and updates `user_points`.

**Gamification**
- +10 per food logged; +50 once/day for reaching the calorie goal (dedup via localStorage flag + DB check); −10 per deletion (floor 0). Full audit trail in `point_logs`; total in `user_points`.

**Friends**
- Search by username; Friends / Requests / Invite tabs; pending/accepted/declined states; re-request after decline; bidirectional duplicate-prevention check.

**Leaderboard**
- Friends-only (current user + accepted friends). Summary card (total points, username, rank); ranked list with trophy/medal for top 3, numbered badges otherwise, avatar from username initial, "You" badge and row highlight for current user; manual refresh; empty state when no friends.

**Profile, streak, history, contact**
- Profile: identity, 3-stat grid, streak section, last-7-days expandable logs, goals card, body stats, update CTA.
- Streak: consecutive days with ≥1 logged meal; current day skipped if not yet logged.
- History: all days grouped newest-first, with date, weekday/"Today", meal-count badge, and calorie/macro totals.
- Contact: feedback form (email + message) → `feedback` table, with success state.

**Platform & data**
- Single Zustand store with `persist` (localStorage) holding all state and business logic; syncs to Supabase when configured.
- 8 Supabase tables (profiles, meal_logs, custom_foods, usernames, friendships, user_points, point_logs, feedback), all with RLS. `usernames` and `user_points` readable by anyone; `feedback` allows anonymous insert; FKs cascade on user deletion.
- Mobile-first responsive design; defined Tailwind design system (orange/sage palettes, DM Serif Display / Outfit / JetBrains Mono); specified Vercel security headers (HSTS, CSP, X-Frame-Options DENY, nosniff, referrer & permissions policy).
- Vercel SPA deploy with route rewrites; Supabase env vars optional (offline fallback).

### Nice-to-Have (P1) — strong fast-follows

- **Online/offline data reconciliation:** explicit sync/merge when a user starts offline then connects (or switches devices). *(currently undefined — see Open Questions)*
- **Server-authoritative points:** move point awards/deductions server-side to prevent client tampering of `user_points`.
- **Profile/username editing:** first-class flows for changing username and editing profile after onboarding.
- **Nutrition trends visualization:** charts for calories/macros over time (note: `recharts` is already a dependency but unused).
- **Richer food metadata:** micronutrients, food categories/tags, search by category.
- **Notifications/reminders:** streak-at-risk and daily-log nudges.

### Future Considerations (P2) — design for, don't build

- Additional auth providers (email/password, Apple) — keep auth abstraction provider-agnostic.
- Barcode scanning / photo recognition / third-party nutrition API integration.
- Exercise & activity tracking and wearable integrations.
- Group challenges, achievements/badges beyond the points number.
- Account data export and self-serve deletion (privacy/compliance) — keep schema clean of orphan-prone data now.
- Internationalization beyond the Indian-cuisine focus.

---

## Success Metrics

> Targets below are **proposed** and need owner sign-off (see Open Questions) — the product currently has no analytics instrumentation specified.

### Leading indicators (days–weeks)
- **Onboarding completion:** % of Google sign-ins that reach a stored calorie goal. *Proposed target: ≥ 70%.*
- **Day-1 activation:** % of onboarded users who log ≥ 1 meal on day 1. *Proposed: ≥ 60%.*
- **W1 logging frequency:** % of activated users logging on ≥ 4 of their first 7 days. *Proposed: ≥ 35%.*
- **Time-to-log:** median seconds from quick-add tap to saved meal. *Proposed: < 20s.*
- **Custom-food reliance:** % of logs that use a custom food (a high rate may signal database gaps). *Watch metric, no target.*
- **Social adoption:** % of active users with ≥ 1 accepted friend. *Proposed: ≥ 30%.*

### Lagging indicators (weeks–months)
- **W4 retention:** % of activated users still logging in week 4. *Proposed: ≥ 25%.*
- **Streak depth:** median longest streak among retained users.
- **Social retention lift:** W4 retention of users with ≥ 1 friend vs. users with none (validates the social hypothesis).
- **Goal adherence:** % of logging days where the user lands within a band of their calorie goal.

**Measurement:** requires adding event instrumentation (logging, onboarding steps, friend actions). Define tool, events, and dashboards before launch.

---

## Open Questions

- **[Data] Offline → online sync:** How do localStorage records reconcile with Supabase when a previously-offline user connects, or uses a second device? Conflict resolution and dedup are undefined. *(Blocking for P1 sync work.)*
- **[Engineering/Security] Points integrity:** Points live in localStorage and sync to `user_points`. What prevents a client from writing an arbitrary total? What are the RLS write policies on `user_points` and `point_logs`? *(Potentially blocking — affects leaderboard trust.)*
- **[Engineering] Daily-goal bonus across devices:** The +50 dedup uses a localStorage flag plus a DB check — does the DB check alone fully prevent double-award on a fresh device/cache clear?
- **[Data/Analytics] Instrumentation:** No analytics layer is specified. What tool, and which events, will measure the success metrics above? *(Blocking for measuring launch.)*
- **[Product] Metric targets:** All targets above are placeholders — what are the agreed success vs. stretch thresholds?
- **[Product] Profile & username editing:** The profile page has an "update" CTA — is full profile editing and username change in v1 scope or P1?
- **[Privacy/Legal] Account deletion & export:** FKs cascade on user deletion, but is there a user-facing deletion/export flow? Any GDPR-style obligations for the target market?
- **[Product] `recharts` dependency:** It's installed but unused — is a trends/chart feature already planned (promote P1) or should the dependency be removed?
- **[Design] Leaderboard refresh:** Manual refresh only — is real-time or pull-to-refresh expected, or is manual acceptable for v1?

---

## Timeline Considerations

- **Dependencies:** All synced features depend on Supabase being configured; offline mode must remain a clean fallback. Social features depend on the `usernames`/`friendships`/`user_points` tables and their RLS being correct before leaderboard trust matters.
- **Suggested phasing:**
  - **Phase 1 (core loop):** Auth, onboarding, food search, logging, dashboard, custom foods, basic points. This alone validates the "fast, culturally-relevant logging" hypothesis.
  - **Phase 2 (social):** Friends, leaderboard, streaks — validates the retention-via-accountability hypothesis. Add analytics instrumentation here at the latest.
  - **Phase 3 (hardening):** Server-authoritative points, offline/online sync, profile/username editing, trends charts.
- **Pre-launch gate:** Resolve the points-integrity and instrumentation open questions before promoting the leaderboard as a headline feature — a tamperable leaderboard undermines the core social loop.
