# Eazybe for Firefox — direct distribution

Hosts the Mozilla-signed Firefox build of the Eazybe add-on and the page people install it
from.

**Install page:** <https://eazybecode.github.io/eazybe-firefox-add-on-release/>

## Why this repo exists

The AMO listing for Eazybe is still in review. AMO's **unlisted** channel signs a build after
automated validation only — minutes rather than a queue — so this build was signed that way
and is served directly while the listing clears.

This is a parallel channel, not a replacement. When the AMO listing goes live it becomes the
real distribution and this repo is retired.

## Contents

| File | |
|---|---|
| `index.html` | Install page. Disables the button and explains why on non-Firefox browsers and on Firefox below 140 |
| `icon128.png` | Extension icon, from the extension repo's `public/` |
| `eazybe-4.0.28.xpi` | The signed build |

## Two things to know before sharing the link

**The add-on id is `eazybe-direct@eazybe.com`, not the production `eazybe@eazybe.com`.**
A separate id keeps this channel from disturbing the pending AMO submission. The cost: to
Firefox these are two different add-ons, so **anyone who installs from here must uninstall
and reinstall from AMO** once the listing is live. Say so up front.

**Firefox 140 or later.** The manifest declares `data_collection_permissions`, which is
unsupported below that. Older Firefox refuses the install with an unhelpful message.

## Publishing a new version

There is no `update_url` in this build, so Firefox will never update it on its own. Every
release is manual:

1. In the extension repo, bump `version` in `wxt.config.ts` — AMO rejects a version string it
   has already seen for this add-on id.
2. Rebuild and re-sign through the unlisted channel with
   `EAZYBE_FIREFOX_EXTENSION_ID=eazybe-direct@eazybe.com` set.
3. Add the new `.xpi` here **alongside** the old one. Don't overwrite — old builds are what
   you need when someone reports a bug on a version they installed last month.
4. Update the link, version and file size in `index.html`.
5. Tell existing users. Nothing else will.

Full procedure, including the failure modes: `docs/guides/firefox-direct-distribution.md` in
the extension repo.

## Serving requirement

GitHub Pages serves `.xpi` as `application/x-xpinstall` (its MIME map comes from `mime-db`),
which is what makes Firefox show an install prompt rather than downloading the file. GitHub
Pages supports no custom headers, so this only works here — release assets and
`raw.githubusercontent.com` both serve `application/octet-stream` and can only download.

After any change, confirm the header survived:

```bash
curl -sSI https://eazybecode.github.io/eazybe-firefox-add-on-release/eazybe-4.0.28.xpi | grep -i content-type
```

## Retiring

Once the AMO listing is live: archive or delete this repo, and tell anyone on the direct
build to reinstall from AMO.
