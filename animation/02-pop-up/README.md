# Popup

In this exercise we have set up a simple pop-up dialog for you. It already works! Load up index.html and give it a shot!

You don't need to worry about the actual functionality here; we've just written a little javascript that adds and removes a `.show` class to the popup and the backdrop.  Your task then is to make it _move_, as in the desired-outcome image below.

### Hints
- "modal" is another word for 'pop-up'
- In the code we've provided, the popup is sitting in its final position. You'll need to change its initial position and then use a transition to move it back to the center.
- You might want to change the initial opacity from 0% to something like 20% while you're working on it, so you can easily see where it is coming from before you click the button.
- Don't overthink this one... it might seem complicated, but it requires just a few lines of code.

## Desired Outcome

![outcome](./desired-outcome.gif)

### Self Check

- The pop-up slides down into position when you click the open button and slides back up when you click 'close modal'
- The opacity fades smoothly in and out when toggling the modal

## My Solution vs the Official Solution

The two versions look alike, but they work quite differently underneath. The official solution is the better base. Using `visibility` in mine is actually an improvement on it.

### Add to the provided code rather than rewriting it

The official solution keeps all of the provided `style.css` and adds 7 lines. I rebuilt the stylesheet from scratch, and a few things got lost along the way:

- **The backdrop no longer works.** `.backdrop` has no styles, so it's an empty `<div>` and the page never dims when the modal opens.
- **The reset is gone.** Without `* { margin: 0 }`, `body` keeps its default 8px margin. Combined with `height: 100vh`, the page ends up 16px taller than the window and gets a scrollbar.
- **`box-sizing` on `body` only affects `body`.** It isn't inherited, so it does nothing for the modal.

**Best practice:** when a task says "make it move", change as little as possible. Smaller changes are easier to review and less likely to break something else.

### Point by point

| | Mine | Official | Better and why |
|---|---|---|---|
| **Centring** | `body` is flex, modal is `position: absolute` with no `top`/`left` | `position: fixed; top/left: 50%; translate(-50%, -50%)` | **Official.** Mine only works because a positioned child of a flex container with no offsets gets centred by `justify-content`/`align-items`. That's a subtle side effect: it breaks if `body` stops being flex, and `absolute` scrolls with the page. A `fixed` element is positioned against the viewport, which is what a modal needs. |
| **Transition** | `transition: 600ms 100ms;` | `transition: transform 0.3s ease-in-out, opacity 0.4s ease;` | **Official.** Leaving out the property name means `all`, so any property that changes later will animate, including ones I didn't intend. Naming each one is clearer and cheaper. The 100ms delay also makes the button feel slow, especially on close. |
| **`.show` selector** | `.show { … }` | `.popup-modal.show { … }` | **Official.** `.show` and `.popup-modal` have the same specificity, so mine only works because `.show` comes later in the file. It also applies to the backdrop, which gets the same class. Chaining the two classes makes the rule specific to the modal and independent of order. |
| **Hiding** | `visibility: hidden` + `opacity` | `opacity: 0` + `pointer-events: none` | **Mine.** In the official version the hidden modal is invisible but still there: you can Tab to the "Close Modal" button and screen readers still read it out. `visibility: hidden` removes it from both. `visibility` also animates in a useful way: on close it stays visible until the transition ends, so the fade-out still plays. |
| **Offset** | `translateY(-50px)` | `translate(-50%, -100%)` | Both are fine. A percentage scales with the modal's size; pixels give a fixed distance. It's a design choice. |
| **Layout** | `display: grid; grid-template-rows: 0.2fr 0.2fr 2fr` | Normal block flow | **Official.** Stacking a heading, a paragraph and a button is what normal block layout already does, so grid adds complexity for no benefit. I also set `justify-content` twice. |
| **Border** | `border: black solid` | `1px solid black` | Without a width you get `medium` (about 3px), which probably isn't what I wanted. Always give a width. |

### Combining the best of both

Start from the official approach and add `visibility`. Because `visibility` is listed explicitly, it still animates without falling back to `all`:

```css
.popup-modal {
  visibility: hidden;
  transform: translate(-50%, -100%);
  transition:
    transform 0.3s ease-in-out,
    opacity 0.4s ease,
    visibility 0.4s;
}

.popup-modal.show {
  visibility: visible;
  transform: translate(-50%, -50%);
}

/* Optional: respect users who've asked for less motion */
@media (prefers-reduced-motion: reduce) {
  .popup-modal { transition: opacity 0.2s, visibility 0.2s; }
}
```

### Main takeaways

1. Name the properties in `transition` instead of relying on `all`.
2. Scope state classes by chaining them (`.popup-modal.show`), not as a bare `.show`.
3. Use `position: fixed` plus `translate` to centre modals, rather than relying on how the parent is laid out.
4. Use `visibility` alongside `opacity` so hidden elements can't be reached with the keyboard or by screen readers.
