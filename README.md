# My Project Portfolio

## Killspree

###  Showcase

[Watch the 23-minute Killspree showcase](https://www.youtube.com/watch?v=0ZLOrN9r6tU)

> The video demonstrates the gameplay, Killer abilities, combat, and networking systems that I worked on.

### My Contributions

I was the **Lead Programmer** for Killspree. I programmed the Killers and their abilities demonstrated throughout the showcase.

I also developed several core gameplay and networking systems, including:

* **Killer abilities and gameplay systems** — programmed the Killer characters and abilities showcased in the video.
* **Hitbox system** — developed the hitbox system used for gameplay interactions and combat.
* **Networking** — implemented interpolated buffer snapshot rollback netcode to improve the responsiveness and smoothness of gameplay, particularly when playing as the Killer.
* **Gameplay programming** — worked on the underlying systems required to make the showcased gameplay function correctly in a networked environment.

### Source Code

The production source code for this project is private and cannot be publicly shared. This repository is intended to showcase my work and demonstrate the systems I contributed to through gameplay footage.


# Backrooms Tower Defense

**Showcase:** [Watch the 9-minute showcase](https://www.youtube.com/watch?v=yrDP3b7QeC4)

The video walks through the gameplay, tower logic, enemy logic, networking systems, and more.

## My Contribution

I was the lead programmer on this project. I built:
- All of the frontend UI
- All of the backend systems
- All of the networking architecture

Aside from two or three minor exceptions, I was responsible for essentially everything under the hood.

## Trading System

This is the piece I'm proudest of. Player-to-player trading is historically one of the easiest systems to exploit on Roblox — duplication ("duping") bugs, where a player trades an item and ends up keeping a copy of it, are the most common and most damaging exploit across games on the platform.

To prevent this, the trading system combines several layers of protection:

- **Session locking** — each active trade is represented by its own stateful object that tracks whether it's `Active`, `Processing`, or `Complete`, and every client-facing action (offering items, confirming, canceling) is gated by that state so nothing can be modified mid-settlement.
- **A confirmation countdown with re-arming** — both players must confirm, and any change to the offer after confirming automatically un-confirms both sides, preventing last-second offer swaps.
- **Atomic settlement with rollback** — when a trade finalizes, both players' inventories are snapshotted first. Items are removed from both sides before anything is granted; if any step fails partway through, both snapshots are restored exactly as they were, and any stat/counter side effects (like tower-count tracking) are diffed and reversed so nothing drifts out of sync.
- **Datastore-health gating** — trades refuse to finalize at all if the datastore is in a critical or closing state, rather than risk a write landing in an inconsistent place.
- **Server shutdown safety** — trades in the middle of processing are given a grace window on `BindToClose` and player-leaving so a settlement isn't cut off mid-write.

We backed this system with a **$300 bounty** at launch for anyone who could produce a working dupe exploit. Over five experienced exploiters attempted it and failed. Across hundreds of thousands of trades processed since release, there has not been a single reported duplication incident.

## Quest System

The quest system manages daily, weekly, lifetime, and clan-specific quest categories per player, with a few reliability details I put real thought into:

- **Refresh-in-progress guards** — because quest refresh logic reads and writes through an async data layer, overlapping refresh calls for the same player (e.g. triggered by network hiccups) are blocked from running concurrently, which prevents duplicate quest batches from ever being generated.
- **Self-healing corruption detection** — if a player's quest list is ever found missing a daily or weekly quest (from a bad state, failed write, etc.), the system detects it and regenerates that category automatically, rather than leaving the player permanently stuck.
- **Clan quests as a fixed active set** — rather than expiring on a timer, clan quests always maintain a fixed number of active slots per player, refilled immediately when one is claimed. An anti-farming cap prevents the random slot rolls from stacking duplicate quest types, ensuring variety and preventing passive-farming strategies.
- **Periodic self-healing sweep** — a background loop periodically re-checks every trackable player and tops up any missing quest slots, so a single dropped event (e.g. a one-shot check that lost a timing race) can't leave a player quest-less for an entire session.

## Distributed Leaderboard System

The leaderboard service is built to support large-scale, cross-server ranked leaderboards (up to 20,000 tracked players) without any single server becoming a bottleneck or a point of failure:

- **Cross-server build locking** — only one server at a time builds a given leaderboard's snapshot, coordinated through a token-based lock stored in the datastore itself. If a build runs long and its lock expires, a stale finalize is detected and safely discarded rather than clobbering fresher data.
- **Budget-aware pacing** — instead of fixed sleep intervals, datastore calls check Roblox's live request budget and only wait when there's actually contention, which keeps normal cycles fast while still avoiding throttling during bursts (e.g. paginated leaderboard reads or metadata enrichment).
- **Read-anywhere caching** — any server can serve a leaderboard snapshot on demand, independent of which server built it, with in-flight request coalescing so a crowd of players opening the same leaderboard at once triggers a single shared read instead of one per player.
- **Failure-safe finalization** — a failed or partial read from the live data store is explicitly never written over a good snapshot, preventing a transient failure from blanking a leaderboard for everyone.
- **Eager season-end payouts** — leaderboards that opt in get their final rankings built and reward tiers paid out to currently-online players immediately when a season rolls over, while offline players are safely caught by a lazy check on next login — with claim-tracking shared between both paths so nobody is double-paid.
