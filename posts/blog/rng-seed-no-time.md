---
title: Seeding RNG Without the Current Time
description: When developing a game for a fantasy console that doesn't have the concept of time I needed a way to seed the Random Number Generator.
date: 2024-03-08
---

A while ago I was working on a little game for the [WASM-4](https://wasm4.org/) fantasy console. It's a super limited platform with few colours and even fewers buttons, but it supports Rust and it felt like a good way to dip into game dev (which I do now and then) without having to worry about fancy graphics or super-involved gameplay. The game is an implementation of the one-page RPG [Potato by Oliver Darkshire](https://twitter.com/deathbybadger/status/1567425842526945280) and I called it [ポテート](https://michaelenger.com/poteto/) (potēto).

![Screenshot of the potēto game showing 3 partially filled tracks, destiny, potatoes and orcs, as well as prompts to roll and hurl.](/assets/blog/poteto-game.png)

The point of the game is to gather up potatoes while avoiding orcs and an encroaching destiny that threatens to take you away from your potato farm. You roll the dice using the `X` key and various events happen that advance one of the tracks. You can hurl potatoes over the fence to distract orcs using the `Z` key and you can reset the game at any point using `R`. It's a hard game to win, but it _is_ possible.

Being an RPG (role-playing game) the game relies a lot on RNG (random number generation). RNG requires an initial seed value to function properly. If you don't seed your RNG before you use it you're going to end up with getting the same results every time. Usually I would get the current system time and use that as a seed, a tried and trusted technique that I learned ...somewhere. But the WASM-4 fantasy console has no way to get the current time of the system it's running in, so I had to improvise.

Before I describe what I did I must explain that this is an issue that is older than I am, and has been solved faster and better by the gurus whose shoulders I stand on. However, I didn't want to just look up an existing algorithm as this was a project done just for fun. So if my solutions seems rudimentary or pointlessly bad please excuse my ignorance. It was fun to make, and sometimes that's enough.

For the actual random number generation I used an existing RNG library called [fastrand](https://crates.io/crates/fastrand), as I wasn't interested in making my own randomisation algorithm. Then to get a random seed to give it I took advantage of the fact that WASM-4 uses a standard update loop. I would increment a tick counter every time the loop repeated, and when a new random value was needed I would seed the fastrand `Rng` instance using this tick value. I also included the previous result along with the tick, to add another element of randomisation.

```rust
use fastrand;

let rng = fastrand::Rng::with_seed(24601);
let mut seed_counter: u64 = 0;
let mut previous_result: u64 = 0;

// On every update loop
seed_counter += 1;

// When getting a new random value given a range
rng.seed(seed_counter + previous_result);
let result = rng.u64(1..100);
previous_result = result;
```

I was partially inspired by what I _think_ was done in some NES games. I may be misremembering, but I vaguely recall hearing in a video essay that a game would start a counter when it first booted up and then use that counter to seed the RNG when the player first pressed the Start button. That way the player's action would be the randomising element.

This seemed like such a clever idea and I tried to replicate it somewhat with my solution. Random numbers are only requested when the player rolls the dice, so the amount of ticks would vary depending on how quickly the player pressed the roll button. It may be overkill to seed the RNG every time a new value is requested, but it saved me from having to put in any logic for starting and stopping the tick counter. The game is very simple and even has no animations in it, so it's not like I was eating up a lot of processing power keeping the tick counter going or seeing the RNG.

If you want to see the solution for yourself it's [available on GitHub](https://github.com/michaelenger/poteto/blob/main/src/rng.rs). It's very simple, but it worked out well for me and I enjoyed working within the limitations of the WASM-4 fantasy console. Making the game also showed me that the rules of the RPG are somewhat flawed and is too dependent on dice rolls, so the player has no real agency as to whether or not they win. Oh well 🤷