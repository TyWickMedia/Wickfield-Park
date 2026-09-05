# Wickfield RV Park — Website

Marketing site for **Wickfield RV Park**, at 1919 N Jardot Rd, Stillwater, OK.

The design mirrors the park's entry sign: dark pine field, bone and rust palette, heavy woodtype
slab headings, condensed gothic labels, solid pictograms and a drawn prairie horizon.

11 oversized pull-through sites · full hookups (water, sewer, 50 amp) · free WiFi · dog park · chipping green ·
$60 a night · $250 a week · $500 a month · reservations through Firefly.

## Stack

One static page — `index.html` with inline CSS and a few lines of vanilla JS. No build step, no
dependencies, no third-party requests except the Google Maps embed. Preview it by opening the file,
or:

```
python3 -m http.server 8000    # then visit http://localhost:8000
```

## Files

```
index.html                    the main site
before-you-book.html          the acknowledgement gate every booking click passes through
assets/logo.svg               WICKFIELD / RV PARK lockup, dark — for light backgrounds
assets/logo-reversed.svg      same lockup in bone — for dark backgrounds
assets/logo-mark.svg          the field mark alone — favicon and profile pictures
assets/og-card.png            1200×630 share card, built as the entry sign
assets/fonts/                 Alfa Slab One + Oswald + Inter, self-hosted (~89 KB)
robots.txt / sitemap.xml      basic SEO files
```

The header and footer logos are inlined directly in the HTML so the wordmark renders in Alfa Slab
One. The standalone `.svg` files fall back to Georgia on machines without that font — fine for
reference, but have the wordmark converted to outlines before sending it to a printer.

### Design tokens

Set once at the top of the stylesheet in `:root`, all sampled from the sign:

| token | value | used for |
|---|---|---|
| `--pine` / `--pine-dk` | `#2F3C2C` / `#202A1F` | dark bands, hero, footer |
| `--rust` / `--rust-lt` / `--brick` | `#B8571F` / `#C86A2A` | buttons, labels, accents |
| `--bone` / `--paper` / `--tan` | `#E7DFC6` / `#F2EAD6` / `#E4D9BC` | text on dark, page, alternate bands |
| `--sage` / `--olive` | `#5C7048` / `#8A8B4E` | the drawn landscape |

Type: **Alfa Slab One** for headlines and the wordmark, **Oswald** for labels, buttons and nav,
**Inter** for body copy. A faint paper-grain overlay sits on `body::after`.

## The day you open: flip one switch

The site currently runs in pre-opening mode. One flag at the bottom of `index.html` controls it:

```js
const FIREFLY = {
  bookingUrl: "https://app.fireflyreservations.com/reserve/property/WickfieldPark",
  bookingOpen: false,   // ← flip to true the day reservations open
  embed: false
};
```

**`bookingOpen: false`** (now) — the top bar and hero say "Opening soon", every call-to-action reads
"Ask About Dates" and scrolls to the contact form, and the reservations section invites people to
send their dates.

**`bookingOpen: true`** — the same page says "Now taking reservations", every call-to-action changes
its wording ("Check Availability", "Book Your Site") and points at the booking path, and the
reservations section switches to the reserve card. Nothing else needs editing.

The wording for both states lives in the HTML: `data-open-text` on the two "opening soon" phrases,
and `data-open-label` / `data-open-href` on each button. To reword a button for the open state, edit
its `data-open-label`.

### The acknowledgement gate

`requireAcknowledgement: true` (the default) means no one reaches Firefly without passing through
`before-you-book.html` first. That page explains the aerobic septic rule, states check-in and
check-out, and will not let the "Continue to Booking" button work until the guest ticks a box
confirming they have read it. The checkbox is `required` and the form posts to the Firefly URL, so
the gate holds even with JavaScript disabled — the script only greys the button out and keeps the
URL clean.

Set it to `false` to send guests straight to Firefly with no gate.

The septic rule appears in three places on purpose: the "Arriving at the park" notice on the home
page, the FAQ (which also carries it in structured data, so Google can surface it), and the gate.
People skim.

`embed` only matters once `bookingOpen` is true *and* `requireAcknowledgement` is false — an
in-page calendar and a gate are mutually exclusive. `false` opens Firefly in a new tab (the safe
default). `true` loads their calendar in an iframe — check it on the live site first, because many
booking engines send `X-Frame-Options: DENY` and would leave a blank box. If Firefly gives you a
`<script>` widget snippet instead of a URL, paste it inside `#firefly-frame`.

## Still to do before launch

Search the file for `TODO`.

- [ ] **Test the contact form** — it posts to Formspree form `xppzrglw`. Formspree needs you to
      confirm the first submission from a new form, so send yourself a test message once the site is
      on a real domain. With the park pre-opening, every call-to-action leads here.
- [ ] **Photos** — the site is deliberately photo-free (drawn prairie scenes and solid pictograms
      instead). When photos exist, the natural slots are behind the hero, the illustrated panel in
      "Built for big rigs", and a new gallery section.
- [ ] **Cabins** — the park plans to add cabins. Nothing on the site mentions them yet. When
      they're close, the cleanest move is probably a second page rather than diluting this one,
      since "Wickfield RV Park" is specifically the RV side of the business.
- [ ] **Site specs** — exact length and width, and the surface (gravel or concrete). The page
      states full hookups at every site but no dimensions.
- [ ] **Opening date** — the page says "Opening soon" with no date. Add one when you have it, and
      flip `bookingOpen` when reservations open.

## Hosting

Any static host. For GitHub Pages: Settings → Pages → deploy from `main`, root folder. For the
wickfieldpark.com domain, add a `CNAME` file containing `wickfieldpark.com` and point DNS at GitHub.
