# Ballpark

**The closest-guess party game.** 2 to 8 players, each on their own phone or computer.

**Play it:** https://ranjanvivek047-bot.github.io/ballpark/

Everyone gets the same question with a number for an answer ("How tall is Mount Everest, in meters?"). Nobody knows it exactly. Lock in your best guess before the timer runs out, then see who got closest.

## How to play

1. **Get in a room.** One player hosts and shares the 4-letter room code (or the invite link). Everyone else joins from their own device.
2. **Guess the number.** Type a guess and lock it in before the timer ends. Guesses stay hidden until the reveal.
3. **See who's closest.** When everyone has locked in, or time runs out, the answer is revealed on a number line.

### Scoring

| Points | For |
| --- | --- |
| +3 | Closest guess |
| +1 | Second closest (games with 3+ players) |
| +2 | Bullseye bonus: within 10% of the answer, on top of anything else |

Tied guesses share the higher place. No guess, no points. After the last round (the host picks 5, 7 or 10) the highest total wins, and ties share the win.

## How it works

- A static site with no server of its own. Plain HTML, CSS and JavaScript, no build step.
- Devices talk through a public MQTT relay over secure WebSockets (EMQX, with HiveMQ and Mosquitto as fallbacks).
- The host's browser runs the game: it receives each player's actions, applies the rules in `site/rules.js`, and publishes one shared game state that every screen renders.
- Guesses and answers are never sent to other players until the reveal.
- Refreshing the page keeps your seat. That works for the host too.
- If the host's phone sleeps or drops offline, the round waits for everyone's guesses instead of ending early.
- 60 questions, each checked against at least one reputable source.

## Run it locally

```bash
cd test
npm install
npm run serve        # http://localhost:8765
```

Open two windows (one normal, one private) to play against yourself.

## Tests

```bash
cd test
npm test             # scoring rules
npm run e2e          # full game across several real browser sessions (needs Google Chrome)
npm run resilience   # host page freezing mid-round, a player leaving mid-round, decimal commas
```

The end-to-end test plays a 5-round game with three players and checks every scoring rule. It also covers joining by code and by invite link, hidden guesses, the timer, a tied round, refreshing mid-game for both a player and the host, the 8-player limit, leaving, and closing the room.
