# Contributing

Thanks for sharing an effect! This guide covers what a submission needs, so it can be merged quickly.

## Adding an effect

1. Fork this repository.
2. Add your effect to the [`effects`](effects) folder, as one `.ripple` file.
3. Add a row for it to the effects table in [README.md](README.md), in alphabetical order.
4. Open a pull request. A short screen recording of the effect is very welcome.

## Requirements

**Name**
- The file name is the effect's name, so pick a short and descriptive one.
- Preferably lowercase, without punctuation: `black hole.ripple`, `crt off.ripple`.
- It must not match one of Ripple's default effects (burn, dissolve, glitch, iris, melt, page curl, ripple, tiles, wipe left, zoom blur), or Ripple ignores the file.

**Header**

Start the file with a one-line description and the license, then the padding if the effect draws outside the actor (see [`//@padding`](README.md#things-to-know) in the README):

```glsl
// Cracks the actor into glass shards that spin and fall away.
// SPDX-License-Identifier: MIT
//@padding 20 50 120 50
```

**Code**
- One `vec4 effect(vec2 uv)` function, plus any helpers it needs.
- A "disappear" transition: fully visible at `_progress` 0, fully gone at 1.
- Portable GLSL: `int` loop counters with constant limits, no reserved words such as `half`, no `pow()` with negative bases.
- No external files or textures.
- Reasonably fast. Return early for pixels the effect can't reach, and keep loops short.
- The smallest `//@padding` the effect needs, or none if it stays inside the actor.

## Testing

Before submitting, preview the effect from Ripple's **Library** page and check that:

- it starts fully visible and ends fully gone, with no jump at either end
- it looks right on popups of different sizes, such as the calendar and the Quick Settings menu
- it also works played backwards: the preview plays it backwards when opening, and forwards when you click it to close
- there are no shader errors in `journalctl -f /usr/bin/gnome-shell`

The [README](README.md#writing-effects) explains everything an effect can use.

## License

By submitting an effect, you agree to release it under this repository's [MIT](LICENSE) license.

Only submit effects you wrote yourself, or ports of shaders whose license allows it. Keep the original copyright and license in the file header for ports. Shaders from Shadertoy are licensed CC BY-NC-SA by default, which isn't compatible, so they can't be accepted unless their author licensed them differently.
