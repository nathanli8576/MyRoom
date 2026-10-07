
Each room is entirely self-contained. You can copy a single file out into another project and it will still work — as long as it's loaded from an origin where `localStorage` is available, it has no external references.

The **back arrow** in the top-left of every room links to `index.html`. If you rename the hub, update that link.

---

## LocalStorage keys

Each room keeps its state under its own key so nothing collides:

| Key | Owner | Purpose |
|---|---|---|
| `thought-decay:forest:v2` | Thought Decay | Trees, seed keys, release count |
| `thought-decay:muted` | Thought Decay | Mute preference |
| `unsent-letters:v2` | The Unsent Letter | Letters in orbit |
| `letter:muted` | The Unsent Letter | Mute preference |
| `burn:muted` | Burn It | Mute preference |
| `worry-jar:v2` | The Worry Jar | Sealed worries and their outcomes |
| `constellation:v3` | A Constellation | Stars, connections, cluster names |
| `constellation:prefs:v1` | A Constellation | UI preferences |
| `constellation:muted` | A Constellation | Mute preference |
| `scream:muted` | Scream Into the Void | Mute preference |
| `bubblewrap:total` | Bubble Wrap | Lifetime pop count |
| `bubblewrap:muted` | Bubble Wrap | Mute preference |
| `glass:muted` | A Pane of Glass | Mute preference |
| `pillow:muted` | The Pillow | Mute preference |
| `pillow:pressure` | The Pillow | Last slider value |
| `wtm:v1` | Will This Matter? | Saved predictions |
| `wtm:muted` | Will This Matter? | Mute preference |
| `the-room:visited:v1` | Hub | Which rooms you've opened |

A "reset everything" button would just loop over these and call `localStorage.removeItem`.

---

## Design principles

A few rules that shaped every room, in case you want to add one:

1. **One verb per room.** Pick up an object, do one thing with it, put it down. No dashboards, no menus, no progress bars that reset daily.
2. **A physical metaphor that does the therapeutic work.** The point of Thought Decay isn't the tree — it's that you watch your own words dissolve and become something else.
3. **Nothing leaves the browser.** No accounts, no servers, no analytics, no images loaded from anywhere. Every visual is drawn on a canvas or built from CSS.
4. **Reduced-motion is respected.** Every room checks `prefers-reduced-motion` and disables large-scale motion while keeping the interaction intact — and while keeping every feature reachable: The Pillow still awards feathers and XP, just with calmer particles. The 4-7-8 breathing guide in Thought Decay, for example, still advances its text cue but stops pulsing the circle. The hub follows the same rule: its live card previews draw a single static frame instead of animating.
5. **Audio is gesture-gated and optional.** No sound plays until you interact. Every room that makes noise has a mute button that remembers your choice. The AudioContext is created lazily on the first click or keypress, never on page load.
6. **A back arrow and a mute button live in the same two corners** across every room, so the interface is predictable.

---

## Accessibility notes

- All interactive controls are real `<button>`, `<input>`, or `<a>` elements — keyboard reachable and screen-reader friendly. The canvas rooms expose keyboard paths too: focus the pane and press Enter/Space in A Pane of Glass, use the Place button / focused canvas in A Constellation, hold the button in Scream Into the Void, pop bubbles with Enter/Space, and open letters, ripe worries and due check-ins from real buttons.
- The seed picker in Thought Decay, the duration picker in The Worry Jar, and the shape picker in A Pane of Glass are proper radiogroups with roving tabindex and arrow-key navigation.
- Dialogs (the letter reader, the worry resolver, the check-in modal, the Constellation panel) use `role="dialog"` + `aria-modal`, move focus in, restore it on close, close on Escape, and mark the background `inert` while open.
- Live regions (`aria-live="polite"`) announce status changes in Thought Decay, The Worry Jar, A Constellation, and Will This Matter?, plus state transitions in Burn It, Bubble Wrap, A Pane of Glass, Scream Into the Void, and milestones in The Pillow.
- Every icon-only button has an `aria-label`. Mute toggles keep a constant accessible name ("Mute sound"), expose state through `aria-pressed`, and carry a dynamic tooltip.
- Focus outlines are visible on every focusable element via `:focus-visible` — including canvases you can act on with the keyboard.

If you extend a room, keep these in place.

---

## Browser support

Tested in current Chrome, Safari, Firefox, and Edge on desktop and mobile.

A few rooms use APIs that require reasonable support:

- `visualViewport` — Thought Decay's mobile keyboard handling. Falls back gracefully if unavailable.
- `inert` — several rooms hide closed panels, inactive phases and modal backdrops from the tab order (Thought Decay, The Unsent Letter, The Worry Jar, Will This Matter?, A Constellation). Older browsers simply won't skip them, but nothing breaks.
- `IntersectionObserver` — The Room's preview gating. If missing, all previews animate at once (still fine).
- Web Audio API — every room that makes sound. If unavailable, silent but functional.

No transpilation. The code is plain ES2020+ JavaScript.

---

## Adding a new room

1. Copy any existing room file as a starting template.
2. Keep the two top corners occupied: back arrow top-left, mute top-right (if it makes sound).
3. Add an entry to the `ROOMS` array at the top of `index.html`'s script with an `id`, `name`, `accent`, `desc`, and an inline `icon` SVG.
4. If you want a live preview on the hub, add a function to the `PREVIEWS` object keyed by the same `id`. The signature is `(ctx, width, height, timeInSeconds)`. It runs every frame while the card is on screen.
5. Namespace any `localStorage` keys as `your-room:something:v1` so they don't collide.

---

## Philosophy

These are small rituals. They aren't therapy, they don't diagnose anything, and they aren't a substitute for care. What they can do is give you ninety seconds of doing one deliberate thing, which is sometimes enough to shift the next ten minutes.

If you use them, use them slowly.

---

## License

Do whatever you want with this. Attribution is nice but not required.