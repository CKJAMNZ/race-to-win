# Race to Win – Round 2 notes

Game designer: Archer · Producer/tester: Jeff · Build: Round 2 (23 Oct 2026)

## Files
- `index.html` – the whole game in one file. It works when opened straight from disk or from any static host.
- `index-round1.html` – the original Round 1 build, kept as a backup.
- `src/p1.html … p6.js` – the same code split into readable chunks. `index.html` is built by joining them:
  `cat src/p1.html src/p2.js src/p3.js src/p4.js src/p5.js src/p6.js > index.html` (or run `./build.sh`, which does the same)
- `tests/play.js` – headless play-through (Playwright, iPad landscape with touch). `tests/last-run.txt` holds the last result.
- `shots/` – screenshots from the test run.

## What changed
**Look**
- The race view is now a pseudo-3D road in the style of OutRun. It has curves, hills, red and white curbs, a dashed centre line, a checkered start line, and painted zoom pads.
- Each map has its own sky, sun or moon, and a far background that scrolls more slowly than the road (parallax): hills, mesas, a city skyline, or planets.
- Roadside scenery is scaled with distance and changes per map: trees, bushes and signs (Grassland); cacti, rocks and tumbleweeds (Desert); buildings and lamps (City); planets and crystals (Space).
- (Round 3: all cars are now drawn smooth and 3D-style; see "Car names and realistic cars" below.) Cars are drawn in code and seen from behind. They show your paint colour and tire colour by tier. Flames come out when you boost and wings come out when you fly. There are special shapes for the Rocket, Shark, Dragon, Unicorn, Hover, Truck, Bug and Police cars, plus a see-through friendly Ghost Car. One bot is a police-style car with a flashing light.
- Night mode has glowing neon curbs, stars, headlights and lit windows. A blood moon appears on some nights.
- Rain and snow are drawn on screen and change how the car handles.
- The canvas uses devicePixelRatio, capped at about 2.6 million pixels so the iPad stays smooth. The headless test ran at 61 fps.

**Features**
- Garage with stat bars (Speed, Boost, Handling, Grip, Flying) and tabs: Cars, Tires, Boost, Wings, Horns, Paint, My parts.
- In My parts you can equip a part, upgrade it in place (gold → diamond → amethyst → ruby), fix it, patch it, or sell it.
- On the track: coins, zoom pads, and cones or hay bales. Hitting a cone or bale costs tire durability and shows an "OOPS!" pop.
- Drag-to-drive (see below) is now the main control. The big touch buttons (◀ ▶ FLY BOOST BRAKE, plus GO, horn, ITEM, PIT and quit) are an Options choice. Keyboard controls: arrows or WASD, Space = fly, Shift or B = boost, H = horn, E = item, P = pit.
- Press FLY and BOOST together for a big jump over about two cars. If you land on a car, you bounce up and forward.
- Auto-GO is in Options. It is on by default for buttons mode and off by default for drag mode.
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

