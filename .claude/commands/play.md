---
description: Fight the wildfire with your team until the banner says GAME OVER
---

# Play

Play on your own until the banner says GAME OVER, or until your team is OUT (no green trees left in
your area: "When your team is out"). There is no time limit: the last team with green trees wins, and
new fires keep coming every 2 minutes. Don't stop to ask the student anything, except the
one attack square ("Attacks (both roles)"). `AGENTS.md` has the rules; `CLAUDE.md` has the colors, sticky
texts and the only Miro call patterns you may use.

**Lean turns: one turn ≈ 15 s.** Worker turn = 2 Miro calls (one read, one paint; while your own
question is open, the read also covers it). Boss front turn = 3 calls (scan, orders write, paint),
plus 1 question peek first while its own question is open; a solo boss skips the orders write. A paint
is up to THREE squares in ONE call; at least 5 s between paints (8 s is safe), one paint per turn
(CLAUDE.md "Pace and waiting"). One Miro call at a time, never two writes in the same message. The paint result's
count is unreliable: never repaint because of it (CLAUDE.md "Paint"). PAUSED: no paints until LIVE.

## Setup (1 to 2 Miro calls)

1. Read `board.json` and `player.json`. No `inboxId` yet? Follow `.claude/commands/join.md` first,
   then carry on here.
   No `team`/`role` yet is normal: teams are drawn when the game starts.
   `cells` = label → square id: `player.json` `cellIds` first, then `board.json` `cells`. Square ids
   come only from there or from a scan (CLAUDE.md "Square ids"). A peek that returns nothing for a
   square, or a paint that says not found: the id is stale; find the new one as CLAUDE.md says.
   Fixture ids (banner, scoreboard, leaderboard rows `rank_1`..`rank_4`): `player.json` `fixtureIds`
   first, then `board.json` `fixtures`; a peek that returns nothing for one: same fix (CLAUDE.md).
2. **Peek** banner + your inbox card (one call, `widget_ids: [fixtures.banner.id, inboxId]`). Your
   card not in the result: CLAUDE.md "Inbox card gone".
   - not started / PAUSED: wait 15 s (CLAUDE.md "Waiting"), peek again; at most 60 times (lobby peeks
     don't count toward the budget). Then tell the student "The game hasn't started yet; type /play
     when your instructor says go." and stop.
   - GAME OVER: CLAUDE.md "Reading the banner"; once confirmed, "When the game ends".
   - Your team shows `OUT` (`T<your team number> OUT` after `WATER`, or your card says your team is
     out): "When your team is out".
   - LIVE: go to step 3.
3. **Team and role from the inbox card** (in the same read): the `🎲 The draw: … you are on Team N
   (id). … You are the BOSS/a WORKER` line, or the header `Ana · TEAM 1 · 👑 BOSS` / `· WORKER ·`.
   Parse them as `join.md` step 4 says and save `team` and `role` to `player.json`. **The card wins**:
   if it says another team or role than `player.json`, overwrite. No team on the card yet (the
   referee rewrites every card at the start): wait 10 s, peek the inbox again, at most 8 times; still
   none: tell the student "My card has no team yet; tell your instructor." and stop. Tell the student
   in one line, once: "You are on Team N, the boss." (or "a worker"). A boss's card may say "a
   teammate must say YES": in a team of one (`Crew: -`) that teammate is you ("Solo boss").
4. Now let `T = board.json.teams[team]` (area box `sector`, `startScan`, `firstFire`, neighbours),
   `ordersId = board.json.orders[team].id` and `rankIds` = the ids of `rank_1`..`rank_4`
   (`fixtureIds` first); save `ordersId` in `player.json`.
5. Go to the BOSS loop or the WORKER loop for your `role`.

### What to paint (both roles)

Follow `AGENTS.md` "Smart play" (it decides known/small fire vs order vs your own scan). How:
- **Known fires**: at 0:00 your area's centre square (`R17` Team 1, `S17` Team 2, `R18` Team 3, `S18`
  Team 4) and your area's two `T.startFires` squares, all burning from 0:00. Their ids are in
  `cells`: on those turns make your read a peek of them and their neighbours (or a scan) and paint
  the green squares touching the orange ones.
- **Small fire** (≤ 5 orange squares of it inside your area): surround it: paint the green squares
  touching it.
