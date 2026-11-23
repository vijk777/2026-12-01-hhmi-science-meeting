# 2026-12-01-hhmi-science-meeting

Assets for the HHMI Science Meeting poster (2026-12-01), plus a small page for embedding them in an iPoster via iframe.

## Layout

- `assets/` - images, GIFs, and videos used on the poster.
- `embed.html` - chrome-less viewer for a single asset, meant to be loaded inside an iframe.

## Embedding in an iPoster

`embed.html` shows one file from `assets/`, scaled to fit the iframe:

```
embed.html?src=<file in assets/>[&bg=<css color>][&controls=1]
```

- Images and GIFs render as `<img>`.
- `.mp4` / `.webm` render as a muted, looping, autoplaying `<video>` (`controls=1` shows player controls).
- `bg` sets the background color (default transparent), e.g. `bg=black` or `bg=%23ffffff`.

### Frame-step mode

`embed.html?src=<video>&step=1&fps=<n>` starts paused with back-1 / fwd-1 buttons, play/pause, a scrub slider, and a frame counter. One click moves exactly one encoded frame, so the video must follow these rules:

- One encoded frame per distinct view. Do not repeat a view over several frames to hold it on screen; the hold time comes from the frame rate (e.g. 18 views at 1 fps).
- Constant frame rate, and `fps` in the URL must equal it.
- H.264, `yuv420p`, even width and height, no audio.
- Every frame a keyframe, no B-frames (`-g 1 -bf 0`), so stepping backwards is instant.
- `-movflags +faststart`.

```sh
ffmpeg -framerate 1 -i frames/%03d.png -c:v libx264 -crf 18 -g 1 -bf 0 \
       -pix_fmt yuv420p -vf "pad=ceil(iw/2)*2:ceil(ih/2)*2" -movflags +faststart -an assets/<name>.mp4
```

iPoster needs a public `https://` URL, so the repo has to be hosted first (e.g. GitHub Pages). Then:

```html
<iframe src="https://<host>/2026-12-01-hhmi-science-meeting/embed.html?src=flip_tour.mp4"
        width="640" height="360" frameborder="0" allowfullscreen></iframe>
```

## Local preview

```sh
python3 -m http.server 8000
# http://localhost:8000/embed.html?src=flip_tour.mp4
```
