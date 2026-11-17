# Balancing Counterweight

How the hidden balance rule works, why the numbers are what they are, and how the design arrived there.

Players are never told the rule. Everything on this page is what happens underneath.

---

## 1. The rule in one sentence

You win a composition when you have painted at least one cell **and** the painting's centre of weight sits close enough to its vertical centre line.

## 2. How weight is calculated

Every coloured area counts: the solid colour blocks the composition starts with, and every cell you have painted. White cells weigh nothing.

```
weight of an area = width × height × colour weight
```

| Colour | Colour weight |
|---|---|
| Blue | **1.25** |
| Red | **1.0** |
| Yellow | **0.6** |
| White | 0 |

The game then finds the **left–right centre of all that weight**:

```
centre of weight = Σ (weight × area's horizontal centre) / Σ weight
off centre       = centre of weight − 320          (320 = middle of the 600 px painting)
```

Only the horizontal position matters. A heavy block at the top of the painting counts the same as one at the bottom. Think of the painting as hanging from a single nail.

When you turn the painting (hold both Shifts), every block moves, so the centre of weight jumps. A painting that is badly off balance one way can be close to balanced after a turn. That is the Shift mechanic doing double duty: it changes the table **and** the weight.

### Worked example

Two areas, on a 600 px wide painting whose centre is at x = 320:

| Area | Size | Colour | Weight | Horizontal centre |
|---|---|---|---|---|
| A | 200 × 200 | blue | 200 × 200 × 1.25 = 50,000 | x = 120 |
| B | 300 × 200 | yellow | 300 × 200 × 0.6 = 36,000 | x = 470 |

Centre of weight = (50,000 × 120 + 36,000 × 470) / 86,000 = **266.5**, which is **53.5 px left of centre**. Yellow B is half as big again as blue A, yet blue still wins.

Repaint B red (weight 300 × 200 × 1.0 = 60,000) and the centre moves to (6,000,000 + 28,200,000) / 110,000 = **310.9**, only **9.1 px left**. That is inside the ±10 px zone of the last compositions, so the painting balances.

## 3. Why these colour weights

The weights come from **visual weight**, a principle from painting and graphic design: darker, more saturated colours feel heavier on the canvas than light ones. In a Mondrian composition the deep blue reads as the heaviest element, yellow the lightest, with red in between. The game's physics was built to match that feeling, so that a balanced painting also tends to *look* balanced.

The values were first chosen by eye. Afterwards they were checked against how dark each colour actually is, using standard perceived luminance (sRGB relative luminance):

| Colour | Hex | Luminance | Darkness (1 − L) | Darkness, red = 1.0 | Game weight |
|---|---|---|---|---|---|
| Blue | `#1E3E93` | 0.058 | 0.942 | **1.12** | 1.25 |
| Red | `#D52A1E` | 0.159 | 0.841 | **1.00** | 1.0 |
| Yellow | `#F3C21A` | 0.577 | 0.423 | **0.50** | 0.6 |

The hand-picked weights track darkness closely. Two deliberate deviations:

- **Blue is pushed a little heavier (1.25 vs 1.12).** Blue blocks dominate a composition more than their darkness alone suggests, and a clearer gap between blue and red makes the choice of colour matter.
- **Yellow is lifted a little (0.6 vs 0.5).** At 0.5 a yellow cell barely moves the balance and painting yellow feels pointless. At 0.6 it is still the light colour, the one you use for small corrections.

The result is a simple strategic layer players can discover on their own: **blue for big moves, yellow for fine-tuning, red in between**, and repainting a cell with a different colour is a way to adjust.

## 4. How close is close enough: the tolerance curve

The allowed distance between the centre of weight and the centre line shrinks as you progress through the nine compositions. The painting is 600 px wide, so ±10 px is about ±1.7% of its width.

| Composition | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 |
|---|---|---|---|---|---|---|---|---|---|
| **Shipped** | **21** | **18** | **16** | **14** | **12** | **11** | **10** | **10** | **10** |

A thin red strip under the seesaw in the corner of the screen shows this zone. It gets narrower each composition, which communicates the rising difficulty without any words.

### How the curve was found

Four versions were playtested:

