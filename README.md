# Ripple Gallery

A collection of community effects for [Ripple](https://github.com/nick-redwill/Ripple), the GNOME Shell extension that adds shader transitions to popups, wallpaper changes and the lock screen.

Every effect is a single `.ripple` file you can drop into Ripple.

## Effects

| Effect | Description |
|---|---|
| [Black Hole](effects/black%20hole.ripple) | Pulls the actor into a spinning black hole that opens at the pointer |
| [Blinds](effects/blinds.ripple) | Closes the actor like window blinds, slat by slat |
| [Crt Off](effects/crt%20off.ripple) | Switches the actor off like an old CRT television |
| [Disintegrate](effects/disintegrate.ripple) | Crumbles the actor into dust that drifts away |
| [Flip](effects/flip.ripple) | Flips the actor away like a card turning on its side |
| [Hexagons](effects/hexagons.ripple) | Breaks the actor into hexagons that spin away, starting at the pointer |
| [Ink](effects/ink.ripple) | Dissolves the actor like ink spreading through water |
| [Prism](effects/prism.ripple) | Splits the actor into its colors, which drift apart and fade |
| [Shatter](effects/shatter.ripple) | Cracks the actor into glass shards that spin and fall away |

## Installing effects

1. Download the `.ripple` files you like from the [`effects`](effects) folder.
2. Open Ripple's preferences, go to **Library** and click **Open Effects Folder**, or open `~/.config/ripple_effects` yourself.
3. Copy the files there. They show up under **My Effects** right away, no restart needed.
4. Select them for the wallpaper, popups or lock screen on the **General** page.

## Writing effects

An effect is a plain GLSL file that defines one function:

```glsl
// Fades the actor out.
// SPDX-License-Identifier: MIT
//@padding 0 0 0 0

vec4 effect(vec2 uv) {
    return pixel(uv) * (1.0 - _progress);
}
```

The file name is the effect's name: `fade.ripple` becomes **Fade** in Ripple.

### What an effect can use

| Name | Description |
|---|---|
| `uv` | Position in the actor, `0..1` across it. Values outside that range are the space around it |
| `pixel(uv)` | Color of the actor at `uv`, transparent outside of it |
| `_progress` | `0` = fully visible, `1` = gone |
| `_random` | A random number in `0..1`, new for every transition |
| `_resolution` | Size of the actor in pixels |
| `_mouseUV` | Where the pointer was when the transition started, in the same coordinates as `uv` |
| `_mouse` | The same position on the screen, in pixels |

### Things to know

- **Write effects as "disappear" transitions.** Ripple plays them backwards when something appears, so an effect that breaks a popup apart also assembles it when it opens.
- **Effects can draw outside the actor with `//@padding`.** By default an effect only draws inside the actor. Add a `//@padding` line near the top of the file to also draw around it, so particles and shards can fly away from a popup.

  It takes 1 to 4 values in the same order as CSS padding: `all`, `vertical horizontal`, `top horizontal bottom` or `top right bottom left`. Top and bottom are percentages of the actor's height, left and right of its width, so `//@padding 20 50 120 50` allows 0.2 heights above, half a width on each side and 1.2 heights below.

  Add a `d` to a value to measure it from the actor's diagonal instead, for effects that rotate or fold the actor: `//@padding 100d 0 0 100d` always leaves room for it to turn around a corner, whatever its shape.

  The padding never goes past the monitor, and `uv` outside `0..1` is that space around the actor. The texture grows with the padding, so only pad the sides your effect reaches, as little as it needs. Only the first `//@padding` line counts.

  To see the area while testing, turn on **Enable Preview Outline** on the **Library** page. The wallpaper and lock screen cover the whole monitor, so there is nothing around them.
- **Keep shapes round.** `uv` is stretched to the actor's size. Multiply by `vec2(_resolution.x / _resolution.y, 1.0)` to work in units of the actor's height, as most effects here do.
- **Colors are premultiplied.** `pixel()` returns premultiplied alpha, so to fade something multiply the whole color, as in `col * alpha`, and tint with `col.a * tint`.
- **Write portable GLSL.** Use `int` loop counters with constant limits, avoid reserved words such as `half`, and don't pass negative bases to `pow()`. Code that works on one driver can fail on another.
- **Keep it fast.** For popups, an effect runs on every pixel of the actor and its padding, every frame. Return early for pixels your effect can't reach, and keep loops short.

### Testing

Ripple's preferences have an editor with a **Preview** button: go to **Library**, click **New Effect**, paste your code and preview it. Click the preview popup to play the effect again in the other direction.

The preview can't show shader errors. A broken effect shows nothing during the animation, then the popup suddenly appears. To see the error, run:

```bash
journalctl -f /usr/bin/gnome-shell | grep -i -A5 shader
```

and preview again.

## Contributing

New effects are very welcome. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

All effects are released under the [MIT](LICENSE) license, unless a file says otherwise.
