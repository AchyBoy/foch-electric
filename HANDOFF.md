# Foch An Electrical Co. website — handoff

**If you are an agent picking this up: keep this file current.** Update it in the same
commit as the work it describes, before you hand back. It is the only place the state of
this project is written down outside of Andrew's head.

Last updated: 2026-08-26.

---

## What this is

The real public website for **Foch An Electrical Co.**, which is Andrew's own company, not
a client's. ⚠️ That is the actual company name, "Foch Electric" is wrong and a whole build
once shipped under it.

- **Live at https://fochelc.com** over HTTPS. `www` 301s to the apex.
- Also reachable at https://achyboy.github.io/foch-electric/
- Repo `github.com/AchyBoy/foch-electric`, public, Pages serves `main` from the root.
- `~/Development/foch-site` **is** the repo. Edit `index.html` and push, that deploys it.
- One file, no build step, no dependencies. `music.m4a` is the only other asset besides `photos/`.
- Preview locally with the `foch-site` entry in JobHub's `.claude/launch.json` (port 8645),
  or just `python3 -m http.server` in this folder.

## State

Done:

- Content signed off by Andrew: phone (337) 277-9200, 17+ years, 400+ homes, 24h callback,
  insured, Lafayette LA and surrounding, licence 55468. Emergency yes, EV yes, generators
  plus maintenance plans yes. No street address, he says it is not relevant.
- `noindex` and the robots `Disallow` are **gone**. The site is public and indexable.
- Domain cut over from Google Sites to GitHub Pages on 2026-08-26, HTTPS enforced,
  Let's Encrypt cert covering apex and www.
- Two real photos are in, in the two slots that suit their shape.

Open:

1. **Two of the six photo slots are still gradient placeholders** (`ph-d` kitchen, `ph-f` wide
   handover), and two of the filled four are stand-ins Andrew wants to reshoot. See "Photos".
2. There is **no `og:image`**, so links shared in a text or on Facebook have no picture.

## Photos

Six slots in `index.html`. Find them with `grep -n 'class="ph ph-' index.html`.

| Class | Where | Crop | Status |
|---|---|---|---|
| `ph-a` | Work grid, lead card (`.card.tall`) | 4:5 | `photos/service-exterior.jpg` — stand-in, reshoot |
| `ph-b` | Builder-promises split section | 4:5 | `photos/panel-directory.jpg` — stand-in, reshoot |
| `ph-c` | Work card (`.card.tall`) | 4:5 | `photos/temp-pole.jpg` — good, keep |
| `ph-d` | Work card | 4:3 | placeholder gradient |
| `ph-e` | Work card (`.card.tall`) | 4:5 | `photos/ev-charger.jpg` — good, keep |
| `ph-f` | Work grid, wide card | 16:9 | placeholder gradient |

⚠️ **Every real photo so far has arrived portrait or square, so the cards that hold them were
turned portrait** with `.card.tall` (4:5). Only `ph-f` still needs a genuine landscape frame.
Check a photo's shape before deciding which slot it fills, rather than the other way round.

A photo is applied as a `background` on `.ph-X::before`, never as an `<img>`. That keeps the
hover zoom on `::before` and the dark gradient scrim on `::after`.

**The full shot list lives in two places and both must be kept in step:**

- a checklist note on the JobHub job **"Test Dummy" (`1778536433809`), phase `Extras `**
  (⚠️ that phase string has a trailing space), 15 items, which is what Andrew ticks in the field
- the artifact at https://claude.ai/code/artifact/af459252-ec8a-4f41-a9f4-608f2a3a2f72

⚠️ **The two panel shots stay on the list on purpose even though they are live.** `ph-a` wants
a landscape reshoot and `ph-b`'s source was shot with the phone rotated. Do not tick them off.
The EV charger and the temp pole are ticked.

⭐ **The best photo so far was not on the list at all.** The temp pole has **FOCH marked on the
lid in his own hand**, with the house going up behind it and his van in frame. Nothing on a
competitor's site has that. When he sends something off-list, judge the photo, not the list,
and add it to the list afterwards so the list matches reality.

### Getting photos out of JobHub

Andrew sends files by adding them to the media of the Test Dummy job. To retrieve:

```
psql $DATABASE_URL  -- from JobHub/jobhub-backend/.env
select file_name, storage_path, mime_type, size_bytes, upload_status, created_at
from job_media where job_id = '1778536433809' order by created_at desc limit 10;
```

then `GET ${SUPABASE_URL}/storage/v1/object/job-media/${storage_path}` with
`SUPABASE_SERVICE_ROLE_KEY` as a bearer token.

Traps, all hit on 2026-08-26:

- **iPhone media arrives as HEIC even though `mime_type` says `image/jpeg` and the name ends
  `.jpg`.** Pillow cannot open it at all. Convert first with `sips -s format jpeg`.
- `job_media` has **no `kind` column**.
- **Check the rotation against a known-good reference before you commit to it.** Andrew's
  exterior service shot needed a *clockwise* turn. Turning it the other way looked plausible
  at a glance and put the main breaker at the bottom with every label upside down, which
  reads as "mirrored" and sends you hunting for a flip that was never there. Sanity check
  against a panel you know is upright: main at top, directory card on the right.