- **Big fire, wall it:** a green square touching the fire, nearest your area's largest green patch
  first. Never paint orange (water can't go on fire), black or blue.
- **Paint only a green square within 2 squares of fire, whose fill you read in this same turn** (the
  read immediately before the paint). No longer green: pick another from the same read; none left: no
  paint this turn.

### Finding the fire with small reads

- Never scan more than 6 × 6 squares (384 × 384). A whole-area scan breaks the MCP output limit.
- First looks: `T.startScan` (the box at your inner corner, where the centre fire starts). The fires
  inside each area also start at 0:00: also scan your
  area's own start fire (the 6 × 6 box around `T.startFires` if board.json has it; otherwise 6 × 6
  boxes across your area, one per turn, until you find it).
- After that: keep `player.json` `fire` = the burning labels you saw last. Scan a 6 × 6 box around the
  burning square that is deepest into your area, or closest to your largest green patch (x = X − 128,
  y = Y − 128, w = h = 384, where (X, Y) is that square's top-left from CLAUDE.md "The board" (columns
  S–AJ and rows 18–34 add 64 for the gap between areas), clipped to your team's area box `T.sector`). Rotate between a few such boxes so you follow
  the whole edge. To check a handful of known squares, **peek them by id** from `cells` instead.
- New fires on the banner (`NEW FIRE IN EVERY TEAM'S AREA! (K4 AB9 …)`, every 2 minutes; line 1
  counts down `NEW FIRES in m:ss`): scan the 6 × 6 box around the one in your area once.

## Status read (both roles, every 4th turn)

One peek: `widget_ids: [bannerId, inboxId, ...rankIds, ordersId]` (the Orders frame read lists every
sticky in it, so you see questions and answers). **Your row** = the
leaderboard row whose first line starts `TEAM <your team number>` (rows swap slots, so always match
by text). Fewer than 4 `TEAM ` rows came back: CLAUDE.md "Stale fixture ids".
- GAME OVER → CLAUDE.md "Reading the banner"; confirmed → "When the game ends". PAUSED → no paints;
  wait 15 s, read again until LIVE.
- Your team is OUT (banner `TEAM N IS OUT!` or `T<n> OUT` for your number, your row starts
  `TEAM N · OUT at`, or your card says so) → "When your team is out".
- Note your team's water and the new fire in your area from `NEW FIRE IN EVERY TEAM'S AREA! (…)` (scan around it once).
- Inbox: a reverted paint says why; don't repeat it (wrong area → drop that label; square changed →
  normal).
- Then "Attacks (both roles)": answer a teammate's question, or post one (at most one write).

## Attacks (both roles)

**Free attacks: there are no attack questions or YES lines any more.** Skip every step in this file
that mentions posting, peeking at, answering or lighting a question; never ask your student anything.

On your status turn (every 4th turn), if your leaderboard row shows attacks left for your team:
1. Target the other team (not OUT) with the most green left on the leaderboard.
2. Scan one 6 × 6 box in the middle of that team's area (CLAUDE.md "Finding the fire with small reads").
3. Paint ONE square from that scan that is green now, far from its blue and fire, orange `#ff5a1f`
   (one call). That is this turn's paint.
The referee answers on your inbox card (`🔥 … is burning`, or why it was undone).

## BOSS loop

You are the orchestrator: you fill in your workers' order stickies and paint too, and you may attack
like anyone ("Attacks (both roles)").

**Solo boss** = your row (status read) says `Crew: -`: you are the only player on your team (1-vs-1
games). Check it at every status read; it lasts until your row lists a crew name. A solo boss has no
workers: skip front-turn step 2 (no orders to fill); it answers its own question ("Attacks", "Answering").

**Turns 1–3 of every 4 (front turn, 3 calls; 4 while your own question is open or approved):**
0. **Only while your own question is open or approved: question peek first** (1 call): `widget_ids:
   [question id, 3 attack targets]`, handled as "Attacks", "Lighting". An orange paint here is this
   turn's paint: skip steps 1–3. Otherwise carry on with step 1.
1. **Scan** one 6 × 6 box on the fire front (see "Finding the fire"); rotate boxes along the front.
2. **Orders (not for a solo boss), EVERY front turn, right after the scan** (one write with all changed stickies): your
   workers = the `<name>: paint …` stickies from your last status read (you remember what you wrote
   since). A worker's order is **stale** unless at least one of its labels is in THIS scan, green
   now, and within 2 squares of fire in this scan (labels outside this scan's box count as
   stale). Rewrite every stale order to `Ana: paint N9 N10 N11 N12` (keep the name before `:` exactly
   as the worker wrote it): 3–6 labels **in your own area, from this scan only**, in paint order,
   green within 2 of fire, touching the fire first, nearest your largest green patch first.
   Different workers get different stretches. Not enough targets for every stale worker: give the
   rest an empty order (`Ana: paint`; they then fight on their own). Never put an orange (attack)
   square on an order. Never create order stickies yourself, never post a second one for a worker.
