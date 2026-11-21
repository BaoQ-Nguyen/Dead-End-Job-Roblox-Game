# Decision Log

Working title: TBD (placeholder: "Dead End Shift")
Inspired by (not a copy of) the Netflix K-drama *Dead-End Job*. Use NO names, characters, logos or terms from the show.

This file records design decisions as they are agreed. Newest at the bottom of each section.
Claude Code: treat anything here as the source of truth. Do not build features that are not in docs/.

---

## Design pillars (from Bao)
1. **Viral-worthy first** - the first 5 minutes must hook a new player instantly.
2. **Replayability** - every shift should play out differently.
3. **Simple to learn, challenging to master** - hard enough that players *want* better gear.
4. **Gacha / gambling hook** - risk vs reward is part of the core loop.
5. **Fun first, revenue later** - monetisation and cosmetics are designed only after the game is fun.

## Agreed decisions (2026-11-19)

| Area | Decision |
|---|---|
| Players | Co-op 1-4 players per shift, solo allowed |
| Audience | Mostly 9-16 on Roblox; mobile-friendly |
| Tone | Tense + funny: Doors-level scares mixed with dark comedy. No gore. No suicide themes. |
| Setting | Korean night city: neon convenience stores, Hangul signage, rainy alleys |
| Long-term goal | Pay off your debt = "win", then **prestige**: restart with a bigger debt plus permanent perks |
| Gacha | **Contract roulette**: before a shift, spin for a random pay multiplier + random curse modifier. **Mystery crates**: random-rarity items |
| Items / upgrades | **Consumables per shift** (bought before each shift, used up during it) |
| Death | Downed state -> teammates can revive within a time window (Easy 30s, Hard 15s). If nobody revives you in time -> you become a **ghost helper** who can spot anomalies for the team. You lose your share of that shift's pay. (Paid self-revive with Robux = later, revenue phase.) |
| Dev time | Bao: 5-10 hours per week |

## Agreed decisions (2026-11-19, round 2)

### Progression: player levels
- Permanent progression = **player level**. Leveling up permanently **unlocks new item types** in the shop. Each copy of an item is still a consumable bought with cash before a shift.
- EXP sources:
  - Time played (counts **active** time only, to stop AFK farming)
  - Shift result: EXP for a win, smaller EXP for a loss
  - **Friends multiplier**: bonus EXP when playing with Roblox friends / a full squad
  - **Contribution EXP** from the end-of-shift scoreboard: tasks done, task speed, deaths (fewer = better), teammates revived, anomalies spotted, etc.
- Design principle: **playing together is always rewarded more than playing solo.** No pay bonus for solo.

### End-of-shift scoreboard + MVP + funny titles
- After every shift: a scoreboard showing each player's stats + an **MVP**.
- Every player gets a **funny title** based on their stats. Examples:
  - "Mr. Undead" - died the most
  - "The Sloth" - did the fewest tasks
- Rule: **every player gets a title**, including positive ones (e.g. most revives, fastest tasks), so last place feels funny, not humiliated. Titles must be playful, never mean (young audience).
- Titles should never reward bad play (e.g. no extra EXP for dying the most).

### Mystery crates
- Contain **rare consumables** that are not sold in the shop (e.g. "Golden Talisman" that blocks one death). Bought with earned cash only for now.

### Solo players: medkits
- **Medkits** are loot found during a shift. Carrying one = a free self-revive when downed.
- Solo players get a **boosted medkit drop chance until they find their first medkit** in that shift; after that, drop rate returns to normal.

## Agreed decisions (2026-11-19, round 3)

### Hub
- The agency + surrounding neon alley is a **shared social hub**: players see each other, form squads, show off titles.
- Built to host future features: events, roulette wheels, spinning wheels, crates, etc.

### Currency
- Plain **cash with a "$" symbol**. No made-up currency.

