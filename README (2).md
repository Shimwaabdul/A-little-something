# A Little Something

A single-page birthday greeting. Plain HTML, CSS and JavaScript. No frameworks, no build step, no backend.

## Project structure

```
a-little-something/
├── index.html   ← the whole site: styles, markup, your text and the script
└── README.md    ← this file
```

Inside `index.html`, in order:

1. **`<style>`**: palette, typography, envelope, stickers and animations (all in the `<head>`)
2. **Sticker drawings**: a hidden block of inline SVG at the top of `<body>` (you never need to touch it)
3. **`STICKERS` block**: one line per sticker in the background (search for `STICKERS`)
4. **Screens**: the intro with the envelope, and the message card
5. **`CONFIG` script** (search for `EDIT HERE`): your names, the date and the message
6. **Behaviour script**: fills the page from `CONFIG` and runs the opening sequence

Fonts (Playfair Display and DM Sans) load from Google Fonts. If they can't load, the page falls back to Georgia and the system sans-serif and still looks fine.

## 1. Customize it

Open `index.html` in any text editor and search for **`EDIT HERE`**. You'll find this block:

```js
const CONFIG = {
  recipientName: "Her First Name",
  senderName:    "Your Name",
  birthdayDate:  "November 25, 2026",

  sound: true,

  music: "",
  musicLoop: false,

  greeting: "Happy Birthday!",

  message: [
    "I hope this new chapter brings you good people, ...",
    "Wishing you nothing but good things in the year ahead."
  ],

  closing: "Here’s to a good year ahead."
};
```

- Replace `Her First Name` and `Your Name` (keep the quotation marks).
- Each line in `message` is one paragraph. Add or remove lines freely; separate them with commas.
- If your text contains a double quote, write it as `\"`.
- The date is shown in capitals automatically.

**Colors:** at the top of `index.html`, the `:root { ... }` block holds the palette. The page background is deep chocolate (`--bg`, with a soft candlelit glow from `--bg-glow`), the card and letter are `--paper`, and the accents are champagne `--gold` and chocolate `--choc`. Change those values to retune the whole page.

## Stickers

The little illustrations around the page (satin bows, pearls, champagne flutes, candles, balloons, roses, a bouquet, a gift, a cake, ribbon curls and sparkles) are drawn in code, so there are no image files to host. Find the `STICKERS` block in `index.html`. Each sticker is one line:

```html
<i class="st" style="--x:13%;--y:8%;--s:60;--xl:19%;--yl:27%;--sl:95;--ar:1.000;--r:-14;--i:0">
  <svg viewBox="0 0 100 100"><use href="#k-bow-choc"/></svg>
</i>
```

