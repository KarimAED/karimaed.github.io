---
layout: splash
title:  "Quantum Gameplay"
header:
  teaser: /assets/images/iquhack_banner.jpeg
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: /assets/images/iquhack_banner.jpeg
---
How to hack a quantum computer and make a game out of it.

# Setting the Stage
When I was selected for MIT's annual quantum hackathon iQuHack 2022,
there were a lot of different feelings that stirred in me. At the time,
I was battling a [mono](https://en.wikipedia.org/wiki/Infectious_mononucleosis)
infection, and there was only a week to go to the hack. I had no idea if
I would be able to attend. There was not a doubt in my mind that I wanted
to though. After having applied for the event, I completely forgot about
it, not even expecting to be accepted. When I did, all I could think of
was that I might still be unable to attend because of my health.

Thus, the countdown began. Every day, I was getting a little better, and
every day the hackathon inched closer. In the end, I made it, but just barely.
I was still on painkillers for the first events on friday, but luckily by saturday
I felt just well enough to get going on this hack. The first challenge was overcome.
## The Challenge
As I was accepted into the MicrosoftxIonQ challenge of the hackathon, I excitedly
anticipated the release of the challenge goals. When I heard that we were supposed to
develop a game using a quantum computer, my first thought was: WHAT?? Where do we even start?

But quickly the wide scope of the challenge became an upside. We were given absolute
freedom to create something new. Ideas began to explode in my head, from platform games, to
games of chance, all relying on the quantum computer as a random number generator.
## The Squad
Luckily though, I wasn't left alone with my ideas. Otherwise, I would have undoubtedly chosen a
ridiculously overambitious project. My great team members [Francesco Scala](https://www.linkedin.com/in/francesco-scala-839507211/),
[Rabins Wosti](https://www.linkedin.com/in/rabins-wosti-703107152/), and
[Arunava Majumder](https://www.linkedin.com/in/arunava-majumder-33500a167/) luckily kept me on the ground.

We had met just the day before in the iQuHack slack channel, and connected only by shared passion,
we instantly formed a team. Meeting three others, so different from me, from all parts of the world,
and getting along immediately was an incredible experience. We were scattered across 4 countries, 4 time zones,
and 3 continents. Despite all of that, we were a team.

# Getting Down to Business

## Part 1: Storm the Brain
As I mentioned before, the others reined me in a little on this one. I was ready to develop a fully fledged platformer game with UI and everything. However, we realized together, that our focus should lie on the quantum part of the challenge and not the game part. The question became, how could we use quantum computing, not just for random numbers but actually for a core gameplay mechanic? To focus on this aspect, we decided not to program an entire gui etc. Instead, we decided to go with a card game, as those are easy to make. Which card game and how exactly we should use the quantum computer was still undecided though. The first suggestion came from Rabins, indicating that we should program a game where the player tries to get as low a number as possible by manipulating the card probabilities. That quickly evolved into an idea pushed by Francesco and me, that of quantum blackjack, or QuHackJack, as we termed it. The idea was to use 5 qubits to generate one of 32 numbers which each would correspond to a game card. We wanted to use 52 numbers to map exactly to the blackjack deck, but this was troublesome, as there was no simple mapping from 64 basis states for 6 qubits to 52 cards. Therefore, we limited ourselves to 32 cards ranging from 3 to 10 for each of the 4 classic suits. These choices made, we were ready to go!

## Part 2: How we made it work
We began by setting up 2 routines, corresponding to the quantum card draw and the basic game mechanics respectively. To draw the cards, we would initialize all 5 qubits in the |+> basis states, achieved by a single Hadamard gate application to each initial |0> state. Then we would sample a single value from this state, corresponding to a binary number ranging from |00000> to |11111>, which maps to the numbers from 0 to 31.