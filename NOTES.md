# Race to Win – Round 2 notes

Game designer: Archer · Producer/tester: Jeff · Build: Round 2 (23 Oct 2026)

## Files
- `index.html` – the whole game in one file. It works when opened straight from disk or from any static host.
- `index-round1.html` – the original Round 1 build, kept as a backup.
- `src/p1.html … p6.js` – the same code split into readable chunks. `index.html` is built by joining them:
  `cat src/p1.html src/p2.js src/p3.js src/p4.js src/p5.js src/p6.js > index.html`
- `tests/play.js` – headless play-through (Playwright, iPad landscape with touch). `tests/last-run.txt` holds the last result.
- `shots/` – screenshots from the test run.

## What changed
**Look**
- The race view is now a pseudo-3D road in the style of OutRun. It has curves, hills, red and white curbs, a dashed centre line, a checkered start line, and painted zoom pads.
- Each map has its own sky, sun or moon, and a far background that scrolls more slowly than the road (parallax): hills, mesas, a city skyline, or planets.
- Roadside scenery is scaled with distance and changes per map: trees, bushes and signs (Grassland); cacti, rocks and tumbleweeds (Desert); buildings and lamps (City); planets and crystals (Space).
- All cars are pixel-art sprites drawn in code and seen from behind. They show your paint colour and tire colour by tier. Flames come out when you boost and wings come out when you fly. There are special shapes for the Rocket, Shark, Dragon, Unicorn, Hover, Truck, Bug and Police cars, plus a see-through friendly Ghost Car. One bot is a police-style car with a flashing light.
- Night mode has glowing neon curbs, stars, headlights and lit windows. A blood moon appears on some nights.
- Rain and snow are drawn on screen and change how the car handles.
- The canvas uses devicePixelRatio, capped at about 2.6 million pixels so the iPad stays smooth. The headless test ran at 61 fps.

**Features**
- Garage with stat bars (Speed, Boost, Handling, Grip, Flying) and tabs: Cars, Tires, Boost, Wings, Horns, Paint, My parts.
- In My parts you can equip a part, upgrade it in place (gold → diamond → amethyst → ruby), fix it, patch it, or sell it.
- On the track: coins, zoom pads, and cones or hay bales. Hitting a cone or bale costs tire durability and shows an "OOPS!" pop.
- Big touch buttons (◀ ▶ FLY BOOST BRAKE, plus GO, horn, ITEM, PIT and quit) and keyboard controls: arrows or WASD, Space = fly, Shift or B = boost, H = horn, E = item, P = pit.
- Press FLY and BOOST together for a big jump over about two cars. If you land on a car, you bounce up and forward.
- Auto-GO is in Options and is on by default.
- The results screen shows a money breakdown and new unlocks. When you win, you get a trophy and pick one part out of three.
- The Ghost Car gets one "Ghost Zoom" per race that passes through cars, and it has a weird honk.
- You must beat the practice test before real races unlock. Before each race you vote for a map.

**Round 2 (from the design doc)**
- Breaking parts: each race may break a part part-way through. A broken part slows you and drops you back a little. A broken ruby part drops to amethyst instead of breaking. Last place gets a broken tire.
- Pit stop: tap PIT and you stop at the next lap line. Fuel costs $10, a tire $100, a ruby tire $1,000. If you can't afford something, it is skipped, so money never goes negative.
- Ruby gem patch: fixes any broken part. Buy one for $250 or win one on the wheel. A pit stop uses a patch first if you have one.
- Qualifying lap: a solo lap where the fastest lap starts at the front. You can skip it and start at the back.
- Lucky blocks ("?" boxes) give a 20-second shield, a goo blob, a zoom, cash, or sometimes a part. The shield blocks every hit and fixes your tires.
  - The harpoon became a friendly **goo blob**: the car ahead gets sticky and slows to 55% for 2.5 seconds. Nothing gets hurt.
- Rare horn: the ruby Freeze Horn or Ice Horn freezes the car in front for 3 seconds, once per race.
- Night mode and blood moon: tap the blood moon 100 times (the count is saved) to go 2.5% faster forever. Options lets you set night races to Auto (25% chance), Always or Never.
- Mini-map: other racers' colours are shuffled every race, so you can't tell who's who.
- Also done early from Round 3: weekly trophy pay, the rainbow trophy giving luckier spins, and the Rocket Car for 10 wins in a row.

**Safety**: No violence or scary content. The ghost is friendly. There is no real money, nothing online, no chat, and no personal information. Everything is saved only on the iPad (localStorage).