| Curve | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | Verdict |
|---|---|---|---|---|---|---|---|---|---|---|
| First (fixed) | 18 | 18 | 18 | 18 | 18 | 18 | 18 | 18 | 18 | Felt the same every round |
| Loose | 30 | 30 | 30 | 30 | 30 | 30 | 30 | 30 | 30 | "Too easy" |
| Steep | 20 | 17 | 14 | 11 | 8 | 8 | 8 | 8 | 8 | A wall at No. 5; No. 9 very hard |
| Gentle | 22 | 20 | 18 | 16 | 15 | 14 | 13 | 12 | 12 | Comfortable, slightly soft |
| **Shipped** | 21 | 18 | 16 | 14 | 12 | 11 | 10 | 10 | 10 | Steady ramp, still a challenge at the end |

The steep curve failed because one painted cell can move the centre of weight by more than 8 px, so the last five rounds became fiddly repainting rather than play. The shipped curve sits between steep and gentle: it removes the sudden drop and still ends noticeably harder than it starts.

### Other balance rules

- **At least one painted cell.** Some generated compositions start close to balanced. Without this rule they could be won before the player did anything.
- **No hidden extra conditions.** An early version also required five painted cells without telling the player. The seesaw turned red but nothing happened, which read as a bug. The rule was removed: if the seesaw shows balanced, you win.
- **Checked every frame**, not only after a paint stroke or a turn.

## 5. How a stroke of paint works

| Step | Rule |
|---|---|
| Pick up colour | Only by hitting one of the composition's **solid colour blocks**. Painted cells never colour the ball. |
| Keep colour | Until the ball hits a block of a different colour. It never runs out. |
| Lose colour | A new ball after a drain starts white (colourless). |
| Paint | Linger with a coloured ball inside a white cell. Larger cells take longer. |
| Repaint | Linger in a painted cell with a different colour. |

## 6. Design decisions along the way

Counterweight went through five gameplay versions in two days. The version players get is the first one, kept and polished.

| Version | Idea | Outcome |
|---|---|---|
| v1 | Mondrian grid, hit blocks to pick up colour, linger to paint, balance left and right, hold both Shifts to turn | **Kept.** Most fun from the start |
| v2 | The whole table tilts with its weight; shapes float in zero gravity and get pushed | Cut: too much empty space, read as a physics toy rather than a painting |
| v1.1 | Dotted target cells to fill in a specific colour; turns earned by finishing cells; double-tap to turn; two kickers | Cut: too complicated, and filling a specific colour was too hard |
| v1.2 | Paint every white cell to win | Cut: lost the balance idea |
| v1.3–1.4 | Visible weights (×3/×2/×1, then grams), a centre line, rules explained on the start page | Cut: made the goal a chore |

The main lesson: **v1 worked because the goal stays in the background.** Most of the time you are flipping, bouncing, painting and turning; balance arrives as a surprise ("oh, it balanced"). Every later version pulled the goal into the foreground and the pinball became work. So the rules stay hidden, and only presentation was changed after v1: typography, the pause menu, the nine-composition structure, the collection screen, sound, and the difficulty curve above.

A finale was also prototyped and cut: composition No. 9 as a wall of your own eight paintings, which you hang and take down to balance the exhibition. It was too hard and added a new mechanic at the very end.

## 7. Making sure every composition is playable

Compositions are generated, so three automated checks run after any change to the generator or physics:

| Check | Method | Result |
|---|---|---|
| Every doorway is real | Fire a ball through the middle of every gap in hundreds of compositions | 0 blocked. Early versions had 36% fake gaps that opened onto a solid block |
| The ball stays on the table | Place the ball inside blocks that touch the frame | 0 escapes |
| The ball is never trapped | Drop a resting ball in every open cell, allow up to 4 turns | 0 trapped; the worst case needs 2 turns |

Turning the painting also nudges a resting ball sideways toward the nearest doorway in the floor it will land on, so it does not drop dead straight onto another flat ledge. Otherwise a resting ball never moves on its own.

## 8. All tunable numbers

| Constant | Value | Meaning |
|---|---|---|
| `WEIGHT` | blue 1.25, red 1.0, yellow 0.6 | Colour weights |
| `TOL_CURVE` | 21 18 16 14 12 11 10 10 10 | Balanced zone per composition (px) |
| `GOAL` | 1 | Minimum painted cells |
| `TOTAL` | 9 | Compositions per run |
| `G` | 1500 px/s² | Gravity |
| Physics step | 1/720 s | Substep length |
| `ROT_DUR` | 0.55 s | Length of the turn animation |
| `CHARGE` | 0.45 s | How long to hold both Shifts to turn |
| `ROT_COOLDOWN` | 1.2 s | Pause before the next turn |

All of them live near the top of `index.html`.