- **Remove** a sticker: delete its line.
- **Move** it: change `--x` / `--y` (phones and tablets) and `--xl` / `--yl` (wide screens, 1100px and up). Values are a percent of the window.
- **Resize** it: `--s` (phones and tablets) and `--sl` (wide screens) are the height in pixels.
- **Tilt** it: `--r` is the angle in degrees.
- **Swap the picture**: change the name after `#k-`. Available: `bow`, `bow-choc`, `pearls`, `cheers`, `candle`, `balloon`, `balloon-black`, `rose`, `rose-deep`, `bouquet`, `gift`, `cake`, `streamer`, `streamer-gold`, `spark`.
- **Add** one: copy any line and change its values. If you change the picture, copy the `--ar` value from another line that uses it (it is the picture's width divided by its height). Lines with `class="st st--wide"` only show on wide screens.

On phones and tablets the stickers sit above and below the content and peek in from the sides, so they never cover the text. On very short screens (for example a phone held sideways) they are hidden.

## Sound

Three soft sounds play when she opens the letter, timed to what she sees: a paper rustle as the flap lifts, a quiet paper slide as the letter comes out, and a two-note glass chime as the card appears. The whole thing lasts about four seconds.

- **No audio files:** the sounds are generated in the browser, so there is nothing to download, license or host.
- **Only after a tap:** nothing plays when the page loads. Browsers require a tap before they allow sound anyway.
- **Mute button:** a small speaker icon in the top-right corner. Her choice is remembered on that device.
- **Reduced motion:** if her device asks for less motion, the paper sounds are skipped and only the chime plays.
- **Turn it off completely:** set `sound: false` in the `CONFIG` block. The button disappears too.
- **Quieter or louder:** in the script near the bottom, change `const VOLUME = 0.8;` (0 is silent, 1 is full).
- **iPhone note:** an iPhone with the silent switch on may mute web sound. The page works the same without it.

## Music (optional)

The page can play a background track. It tries to start the moment the link is opened and fades in over about three seconds. Browsers (Chrome, Safari, Firefox) usually block sound on a freshly opened page, so in most cases it starts on her very first tap or key press anywhere on the page, even before she opens the envelope. No code can get around that browser rule. The speaker button turns the music off and on. Nothing plays until you add a file and set `music` in the `CONFIG` block.

**1. Shrink your audio file.** A 123 MB file is too big: GitHub rejects anything over 100 MB, and a phone should not download that much. Aim for 2 to 5 MB. An MP3 at 128 kbps is about 1 MB per minute.

- **Audacity** (free, no commands): open your file, select the part you want, then Edit → Remove Special → Trim Audio. Add Effect → Fading → Fade Out on the last few seconds, and lower the volume with the Amplify effect (try -6 dB). Then File → Export → Export as MP3 at 128 kbps and name it `music.mp3`.
- **ffmpeg** (free; on Windows, `winget install ffmpeg`), simple version:

  ```
  ffmpeg -i "your-file.wav" -vn -ac 2 -ar 44100 -b:a 128k -af "volume=-6dB" music.mp3
  ```

  To keep only part of it and add a fade-in and fade-out (this keeps 3 minutes starting at 10 seconds, and fades out over the last 8 seconds):

  ```
  ffmpeg -i "your-file.wav" -ss 00:00:10 -t 180 -vn -ac 2 -ar 44100 -b:a 128k -af "volume=-6dB,afade=t=in:d=2,afade=t=out:st=172:d=8" music.mp3
  ```

  If your clip is not 180 seconds long, set `-t` to its length and set `st` to its length minus 8.

**2. Put `music.mp3` in the same folder as `index.html`** (next to it, not in a sub-folder).

**3. Turn it on.** In the `CONFIG` block, change `music: "",` to `music: "music.mp3",`. If the track is short and you want it to repeat, set `musicLoop: true`.

**4. Test it.** Double-click `index.html` and tap anywhere on the page, and the music should start softly. If you hear nothing, check that the file name matches exactly (online, capital letters matter) and look at the browser console (F12), which names the file it could not load.

**5. Upload both files.** For GitHub Pages, add `music.mp3` to the repository next to `index.html`, then commit and push. Free GitHub Pages needs a public repository, so the audio file becomes publicly downloadable. If that worries you, Netlify Drop (drag the whole folder onto <https://app.netlify.com/drop>) gives you a link that is not listed anywhere.

Settings in the script near the bottom of `index.html`: `MUSIC_VOLUME` (0 to 1) and `MUSIC_FADE_MS` (fade-in length). iPhones ignore volume changes from web pages, so on an iPhone the track plays at the file's own level with no fade. That is why the steps above lower the file by 6 dB.

The page's browser-tab title updates from the name too. Until you replace the placeholder names, the browser console shows a reminder.

## 2. Run it locally

Easiest: double-click `index.html`. It opens in your browser and works as is.

Or with a local server (closer to how it behaves online):

```bash
cd a-little-something
python -m http.server 8000
```

Then open <http://localhost:8000>.

To test it the way she'll see it, open your browser's device toolbar (F12, then the phone icon) and try a small screen such as 360 × 640.

## 3. Deploy with GitHub Pages

1. Create a new repository on GitHub. Consider making it **public**; free Pages needs that.
2. Upload `index.html` (and optionally `README.md`) to the repository root.
3. Go to **Settings → Pages**.
4. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose `main` and `/ (root)`, then **Save**.
5. After a minute or two your site is live at `https://YOUR-USERNAME.github.io/REPOSITORY-NAME/`.

Tips:

- The repository name appears in the URL. A neutral name keeps the link low-key.
- The page includes `noindex`, so search engines are asked not to list it. Anyone with the link can still open it.
- Open the live link on your phone before sending it.
- Netlify also works: drag the folder onto <https://app.netlify.com/drop>.
