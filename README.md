
#  ⚠️ created entirely by AI  ⚠️

# JAI Bio Studio

Editor for JanitorAI character bios with a live preview of what the server sanitizer **actually keeps**.

JanitorAI strips `<style>`, `<script>`, `<link>` and every `position` declaration when a bio is saved, and `z-index` is removed with a naive text replace (it eats the word even inside body copy). The client renderer, meanwhile, sanitizes nothing — whatever ends up in the database is rendered verbatim. So the trick is to write the bio through the API instead of the built-in editor.

This tool makes that loop fast: edit on the left, see the post-sanitizer result on the right, get a ready-to-paste script.

## Features

- **Sanitizer preview** — a local reimplementation of the server pass: cuts `<style>`, `<script>`, `<link>`, strips `position`, removes `z-index`, drops `style` attributes that became empty. Side-by-side with the raw source.
- **Linter** — line-by-line warnings for anything that will not survive: `position`, `z-index`, `animation`, `100vw`, `javascript:`, relative `<img src>`, custom fonts outside the 14 JAI ones, unbalanced tags.
- **Token helper** — builds a console snippet that reads the Supabase session cookie on janitorai.com, decodes chunked / URL-encoded / base64 cookies, checks expiry and copies the token. The main save script can also read the cookie itself, so a token never has to be pasted at all.
- **Profile gallery** — multiple bios in `localStorage`, save / duplicate / delete, JSON import and export for sharing.
- **Image insert** — paste a URL from the janitorai gallery (the one that shows up after moderation) and get either an inline `<img>` with `max-width` or a `background-image` block.
- **RU / EN interface**.
- **Syntax highlighting**, `Ctrl+Enter` to render, `Ctrl+S` to save the profile.

## Usage

Open `index.html` in a browser. No build step, no dependencies, no network calls except the Google Fonts stylesheet used by the preview.

The app ships with a built-in guide (the **Guide** tab, or press `F1`) covering the whole flow for first-timers, plus a five-step welcome dialog on first launch.

1. Paste or write your HTML.
2. Fill in the bot UUID (from `janitorai.com/characters/{uuid}`). The token field can stay empty — the generated script reads the cookie itself.
3. Export tab → **Copy script**.
4. On janitorai.com open the bot page → `F12` → Console → paste → `Enter`.
5. A Cloudflare Turnstile overlay appears: **close DevTools**, solve the captcha, come back to the console.
6. `200 OK` → press `F5`.
7. Optional: **Verify** gives you a snippet that fetches the stored bio back, so you can diff it against what you wrote.

### Gotchas

- **Scripts and lorebooks** save through a different endpoint (`PUT /hampter/script/{uuid}/content`, twice: `draft` and `published`) and need no Turnstile. Always run the update from the view page `/scripts/{uuid}` and reload with `F5`: navigating to your own script by clicking inside the site triggers a background auto-save that silently rewrites your HTML with a stripped-down version.
- **No overlapping elements.** `position` is gone. Fake layering with `clip-path`, negative margins, or SVG inside `background-image`.
- **No custom fonts.** Only the 14 loaded by JAI: `Inter`, `JetBrains Mono`, `Jura`, `Poppins`, `Fraunces`, `Crimson Text`, `Lexend`, `Berkshire Swash`, `Dancing Script`, `Monoton`, `Press Start 2P`, `Rock Salt`, `VT323`, `Noto Sans JP`.
- **No keyframes.** `animation` can only reference JAI's own hashed names, which change on every redeploy.
- **Watch `100vw` and unconstrained heights** — they overflow the container for every visitor.
- The preview is approximate: JAI's own container variables, paddings and `max-width` differ. The linter catches mistakes; it does not reproduce their layout.

## Security

The access token is a full session credential for your account and lives about an hour. Never paste it into someone else's script, a pastebin, or a "help me fix my bio" thread. Profiles are stored unencrypted in `localStorage`, and exported JSON files contain your bio text — scrub anything private before sharing.

This talks to JanitorAI's own API with your own credentials for your own bots. It does not bypass authorization; it works around an editor that mangles valid HTML.

## License

MIT — see `LICENSE`.
