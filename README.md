# LastLink: Meshtastic E-Ink node, open hardware, off-grid, verifiable

**Website:** https://peterwolfframm.github.io/meshtastic-esp32s3-eink/ · available in English, Czech and Polish

An open-hardware, solar-powered [Meshtastic](https://meshtastic.org) LoRa node for ESP32-S3 boards with E-Ink displays:

- **Delivered fully assembled** to every household: plug in the solar panel and mount it. Soldering is optional.
- **Blackout demo**: an interactive simulation of an alert failing over the cell network and reaching every node through the mesh (Cieszyn / Český Těšín).
- **Or build it at home** with a parts list (with shop links), an interactive pin diagram, a pin-by-pin wiring guide and a DIY firmware variant ([`hardware/`](hardware/), [`variants/diy_s3_eink/`](variants/diy_s3_eink/)).
- **Runs off-grid on solar**: a 2–6 W panel plus one 18650 cell.
- **Accessibility add-ons** for deaf, blind and deafblind people: a Braille keyboard and the state-supplied haptic band Pro (with sound recognition and an optional speech-to-text hub), both open hardware; deafblind people get an in-person handover ([`hardware/addons/`](hardware/addons/)). There is a registration form for supplied units and a self-build form for people who opt out of the assembled delivery.
- **Central-authority Q&A**: people ask only the central authority, never each other. Questions are auto-translated into EN / CS / PL and published with the official answer in a read-only channel.
- **Lab**: type a message to see its bytes, time on air, link budget and LoRa chirps for every preset; size a solar setup per month and simulate two weeks of weather.
- **Verifiable firmware**: SHA-256 manifests signed with ML-DSA-65 (FIPS 204) + Ed25519, verified entirely in the browser.

> Unofficial community project. Not affiliated with or endorsed by the Meshtastic project. LoRa use is regulated; check your local rules.

## Publisher key fingerprints

Compare these with the fingerprints shown on the Verify page:

```
ML-DSA-65  d1eb20c8d427e0bc3286cd9ae281f57d5b3e8a159ebf0a83a10f7fb960578610
Ed25519    0d8d88541b61d4e0d73e3e4e622e90f44f4c3267b48928b846296d24d40d03e6
```

## Repository layout

| Path | What |
|---|---|
| `site/` | Static website (no build step, deployed to GitHub Pages). Content in `site/content/{en,cs,pl}.js`, pin maps in `site/pins.js`, settings in `site/config.js` |
| `site/channel.json` | Read-only Q&A channel data (written by the workflow) |
| `site/releases.json`, `site/releases/` | Signed releases (currently one **demo** release of random bytes) |
| `site/demo.js` | Blackout simulation (cell network vs. Meshtastic managed flooding) |
| `site/lab.js` | Lab page: LoRa packet/airtime calculator, chirp visualiser, solar autonomy simulator |
| `site/extras.js` | Living E-Ink hero, ambient mesh canvas, ⌘K search palette, heading anchors, scroll reveal |
| `site/vendor/noble.js` | Bundled ML-DSA + Ed25519 verifier (`npm run vendor`) |
| `tools/sign.mjs` | Key generation and release signing |
| `tools/channel.mjs` | Translates questions and answers with Claude and updates the channel |
| `hardware/` | Open-hardware docs: parts list, wiring, accessibility add-ons |
| `variants/diy_s3_eink/` | Firmware variant for the DIY board (copy into `meshtastic/firmware/variants/`) |

## How the Q&A channel works

1. A visitor fills in the question form on the website. It opens a pre-filled GitHub issue (`.github/ISSUE_TEMPLATE/question.yml`).
2. `.github/workflows/question-channel.yml` **locks the issue**, so only maintainers can comment and users can't talk to each other. It then translates the question into EN / CS / PL and adds it to `site/channel.json`.
3. A maintainer (the central authority) comments on the issue. That comment is the official answer: it is translated, published to the channel, and the issue is closed.
4. The site redeploys automatically.

**Setup:** add the repository secret `ANTHROPIC_API_KEY` (Settings → Secrets → Actions). Without it, questions are still published, just untranslated.

## Registrations

Accessibility registrations and self-build / group-purchase sign-ups contain personal data, so they are **never** posted publicly. They are sent by e-mail to the address in `site/config.js` (`authorityEmail`). Until that is set, the forms show the filled-in data for the visitor to keep.

## Signing a release

```bash
npm install
npm run keygen                      # once: keys/private.json (gitignored, keep offline) + site/pubkey.json
node tools/sign.mjs sign path/to/firmware.bin site/releases/v1.0.0 \
  --version 1.0.0 --board heltec-wireless-paper --commit <upstream sha>
```

Then commit `site/releases/`, `site/releases.json` and (after keygen) `site/pubkey.json`, and update the fingerprints in this README.

## Run locally

```bash
npm run serve    # http://localhost:8080
```

## Licences

Firmware-related code and website: GPL-3.0 (`LICENSE`). Hardware designs: CERN-OHL-S-2.0 (`LICENSE-HARDWARE`).
