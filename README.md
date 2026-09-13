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


## Into the Backrooms Tower Defense

### Showcase
[Watch the 9-minute showcase](https://www.youtube.com/watch?v=yrDP3b7QeC4)

> The video demonstrates the gameplay, tower logic, enemy logic, networking systems, and more that I worked on.

### My contribution

I was the Lead programmer and I developed:
All of the frontend UI
All of the backend code
All of the networking design

Basically everything the game has except like 2-3 things which arent of much importance.

I am most proud of my trading system which used session locking combined with rate limits and atomic trades to result in a flushed out system. During release, we placed a bounty of 300 dollars so that if anyone found a dupe(a situation in which you trade an item and replicate it. This is the most prominent issue in every game on the platform) they would be rewarded heavily. Even after over five seasoned exploiters attempted to claim the bounty, they all failed. Additionally, after the hundreds of thousands of trades our game has handeled, there was not one report of a dupe glitch.

I am next most proud of my quest system which usued good practices.

I am next most proud of my distributed systems leaderboard which could store up to the top 20,000 players on an updating leaderboard by distributing server load across multiple servers by using timed locks.