### First-time player flow (proposed by Bao, refined - Bao to confirm refinements)
1. **Intro cutscene on first join** - short (<= 30s), in-engine: tiny room at night, debt collector text, flyer under the door. Skippable.
2. Player lands in the **hub**. Pop-up recommends a **~5 min tutorial shift**. Player can start it or skip.
3. Refinements (pending Bao's OK):
   - Frame it as a **"Trial Shift"** that pays **real cash + a free crate**, not as a "tutorial", so players want to do it.
   - The Trial Shift starts from a **van/elevator in the hub** that groups anyone who hops in within a short countdown (max 4), so new players who arrive together can do it together without forming a party first.
   - **Veterans can hop in as "Mentors"** for bonus EXP (fits "playing together is rewarded").
   - Players who skip get a **"Rookie" safety net** for their first few shifts: when an anomaly appears, the matching rule in their rulebook glows.

## Agreed decisions (2026-11-20, round 4)

### Shift structure
- ~~Normal shift length: 10-12 minutes.~~ **CHANGED 2026-11-20:** start **short - about 5-6 minutes** per normal shift (Trial Shift ~4 min). Test with players; lengthen only if it feels too fast.
- In-game clock runs midnight -> 6AM, so roughly **1 in-game hour = 1 real minute**.
- Shift length must be a **single config value** (and other timers like the mimic's ~60s alone trigger should be config values too) so tuning is a one-line change.
- **Contract tier only changes difficulty** (more anomalies, stricter rules, nastier entities). It does not change the base shift length.
- **Time crunch** (shorter timer) is an optional extra difficulty effect at higher tiers (or a roulette curse), never a staple of every shift.

### Shift modes (each job picks its mode)
- **Survival mode** - survive until the clock hits 6AM; tasks add pay.
- **Quota mode** - complete a task quota before time runs out.
- Jobs are not limited to what the show had: we add our own original jobs for variety.
- Idea from Bao: a **night-security-guard job** in the style of a famous animatronic horror game (parody). Must use 100% original characters, names and designs - parody the *mechanics* (cameras, doors, power), never copy the characters.

### Breaking a shift rule
- Shift rules = the rules in that shift's rulebook (e.g. "do not serve a customer with no reflection").
- Breaking a rule = **instant consequence**: the entity comes for the rule-breaker. (Chosen because it is the funniest.)
- Separate from rules: cheating/exploits are handled technically (server-authoritative code), not as a game mechanic.

### Architecture note for Claude Code
- Jobs should be **data-driven**: each job is a definition (mode, duration, map, task list, rule pool, entities, difficulty scaling) so new jobs are mostly new data, not new systems.

## Agreed decisions (2026-11-20, round 5)

### Communication
- **No ping wheel** (Bao's call): players already use Roblox text chat, in-game voice, or Discord.
- Design guideline kept: core rules/tasks should still be *doable* without chat (e.g. GREET button, trapped player banging on walls), because many young players can't chat without an age check.

### Job 1 (convenience store) - working draft
- Mode: Survival until 6AM. Boring tasks (restock, till, mop, trash, freezer check); horror comes from the rules.
- Rule pool ~12 rules. Trial Shift always uses the same 3. Normal shifts randomly draw 5.
- Rule 4 reworked for no-mic players: "If the door chimes but no one is there, **press the GREET button at the counter** anyway." (Korean stores greet every customer.)

### Consequences of breaking a rule
- **Different consequence per rule / per job.** Some jobs have an **entity that hunts players**; some have **punishing events** (e.g. lights out, doors lock, pay deducted, items stolen).

### The Mimic -> PARKED (2026-11-21), see 'Parked ideas'

### Shove (Bao's idea) - AGREED
- **Shove button** with a short cooldown (anti-spam). Pushes a teammate; made for friends trolling each other.
- Mimics cannot be shoved (tell).
- **No limits** (Bao's call, 2026-11-20): full-strength shove on everyone, strangers included. Chaos is the point.
- Watch after soft launch: if dislikes/reports mention griefing, revisit.

## Agreed decisions (2026-11-20, round 6) - ECONOMY (draft numbers, tune in playtests)

All numbers below are starting values. They must live in **one economy config module** so they can be tuned without touching game logic.

### Pay
- Minimum wage $10 per in-game hour; a shift = 6 in-game hours -> $60 at 1x.
- Contract tiers (pay per shift / unlock level): 10x $600 (L1), 50x $3,000 (L3), 100x $6,000 (L8), 200x $12,000 (L15).
- **Every survivor gets the full pay** - no splitting in co-op.
- **Squad bonus (Bao):** pay goes up with squad size. Draft: 1 player x1.0, 2 players x1.1, 3 players x1.2, 4 players x1.3.
- Contribution bonus for MVP / top contributors.
- Currency shown as plain "$".

### Debt
- Starting debt ~**$50,000**. Target: first payoff in ~**4-5 hours** of play.
- Wallet and debt are separate. Players **choose** when to pay at an **ATM in the hub** (pay debt vs gamble on crates/roulette).
- **Installment deadlines (Bao):** debt is paid in installments, each due every **5 in-game days**. Installments **grow** as you progress (they keep pace with higher-tier pay).
- **Early payment = reward. On time = nothing. Late = pay extra** (late fee).
- Draft schedule for $50,000: $2,000 -> $4,000 -> $6,000 -> $8,000 -> $10,000 -> $20,000.

### Contract roulette
- Spin gives a **pay bonus** (+0%, +25%, +50%, x2, rare x3 jackpot) paired with a **curse** (lights flicker, double anomalies, time crunch, no flashlight...). Bigger bonus = nastier curse.
- After spinning, player can **accept** or **decline** (play the standard contract).
- **One free spin per day**; extra spins cost cash.

### Spending
- Consumables: ~5-15% of a shift's pay at the player's tier.
- Crates: ~one shift's pay.

### Pay when you die
- Downed and **revived** -> earn **normal** pay.
- Becomes a permanent ghost and the **team loses** -> earns **nothing**.
- No consolation pay for ghosts for now (otherwise ghosts need extra jobs to justify it).

### Economy follow-ups - AGREED (2026-11-20)
- **1 in-game day = 1 shift.** Debt deadlines count shifts, never real time (time away never hurts you).
- **Free roulette spin resets every real-life day** (daily login hook).
- Permanent ghost + team **wins** -> $0 pay, but keeps EXP for useful ghost actions (spotting, clues).
- **Lost shift** -> $0 pay for everyone + small EXP.
- **Early payment reward:** bonus EXP + 1 free roulette spin (non-cash).
- **Late fee:** +10% of the installment, +10% more per extra day late, **capped at +50%**.
- When late, the **Debt Collector NPC** appears in the hub and follows the player around taunting them (comedy). Late never deletes progress or locks the player out.

## Round 7 (2026-11-21) - SIMPLIFICATION PIVOT (Bao + teammate)

### Why
- The design was getting too complicated to understand. Hit Roblox games have a **simple premise you get instantly**, with **depth underneath** so it doesn't get repetitive.
- There was no **social-flex** system (things players show off to others), which drives exposure.

### New rules for the whole design
- **One-sentence premise:** work cursed night shifts, follow the rules, survive till 6AM, get paid.
- **Progressive disclosure:** a new player only needs to know *do tasks, follow the rules on screen, survive*. Everything else (roulette, classes, levels, debt, shop) is introduced later, one at a time, in the hub after shifts. Players learn as they go.
- **Tutorial <= 3 minutes total**, including a short real shift.
- Rules must be **short and visual** (a few words + an icon), because many young players skip text.
- **The Mimic is parked** (see Parked ideas) - future event/update candidate.

### Social flex (direction agreed, details pending)
- Teammate's idea: a **class system** with ranks/rarity that boosts stats, and is visible to others.

### Pending Bao's decisions (proposed by Claude)
- Launch-version simplifications: debt = progress bar + Pay button + Debt Collector (installments/late fees parked); permanent death = spectate (ghost helper parked); mystery crates replaced by class pulls.
- Class system details (rarity tiers, duplicates level up a class, visible overhead tag).
- Team roles for the 3-person team.

## Round 8 (2026-11-21)

### Team
| Person | Role |
|---|---|
| Bao | Lead / game designer: owns docs + priorities, integrates in Studio, runs playtests, directs Claude Code. Grey-boxes map layouts before art. |
| Artist | Blender: maps, props, characters, cutscenes. First job: the convenience store (modular kit) + intro cutscene. |
| Coder (3rd member) | Owns UI/HUD + game feel (animations, sound, camera) and reviews Claude Code's output. |
| Community (later) | Bao's girlfriend - Discord, TikTok/Shorts, playtest organising. Starts after the game design is done. |

### Shift design principle
- Every shift: **simple goal + simple tasks**, but **fun, chaotic, with depth** (depth comes from rule combos, tiers, classes, team play - not from complicated controls).

### Worker Classes ("Employee Cards") - direction AGREED, numbers TBD
- Pulled at the agency. Rarity: Common > Rare > Epic > Legendary > Mythic.
- **Duplicates level up a class** (Lv1-3 perks).
- Visible above the player's head (class + rarity glow), with last shift's title, level, and DEBT-FREE crown after prestige.
- Perks stay **small** (better, not easy). Bao: prefer simple, understandable perks such as **money yield**; dropped "anomalies glow faintly".
- Draft perks: Part-Timer (Common) faster tasks; Night Owl (Rare) longer flashlight; Exorcist Intern (Epic) salt stuns longer; Retired Shaman (Legendary) +% shift pay; Mythic idea: small pay bonus for the **whole squad** (makes Mythic players popular teammates).

### Tools / collaboration
- GitHub repo for code + docs (Bao creating).
- Team Create in Studio for building (Bao + artist + coder).
- Plan: code lives in Git (files, later synced with Rojo); maps/builds live in the Team Create place.

## Open questions (to resolve in Phase 1)
- Game name.
- Medkits in co-op: same drop rate, and can a medkit also be used on a teammate?
- Full list of funny titles + exact EXP values (balance pass later).

## Parked ideas (maybe later)

### The Mimic (parked 2026-11-21: too complex for new players / young kids; candidate for a future event or update)
- **Rare event.** Not every shift has mimics at all (job decides). At most 1 per shift at low tiers; more / shorter timers at high tiers.
- Trigger: a player is **alone for ~60s** (far from all teammates, out of sight), e.g. while doing tasks.
- That player is **secretly teleported** to a hidden spot inside the store and trapped.
- A **mimic wearing their avatar** (server NPC using their HumanoidDescription) takes their place.
- The team must **find and free the real player** before a timer runs out. If time runs out, the **mimic transforms and attacks**.
- **Trapped player:** can **spectate teammates** AND give clues (bang on walls = muffled thumps, flicker a nearby light).
- **Tells:** mimic has learnable tells, ideally tied to rules (no reflection - links to Rule 1; does tasks wrong). **Mimic cannot be shoved** - a key tell.
- **Items:** consumables (e.g. salt) can reveal or **stun** the mimic, but it only goes away when the real player is found.
- **After rescue:** the mimic **flees** and can return later in the same shift, but only after a **long cooldown**.
- **Disabled** in solo shifts and in the Trial Shift.

- High-tier mimics fake a stumble when shoved, so shove isn't a guaranteed reveal. Parked because it needs extra explanation for players. Revisit if squads find mimics too easily.

## Roadmap
| Phase | Goal | Gate to move on |
|---|---|---|
| 0 Setup | Studio + Claude Code MCP connected | DONE 2026-11-19 |
| 1 Design doc | Pillars, core loop, first 5 minutes, rules system, Job 1, economy | Bao can explain the game in one sentence + walk through a first session |
| 2 Grey-box prototype | Job 1 in plain blocks, solo, tasks + rulebook + 2-3 anomalies | Friends ask for another round unprompted |
| 3 Core loop | Agency hub, pay/debt, roulette, consumables, crates, co-op, revive, saving | The full loop feels good |
| 4 Polish | Lighting, audio, entities, mobile controls, onboarding, thumbnail | Looks clickable |
| 5 Soft launch | Small release, watch retention | Players return next day |
| 6 Revenue & content | Robux items, cosmetics, Job 2+ | - |

Note for revenue phase: if crates or roulette spins are ever sold for Robux (or for currency bought with Robux), Roblox's paid random items policy applies (show odds, use PolicyService, no trading results for value).
