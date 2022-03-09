---
layout: splash
title:  "Quantum Gameplay"
header:
  teaser: /assets/images/iquhack_banner.jpeg
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: /assets/images/iquhack_banner.jpeg
  og_image: /assets/images/iquhack_banner.jpeg
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
We began by setting up 2 routines, corresponding to the quantum card draw and the basic game mechanics respectively. To draw the cards, we would initialize all 5 qubits in the |+> basis states, achieved by a single Hadamard gate application to each initial |0> state.

\|0> ----\|H\|------  
\|0> ----\|H\|------  
\|0> ----\|H\|------  
\|0> ----\|H\|------  
\|0> ----\|H\|------  
Then we would sample a single value from this state, corresponding to a binary number ranging from \|00000> to \|11111>, which maps to the numbers from 0 to 31. This value, let's call it n, is then mapped to a card by use of the values:  

m = (n % 8) + 3  
a = int(n / 4)  

such that we have: m = 3, 4, ..., 10 and a = 0, ..., 3. Now m indicates the card value and a the suit, so ♣, ♠, ♥ or ♦.  

With these basics out of the way, we are ready to set up our game. First of all, the player and dealer are both dealt 1 card, with no modification to the random number generator. As a short recap, here is how blackjack works:

1. The player plays against the house, or the dealer
2. Both draw a number of cards
3. Whoever gets a higher value without going over 21 wins

Of course, these rules only hold for a 52 card deck, and we only have 32 cards. We found 16 to be a good threshold for our game. Instead of dealing hidden cards, which is usually done in blackjack, QuHackJack simply starts by dealing each side only 1 card. Then the player get's to draw further cards, but this is where the twist comes in. Instead of simply drawing cards according to the random distribution described by the circuit above, they get to modify the circuit. The easiest way to do so is the use of the Hadamard gate. As it is it's own inverse, these two operations are equivalent:  

\|0> ----\|H\|----\|H\|--  
\|0> ----------------  

Hence, we can use the Hadamard gate again to retrieve the \|0> state in any of the qubits. Combining this with the PauliX gate,
which yields \|0> -> \|1> and \|1> -> \|0>,
we can achieve any deterministic bit state, e.g. \|01100> with just a few
Hadamard and PauliX gates. This state for example corresponds to: n = 12;
m = 7; a = 3 or 7♥. While this is all good and fun,
it doesn't really make for compelling gameplay by itself.
Sure the first few times this puzzle is interesting to solve.
The question there becomes, what combination of gates do I need to
get a specific card? But the procedure is always the same,
so even though the first random card requires the player to get
different cards each game, the process doesn't really change.
As a matter of fact, the Hadamard gate is just too easy to use to reverse the game. Furthermore, the unlimited availability of gates for the player doesn't really add to the game either.

As the dealer always sticks with random cards, and plays according to a deterministic algorithm, the player can quickly start winning every single game against the dealer. That doesn't only make it boring, but it also limits the gates the player is familiar with to the bare minimum of Hadamard and PauliX.  

## Part 3: Making the game fun

To address these issues, we decided to add different **gamemodes**. They differ by the set of available gates, and the number of gates the player gets to use during one round of gameplay. Both make for interesting constraints, as the former requires the player to find equivalent combinations of e.g. rotations to achieve a Hadamard gate, and the latter requires the player to consider which qubits they want to apply the gates to for maximal chances of victory. To assist with these challenges, we also include the use of a simulator, which allows the player to see the probabilitiy distribution of cards arising from a certain circuit. Overall, this puzzle game allows the player to learn much more about qubits, quantum gates, their connections and how they can be used for probability distributions.

# Try it for yourself!

It doesn't matter if you have always been interested in quantum computing, or if you just heard about it yourself, QuHackJack is designed as a game for anybody. We have run playtests with many different people, indicating that high school level maths should be enough to get you going with this game. There is also extensive help and info messages within the game, that run you through a few different examples. To play, you will first need to have python 3 and qiskit installed. Then simply clone the repository from [here](https://github.com/KarimAED/2022_microsoft_ionq_challenge), and run QuHackJack.py from a console. If you have any further questions about running the game, you can also read the [readme](https://github.com/KarimAED/2022_microsoft_ionq_challenge/blob/main/README.md) or [contact me](mailto:karimaed@gmx.de).