- **Do not commit raw job photos to this repo, it is public.** Only the cropped,
  resized web versions in `photos/` belong here.

## The domain

`fochelc.com` is registered at **GoDaddy**, but **Andrew has no GoDaddy login and never did**.
The domain was bought inside the **Google Workspace signup in 2018** with GoDaddy as the
partner registrar, so Google generated the account.

Reach the DNS console this way:

1. admin.google.com → Account → Domains → Manage domains
2. **View Details** on fochelc.com → **Advanced DNS Settings**
3. That panel prints the GoDaddy sign-in name, password and support PIN
4. **SIGN IN TO DNS CONSOLE** single-signs-on into `dcc.secureserver.net`

⚠️ Those credentials are deliberately **not written down in this repo**, it is public.

⚠️ If the "retrieve username" email never arrives, that is expected and does **not** mean
there is no account. `whois fochelc.com` shows the renewals. **Never create a new GoDaddy
account.** GoDaddy's own auto-renew reads Off and must stay off, Google does the renewing.

**`.dns/before-2026-08-26.txt` is the full 32-record pre-cutover state.** Only five records
moved: four apex `A` records from Google Sites `216.239.*` to `185.199.108-111.153`, and the
`www` CNAME from `ghs.googlehosted.com` to `achyboy.github.io`.

⚠️ **Two mail systems live in that zone and neither may be touched**: Google Workspace on the
`@` MX records, and **Mailgun on `mail.fochelc.com`, which is JobHub's own inbound address
`office@mail.fochelc.com`**. Leave every MX, both SPF TXT records and the DKIM key alone.

## Traps in the page itself

Read these before touching the scroll-reveal or the audio.

1. **Reveal anything at or above the fold, not only what is in view.** A jump straight to a
   `#hash` fires one scroll event at the destination, and anything skipped stays at
   `opacity:0` for good.
2. **Nothing that keeps content visible may depend on the compositor.** The reveal backstop
   is a plain time throttle, not `requestAnimationFrame`, because rAF pauses whenever the
   view is not compositing and that left all 15 sections hidden.
3. `html{scroll-behavior:smooth}` also animates the browser's own load-time jump toward a
   position measured before layout settles. The initial landing is instant on purpose.
4. **Any pause, stop or teardown that hangs off a fade completing is broken on iOS.** iOS
   ignores `element.volume` outright, so a fade that waits for volume to reach zero never
   finishes. `fadeTo` has a hard deadline and `silence()` probes whether volume is honoured.
5. A `@media` block placed **above** the base rule it overrides silently loses. Check rule
   order before suspecting a stale cache.

⚠️ **The in-app browser pane cannot verify this page and will lie to you.** Its compositor
freezes after the first navigation, screenshots come back solid black while the DOM reports
correct state, and `innerHeight` reads 0x0 when the pane is hidden. **Drive Chrome over CDP**:
launch `--headless=new --remote-debugging-port=9333`, connect with
`require('<JobHub>/node_modules/ws')` (node 20 has no global WebSocket), then
`Emulation.setDeviceMetricsOverride` + `Page.captureScreenshot`.

## House style

- **Commas, not dashes.** Plain everyday words. This is how Andrew writes and how the site reads.
- **Say client, never homeowner.** Changed across the whole page on 2026-08-26 at his request, the
  same word the JobHub app settled on. ⚠️ Two lines were drawing a line between the trade side and
  the retail side ("for builders and homeowners"); a straight swap made them nonsense because a
  builder is a client too, so they read **"for builders and private clients"**.
- **Watch for phrasing that means cost but reads as scheduling.** "Put right on our time" was
  read as "whenever we get round to it", the exact opposite of the promise.
- **Put promise wording in front of him rather than shipping it.** He will not sign off on
  something he cannot keep on a bad week. Two builder promises were softened for this reason:
  rough-in photos are offered **on request**, and there is **no claim to pull the permit**.
- **Nothing on the page is invented.** Where a fact was missing it became a `data-todo`
  marker, never a plausible number. Keep it that way.
- **No stock or manufacturer photography.** Everything in Selected Work reads as work Foch An
  Electrical Co. did, under a page carrying licence 55468. An empty slot beats a borrowed photo.
- 🔴 **But do not read retouching as stock.** On 2026-08-26 the Tesla Wall Connector photo was
  called out as manufacturer product photography and refused, on three tells: a perfect square
  crop, **no camera EXIF**, and even studio-looking light. **All three were wrong.** It is
  Andrew's own photo of a Level 2 Wall Connector **he installed at his own house**, and he had
  run AI over it to take out personal items, which strips EXIF, re-crops and evens the light.
  **Ask whose photo it is before concluding anything from metadata.** The absence of EXIF is
  evidence of editing, not of origin. He is comfortable with AI cleanup of his own photos; the
  line he agreed to is that it must not invent work that was not done.
- `data-todo` attributes are inert markers of what is still open.
  `grep -o 'data-todo="[^"]*"' index.html` recovers the list.
