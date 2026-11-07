# Topside Mountain View

An animated background for [Omarchy](https://omarchy.org). A scout on a ledge at dusk, looking out over
the mesas: his coat moving in the wind, a radio mast blinking on the far cliff, a drone on patrol, a
convoy crawling through the fog in the valley. Now and then he kneels to watch the road through
binoculars, then walks off the ledge and later walks back.

> **Animated with [omarchy-live-wallpaper](https://github.com/Gleasonmt/omarchy-live-wallpaper).**
> A background, not a theme: it goes with whatever theme you use.
![Topside Mountain View, moving](media/topside-mountain-view.webp)

## Install

You need the [live-wallpaper plugin](https://github.com/Gleasonmt/omarchy-live-wallpaper), 1.2.0 or later.
Install the plugin and this background together:

```bash
omarchy plugin add https://github.com/Gleasonmt/omarchy-live-wallpaper --enable --yes
git clone --depth 1 https://github.com/Gleasonmt/omarchy-topside-mountain-view-theme ~/.config/omarchy/live-backgrounds/topside-mountain-view && omarchy restart shell
```

If you've installed the plugin's optional `live-wallpaper` command, `live-wallpaper install https://github.com/Gleasonmt/omarchy-topside-mountain-view-theme` does the second line for you.

Then pick **Topside Mountain View** in Omarchy menu → Style → Background. It's offered under every theme,
including ones you install later. Update with `live-wallpaper update`, or `git pull` in that folder.

Already have the plugin? Run `omarchy plugin update`: this scene needs its support for
picture layers and video.

## Details mode

In details mode the picture stays still and only small details move. It uses the least power,
which makes it good on battery or if you want a calmer desktop. Turn it on with `live-wallpaper mode details`
(`battery-mode details` for battery only) or in Omarchy menu → Style → Live Wallpaper:

![Topside Mountain View, details mode](media/topside-mountain-view-lite.webp)

## How it moves

The scene is four pictures stacked in depth (`live/*.png`: the sky, the far valley, the
cliff with the mast, the near ledge) and a GPU shader (`live/1-mountain-view.frag.qsb`,
source in `src/`) that drifts a camera slowly across them, so near things slide past far
ones, and draws the fog, clouds, lights, drone and vehicles. The scout is a looping
2-minute video (`live/scout.mp4`, colour and mask side by side) that the
[live-wallpaper plugin](https://github.com/Gleasonmt/omarchy-live-wallpaper) decodes on the GPU.
`backgrounds/1-mountain-view.png` is a frame of it, used by the lock screen and when the
plugin isn't installed. Everything pauses while a window covers the screen. See the plugin's
README for frame rates and battery settings.

The painting and the scout's movement were made with AI image and video models, then
cut into layers, cleaned up and animated by hand. The look is inspired by films and
games; no logos or characters are used.

## Performance

Measured on a Dell Latitude 9520 (Intel Iris Xe graphics, 8 threads) driving a 3440×1440
monitor, plugged in, with the plugin's default 30 fps. The wallpaper pauses whenever a window
covers the screen, so these are the costs while it's actually visible:

| Mode | GPU used | Wallpaper CPU | Compositor CPU |
|---|---|---|---|
| Still (plugin off) | ~0% | 2% | 4% |
| **Details mode** | ~2% | 10% | 8% |
| Topside Mountain View, half resolution | ~2% | 9% | 8% |
| Topside Mountain View, 15 fps | ~3.5% | 8% | 8% |
| **Topside Mountain View** | ~9% | 9% | 8% |

*GPU used* is how busy the GPU was, weighted by its clock speed against its maximum
(1350 MHz here). *CPU* is a share of one core. The scene only works out each effect
(fog, convoy, drone, mist) where it can be seen, and redraws once per frame, so even
full motion at full resolution is light; details mode and half resolution are lighter still.

On battery the plugin draws at half resolution and 10 fps by default (plugin 1.1.2 or
later: run `omarchy plugin update`). To use half resolution plugged in too, set
`"renderScale": 0.5` in `~/.config/omarchy/live-wallpaper.json` (see the
[plugin's README](https://github.com/Gleasonmt/omarchy-live-wallpaper)), or use details mode for the lightest moving version.

## Making changes

The scene is `src/1-mountain-view.frag` (with `src/common.glsl`); `src/scene.json` lists
the pictures and the video it uses. Edit it, then run

```bash
tools/build.sh           # rebuilds live/*.frag.qsb and live/*.json, the still in backgrounds/
tools/build.sh --media   # also the animated previews in media/ (a few minutes)
```

and commit the results. Needs `qt6-shadertools`, `qt6-declarative`, ImageMagick, jq and
(for `--media`) ffmpeg. To try a change on your own desktop, run
`live-wallpaper update` after pushing, or copy the built files into
`~/.config/omarchy/live-backgrounds/topside-mountain-view/`.

## License

[MIT](LICENSE)
