# Dropdown Menu

We've set up a dropdown menu in this exercise. Load up the page, you can see a single menu title, with a dropdown menu that will open upon clicking on the title. 

Your task is to add animation to the dropdown menu so that it will have an effect of expanding. Check out the desired outcome below, and notice the _bounce_ illusion when the dropdown expands close to its final end state. 

### Hints
- You need to specify a _transform-origin_ property to make the dropdown menu start transforming from the top
- You need to add an intermediate step to the keyframe at rule to implement the _bounce_ illusion.

## Desired Outcome

![outcome](./desired-outcome.gif)

### Self Check

- The dropdown menu expands after you click on the menu title
- There's a _bounce_ illusion towards the end of the animation

## My Solution vs the Official Solution

The animation itself is identical to the official one: the same `@keyframes expand` with a 70% overshoot to `scaleY(1.1)`, the same `transform-origin: top`, and the same `500ms ease-in-out`. The differences are all in the surrounding code, because I rewrote the starter stylesheet again instead of adding to it.

### Add to the provided code rather than rewriting it

The official solution keeps `style.css` as it is and adds one rule plus the keyframes. Rewriting didn't break anything this time, but it led to layout code that works against itself:

- `.dropdown-container` became a flex column with `align-items: center`. That shrinks the children to fit their content, so I then needed `width: 100%` on `.menu-title` and `.dropdown-menu` to stretch them back out. In normal block layout they're already full width.
- `height: 100%` on the container does nothing, because `body` has no set height to take a percentage of.
- `margin: 40px auto` was dropped, so the menu now sits against the top of the page.

### Point by point

| | Mine | Official | Better and why |
|---|---|---|---|
| **Animation** | `@keyframes expand` 0% → 70% `scaleY(1.1)` → 100%, `transform-origin: top` | Same | **Equal.** Identical, and the correct approach. `transform-origin: top` makes it grow downwards from the title instead of from the middle, and the 70% step overshoots before settling back to create the bounce. |
| **Hiding** | `visibility: hidden` + `display: none` | `display: none` | **Official.** In exercise 2, `visibility` was needed because `opacity: 0` leaves the element on the page. `display: none` already removes the menu completely, from the layout, the Tab order and screen readers, so `visibility` adds nothing here. The lesson is to know *why* a technique works rather than carry it over by habit. |
| **Width** | `width: 30%; max-width: 300px` | `max-width: 250px` | **Official.** `30%` depends on the screen size: on a 375px-wide phone the menu is only about 112px wide. `max-width` on its own lets a block fill the available space up to a limit, so it works on every screen. |
| **Centring** | `body` is flex with `justify-content: center`, and the container is a flex column | `margin: 40px auto` | **Official.** Auto horizontal margins centre a block that has a width limit, with no flexbox needed. It's one line, and it also sets the gap at the top. |
| **Item height** | `padding: 1rem 0` | `line-height: 50px` | **Mine.** A fixed `line-height` only works while the text fits on one line: if an item wraps, each line is 50px tall and the item doubles in height. Padding in `rem` also scales with the user's browser font size, so the menu stays in proportion. |
| **Hover selector** | `.dropdown-menu > li:hover` | `ul.dropdown-menu li:hover` | **Mine, slightly.** Adding `ul` makes the selector more specific (harder to override later) and ties it to that element. `>` only matches the menu's own items, not any nested lists. |
| **`overflow: hidden` on `body`** | Added | Not present | **Official.** It stops the whole page from scrolling, so on a short screen part of the menu becomes unreachable. Only use it when there's a specific reason. |
| **`box-sizing` on `body`** | Added | Not present | **Neither.** As in exercise 2, `box-sizing` isn't inherited, so this only affects `body`. To apply it everywhere, use `*, *::before, *::after { box-sizing: border-box; }`. |
| **`.visible` selector** | `.visible` | `.visible` | **Equal.** Both use the starter's class name, so this is fine. As in exercise 2, `.dropdown-menu.visible` would be more robust because it doesn't rely on coming later in the file. |

### Worth knowing: why it doesn't animate on close

In both versions the menu animates open but disappears instantly on close. Removing `.visible` switches back to `display: none` straight away, and nothing can animate an element that is no longer displayed. `@keyframes` suit one-off effects like this bounce. To animate in both directions, use a `transition` together with `visibility`/`opacity`/`transform`, as in exercise 2.

### Main takeaways

1. The animation was spot on. `transform-origin` and an intermediate keyframe are exactly what this exercise is testing.
2. Build on the starter code. Most of the extra code here was fixing problems my own rewrite created.
3. Use `max-width` with `margin: auto` for widths that adapt to the screen, rather than a percentage.
4. Only use a technique when it's needed: `visibility` was needed in exercise 2 but not here.
5. Using `rem` padding instead of a fixed `line-height` was a good call.