## ECONOMY in plain words (the `ECONOMY` object at the top of the code)
- You start with **$1,000**. Archer also starts with the Ghost Car, as the design doc says.
- Starter cars cost **$300–$700**. For example, the Zoomer costs $450 and a gold tire $300, which leaves $250. The next car (Lightning Bolt) costs **$1,500**, then $2,200, $3,000, $4,500, $7,000 and $10,000. The Rocket Car can't be bought; you win it with 10 wins in a row.
- Parts cost **$100 normal / $300 gold / $800 diamond / $2,000 amethyst / $5,000 ruby**. The second style of each part costs 20% more.
- Upgrading in place costs the next tier's price minus half of the current price.
- Race prizes: **1st $200, 2nd $120, 3rd $80, 4th $50**, multiplied by the map bonus: Grassland ×1, Desert ×1.3, City ×1.7, Space ×2.2. Each lap beyond 2 adds +25%. Each coin is $5. Pole position is +$25. Passing the practice test pays $100 once.
- A typical Grassland win pays about $250–$300. The next useful upgrade usually takes **about 3–6 races**.
- Selling gives back **50%** of the price.
- Daily reward: **$100** plus one wheel spin. Paint costs $50.
- Weekly trophy pay: normal $50, gold $100, diamond $120, amethyst $150, ruby $200 per trophy every 7 days.
- Trophies sell for $50 to $20,000; a ruby trophy sells for $20,000, as the doc says.
- **You can never buy what you can't afford.** Every purchase goes through one `spend()` check, and buy buttons turn grey and say "Need $X more". The test proved that a $1,500 car can't be bought with $1,000, even when the button is bypassed.

## LUCK in plain words (the `LUCK` object)
- The wheel and the lucky blocks use **one table**. Each prize has "tickets in a hat" for the wheel and for the blocks.
- Wheel odds (shown on screen in kid words):

  | Prize | Odds |
  |---|---|
  | $50 | lots, 1 in 4 |
  | Normal part | 1 in 4 |
  | $150 | often, 1 in 8 |
  | Gold part | often, 1 in 8 |
  | Free paint | 1 in 10 |
  | Ruby patch | sometimes, 1 in 17 |
  | Diamond part | sometimes, 1 in 17 |
  | Amethyst part | rare, 1 in 33 |
  | **Ruby part** | **super rare, 1 in 100** (Archer's rule) |

- Lucky blocks: shield 1 in 4, goo blob 1 in 4, zoom 1 in 5, $25 about 1 in 7, gold part 1 in 12, diamond 1 in 25, amethyst 1 in 50, **ruby 1 in 100**.
- Pity timer: after 8 wheel spins with no diamond or better, the next spin is a guaranteed diamond or amethyst part. Ruby is never given by this timer. A separate ruby timer guarantees a ruby only after 100 spins without one. The lucky meter on the wheel screen shows how close you are.
- Rainbow trophy (gold + diamond + amethyst + ruby trophies): gold comes up ×2, diamond ×1.6 and amethyst ×1.4 as often. Ruby stays super rare.

## DIFFICULTY in plain words (the `DIFFICULTY` object)
- Bot speed is measured against a starter car's top speed (1.0 = the same).
  - Practice bot: **0.70**, so it's easy to beat on the first or second try.
  - Grassland bots: 0.76–0.86.
  - Desert: 0.82–0.93.
  - City: 0.88–1.00.
  - Space: 0.95–1.08, so you need upgrades to win there.
- Options: Easy ×0.88, Normal ×1.0, Hard ×1.1. These don't change the practice bot.
- Gentle rubber-banding: a bot ahead of you goes at most 6% slower, and a bot behind you goes at most 4% faster. Bots also slow a little on bends.
- Laps: practice 2, Grassland 2, Desert 2, City 3, Space 3.

## Save
- The game saves to localStorage key `raceToWin_v2`.
- An old `raceToWin_v1` save (Round 1) is carried over automatically: money, wins, races, practice pass, cars, parts and trophies. Rims were removed, so they are refunded at 50%. The test checked this.

## Test results (headless Chromium, iPad landscape 1180×820 @2x with touch)
All 22 checks passed, with **zero console errors**. See `tests/last-run.txt`. The checks covered:
- the old save migrating
- the too-poor purchase being refused
- buying a car and a part
- the practice race finishing and passing
- the results screen
- the wheel spin and its once-a-day limit
- ruby odds of about 1%, the pity timer and the ruby timer
- the qualifying lap and grid
- the freeze horn, the goo blob, a pit stop charging $110, the shield fixing a tire, a blood moon tap, and a lucky block
- a full Desert night race won with the prize pick
- the touch steering buttons

## What's next
- **Round 3:** rocket car upgrade path (rocket pieces), quest realm with gem helpers, rewind prize, and more cars and tracks.
- **Round 4:** friend races and Dad Mode (wobbly wheel) on the same iPad, then trading. Trading needs an offline design, such as pass-and-play.

## Known issues / things to tune with Archer
- The bots follow simple lanes and don't steer around cones.
- Bumps between cars are soft: you just slow down behind a car.
- Real iPad Safari hasn't been tested yet; only an emulated iPad was used. Please check that it feels like 60 fps and that the button positions suit Archer's thumbs.
- The design doc says gold parts last about 2,000 races, so gold breaks less often than diamond, amethyst and ruby. This is kept as written. Ask Archer if that's intended.
- Rain or snow is rolled when you open the map vote, not per map.
- The race shown on the mini-map is an approximate loop shape, not an exact map.
- Sounds are simple beeps made in code, with no sound files.