## Rocket Car Family (23 Oct 2026, live on GitHub Pages since commit `4669d84`)
Six new rocket cars in the garage Cars tab, **$5,000 each**, bought through the one `spend()` check (the button says "Need $X more" and a forced buy is refused). They all have the same stats (speed 1.12, handling 1.1, grip 1.1); only the power and look differ. Each has a nose fin, side fins, a stripe and exhaust colour of its own, and keeps its own body colour (paint doesn't change it).

**One big 🚀 ROCKET button** (above BOOST, or press R; in drag mode: double-tap your car), shown only when you drive a family rocket. It works **once per race** and greys out after you use it. I picked a new button instead of reusing BOOST so BOOST, FLY and the fly+boost jump still work the way Archer knows. I picked once per race because it's the easiest rule for a kid, it matches the Ghost Car's one Ghost Zoom, and choosing when to use it is part of the fun.

| Car | Power | Garage line |
|---|---|---|
| Zoom Rocket (red/gold) | 1.6× top speed for 5 s, plus road magnets pull you back on the road for 10 s | Super ZOOM for 5 seconds, and road magnets keep you on the road for 10! |
| Sky Sparkle Rocket (ice blue) | Hovers high for 5 s, see-through with sparkles; cones and cars can't touch it | Fly up high and go see-through and sparkly - nothing can touch you for 5 seconds! |
| Smoky (grey, Archer's name) | Fluffy smoke-puff cloud for 6 s; cars just behind slow to 60% | Puffs a big fluffy smoke cloud - cars behind you slow down to peek through it! |
| Coin Magnet Rocket (gold) | Grabs every coin near you, any lane, for 8 s | A giant magnet pulls in every coin near you for 8 seconds - cha-ching! |
| Boing Boing Rocket (green) | Three giant bouncy jumps over the cars | Three giant bouncy jumps right over the other cars! |
| Slow-Mo Bubble Rocket (purple) | Every other car goes at half speed for 6 s | Pops a giant bubble - every other car goes slooow-mo for 6 seconds! |

- The original **Rocket Car** is still prize-only for 10 wins in a row. It's labelled "👑 Boss of the Rocket Family!" and has no ROCKET button (its prize is top speed 1.42).
- All the numbers live in the commented `ROCKETS` list (and `ROCKET_PRICE`) in `src/p2.js`. Powers are in `doRocket()`/`update()`/`updateBots()` (`src/p4.js`) and drawn in `drawPlayer()` (`src/p5.js`).
- Nothing violent: powers only slow, float, bounce or collect.

## Drag-to-drive (Round 3, 23 Oct 2026, from Archer's feedback)
Driving is now like pushing a toy car. It's the default for everyone, including old saves. No buttons are needed to drive.
- **Put your finger on your car and push it up the screen to go.** The further you push, the faster you go: a half push means about half speed, and a full push (about a fifth of the screen height) means top speed.
- **Slide left or right to steer.** It's smooth: a small slide means a gentle turn.
- **Pull back to brake. Let go to coast** and slow down gently.
- **Quick flick up = BOOST.** **Tap the car = FLY.** A tap while you're boosting does the big fly+boost jump. **Double-tap = 🚀 ROCKET** on the rocket-family cars. On those cars a single tap waits about a third of a second to check for a second tap, so a double-tap never makes you hop first.
- I chose "tap = fly" and "flick up = boost" because "flick up" and "flick forward" are the same direction on a screen. A tap is the easiest move for a kid, and the flick feels like flinging the car forward.
- **Big touch target:** the whole area around and above the car, down to the bottom of the screen. That's about 680 × 430 px on an iPad, so a thumb anywhere near the car works.
- **First-time hint:** a big "👆 Put your finger on the car and push!" banner with a bouncing arrow. It goes away after your first real push, and that is saved. While you push, a green line from your finger shows the push (it turns red when you brake).
- **Buttons are still there:** Options → *Driving: Drag / Buttons*. *Buttons* brings back all the old touch buttons exactly as before. In drag mode only horn, ITEM, PIT and quit stay on screen.
- **Auto-GO** is saved separately for each mode: it's off by default in drag mode, so it never fights your finger, and on by default in buttons mode, as before. The Auto-GO option changes the setting for the mode you're in.
- **Keyboard** works in both modes: arrows or WASD to drive and steer, plus all the old keys (Space fly, Shift/B boost, R rocket, H horn, E item, P pit). **Mouse drag** works on desktop the same way as a finger.
- Code: `DRAG` state and the throttle/steer/brake mixing in `update()` (`src/p4.js`); the pointer handling, `TUNE` distances and timings, and the hint in the "DRAG-TO-DRIVE" block at the end of `src/p5.js`.

## Car names and realistic cars (Round 3, 23 Oct 2026)
- **Rename:** the red sports car (it was "Red Racer") is now **Mini Bug**, Archer's priority car. The older little $300 starter car (it was "Mini Bug") is now **Little Beetle**.
- **Save safety:** the cars got new ids that were never used before. Old `redracer` becomes `minibug`, and old `bug` becomes `beetle` (`CAR_ID_MAP` in `src/p2.js`, used for v2 saves and old Round 1 saves). A new id never reuses an old one, so nobody's car gets swapped and it is safe to run twice. Your equipped car stays equipped. Tested.
- **Every car is now drawn smooth, like a little 3D toy**, both from behind (race) and from the side (garage, home, Cars tab): round wheels with shiny rims, spokes, a brake disc and a red caliper, plus a sidewall ring in your tyre-tier colour. They have shiny tinted glass with highlight streaks and chrome trim, glowing headlights and tail lights, a third brake light, reversing lights, door seams and handles, mirrors, gradient paint with a shoulder shine and dark edges for roundness, soft shadows, bumpers, exhausts and number plates.
- The special looks are kept: the rocket family keep their own colours, nose fin, side fins, stripe and glowing flame nozzles. The Shark has its fin, the Dragon its green spikes and scales, and the Unicorn its gold horn and rainbow stripe. The Police car has its light bar (it still flashes in races) and its POLICE panel. The Truck has big wheels, springs, a bull bar and an exhaust stack, the Hover car has glowing pads, and the Little Beetle has round fenders and engine louvres. The Ghost Car is still see-through with cute eyes and a wavy skirt. Bots use the same art.
- **Mini Bug has the most detail:** a low wedge body with a rear wing, LED tail lights with dots and a light bar, a third brake light, a demister and rear wiper, a chrome badge, a MINI BUG plate, a carbon diffuser, quad exhausts and rims peeking out with calipers. From the side it has an LED headlight with a DRL, a bonnet line, fender and side vents, a fuel cap, chrome window trim, 10-spoke rims with drilled discs, and tyre lettering. It keeps its red.
- **Performance:** each car is drawn once onto an offscreen canvas at high resolution and cached, with pre-shrunk copies (mips), so a race just copies images (`carSprite`/`carSide` in `src/p3.js`). The headless run still shows 61 fps.

## Open idea from Archer (not built)
- A **quest man** who gives gem pieces by colour: red = ruby, green = emerald, gold = gold, blue = diamond. **Emerald isn't a tier yet**; it's for Round 3.

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
**Round 3 (drag-to-drive, car art, rename), 23 Oct: all 77 checks passed with zero console errors.** The new checks: drag mode is the default and Auto-GO is off, so the car waits for your finger. Drag mode hides the driving buttons, and the touch target is generous. A real touch drag drives the car, and pushing further is faster. Touch drag steers both ways, letting go coasts, and pulling back brakes. A flick boosts, a tap flies, and a double-tap fires the rocket (without a hop). The Options toggle works, and the old buttons come back in buttons mode. A mouse drag drives and steers on desktop, and the keyboard still works in drag mode (arrows, WASD, B, Space). The rename and migration were checked for v2 saves, for v1 saves, and for running twice. All 18 cars render a rear and a side view, and every car drives in a race. Screenshots are in `shots/drag-*.png` and `shots/cars-*.png` (`cars-sheet.png` shows every car from the side and the rear).

**Rocket family run (23 Oct): all 49 checks passed with zero console errors.** The new checks: a rocket can't be bought at $4,999 (the button is grey and a forced buy is refused), and each rocket can be bought at exactly $5,000 with a tap. For each car, the ROCKET button is visible and tapped by real touch, its power does its thing, and a second tap doesn't restart it. A normal car has no ROCKET button. Screenshots are in `shots/rockets-*.png`.

Before that, all 22 Round 2 checks passed, with **zero console errors**. See `tests/last-run.txt`. The checks covered:
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
- Rocket family: the Sky Sparkle car flies about the same height as a normal jump, so it doesn't look very high. The ROCKET button sits close to the mini-map and FLY, so check it with Archer's thumbs. Each power lasts 5–8 s; tune that with Archer.
- The bots follow simple lanes and don't steer around cones.
- Bumps between cars are soft: you just slow down behind a car.
- Drag-to-drive tuning to try with Archer: the push distance for full speed (`TUNE.fullPush`, now 22% of the short screen side), steering sensitivity (`TUNE.fullSteer`), and the flick speed. Check that a tap to fly doesn't happen by accident when a kid just touches the car.
- The cars are drawn smooth now, but the roadside scenery is still pixel art; that mix is on purpose for now.
- Real iPad Safari hasn't been tested yet; only an emulated iPad was used. Please check that it feels like 60 fps and that the button positions suit Archer's thumbs.
- The design doc says gold parts last about 2,000 races, so gold breaks less often than diamond, amethyst and ruby. This is kept as written. Ask Archer if that's intended.
- Rain or snow is rolled when you open the map vote, not per map.
- The race shown on the mini-map is an approximate loop shape, not an exact map.
- Sounds are simple beeps made in code, with no sound files.