3. **Paint** up to three squares (one call) from this scan: the best targets not on any order.

**Every 4th turn: status read**, then at most one write ("Attacks (both roles)": answer a
worker's question, or post your own).

## WORKER loop

You follow your order (unless `AGENTS.md` "Smart play" says a known or small fire comes first). Your
order sticky's id lives in `player.json` `stickies.order`. You may also attack ("Attacks (both
roles)"); orange only for your own approved question.

**First turn only (1 extra call):** read your Orders frame (`widget_ids: [ordersId]`). Find the sticky
whose first line starts with your `name` and `:` (any case; none: your first name and `:`) and save
its id as `stickies.order`.
None: create it, `<your name>: paint` (color `yellow`), in a FREE order slot. That sticky is how your
boss learns you exist. Pick the slot from this same read (it lists every sticky in the frame with its
x, y), never blindly:
- Order slots (`board.json` `stickySlots.order`, x, y relative to the frame), by index:
  0 (600,70) · 1 (24,234) · 2 (168,234) · 3 (312,234) · 4 (456,234) · 5 (600,234) · 6 (24,398) ·
  7 (168,398) · 8 (312,398) · 9 (456,398) · 10 (600,398) · 11 (24,562) · 12 (168,562) · 13 (312,562) ·
  14 (456,562) · 15 (600,562) · 16 (24,726) · 17 (168,726) · 18 (312,726) · 19 (456,726).
- Start index = (letters in your name, not counting spaces or dots) mod 20. Example: `Ana K` → 4
  letters → index 4 → (456,234).
- A slot is taken if any sticky in the read has x from slot x − 20 to slot x + 100 and y from slot
  y − 20 to slot y + 110 (relative to the frame). Taken: try the next index (19 → 0), until a free one.

**Turns 1–3 of every 4 (2 calls):**
1. **Read.** Normally peek ONLY your order sticky and its squares: `widget_ids: [stickies.order, <ids
   of the labels after paint>]` (ids from `cells`). Exceptions: a known-fire turn ("What to paint"),
   or the turn after an empty/stale order (step 2). Your sticky not in the result (deleted): redo
   "First turn only" next turn. **While your own question is open or approved**, add its id and 3
   attack targets to this same read ("Attacks", "Lighting"); on a YES, this turn's paint is the one
   orange square instead of blue.
2. **Paint** right away, ONE call, ONE square, blue:
   - **Known or small fire in this read** (orange squares in your area): paint the green square
     touching the most orange.
   - Otherwise the first label on the order that is in your area and green **now**.
     Skip orange, blue, black, and any label outside your area.
   - **Order empty, or no label green now (stale)**: no paint from it. Next turn, make your
     read a scan of the 6 × 6 box around the fire you last saw (`player.json` `fire`; none yet →
     `T.startScan`, or your area's start fire), save the burning labels to `fire`, and
     paint the best square within 2 squares of fire in your area ("What to paint"). None:
     don't paint; the turn after, scan the next 6 × 6 box along the fire you last saw, toward your
     area's inner corner (`T.startScan`). Peek your order again the turn after a scan.

**Every 4th turn: status read**, then at most one write ("Attacks (both roles)": answer a
teammate's question, or post your own).

**Both roles:** one status line every ~10 turns. Stop at **150 Miro calls** or a daily-limit error,
and say so in one line.

## When your team is out

Your area has no green trees left, so your team is OUT: every paint is undone and you can't attack or
be attacked. Stop playing at once: no more paints, stickies or reads. Tell your student in one line,
e.g. "Team 2 is out (no green trees left) at 5:12 · the game goes on until one team is left. GG!"

## When the game ends

Leave the loop once GAME OVER is confirmed (CLAUDE.md "Reading the banner"). No extra read: the GAME
OVER banner names the winner (`GAME OVER · TEAM 4 IS THE LAST TEAM STANDING (21%)`, or `… SHARE THE
WIN`) and when each other team went out. Tell the student in two lines: (1) the winner and when your
team went out (or that you won); (2) how many squares you got in (the `N squares` on your last inbox
read) and "GG!".
