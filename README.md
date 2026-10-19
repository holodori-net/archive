# Holodori Archive

This repository contains public, account-independent event, Gacha, exchange, and notice data. The archive is rooted at [`index.json`](index.json) and uses schema version 2.

## Files

```text
index.json
events/<eventId>/metadata.json
events/<eventId>/<locale>.json
gachas/<gachaId>/metadata.json
gachas/<gachaId>/<locale>.json
exchanges/<exchangeId>/metadata.json
exchanges/<exchangeId>/<locale>.json
notices/<noticeId>/metadata.json
notices/<noticeId>/<locale>/index.html
notices/<noticeId>/<locale>/<sha256>.<ext>
```

The supported locales are `jpn`, `eng`, `ind`, `kor`, `cht`, and `chs`. `index.json` maps event, exchange, and notice IDs to the locales present in the archive. Gacha metadata and text are available in all six locales. `knownGachaIds` lists observed Gachas retained in the archive; `currentGachaIds` lists IDs visible in the latest collected API list. `currentEventIds` lists events visible in the latest collected API list; `events` includes all retained event IDs and their available locales. Discovery depends on the collecting account's access and does not enumerate every historical event or Gacha.

Event, Gacha, and exchange metadata contain source fields; localized files contain original localized API text. Timestamp fields are decimal epoch milliseconds represented as strings. Probability values use parts per ten million. `probabilityStatus` records availability; card-selection pools retain candidates and selection quantity with `selection-required` status and null public probability arrays. `masterVersion` is the master-data revision reported by the API; `databaseMasterVersion`, when present, is the separately sourced SQLite database version.

Event metadata contains schedules, assets, Marathon chapters, score and ranking rewards, and bonuses. Event locale files contain the event name and chapter score labels, joined to metadata by event and chapter IDs. `marathonInfo.exchangeBoothGroupId` links to an exchange in `index.exchanges` when available.

Notice metadata contains localized API fields, including `htmlPath` and image paths relative to `notices/<noticeId>/`. `htmlPath` is null when no detail page is supplied. Archived HTML is at `<locale>/index.html`; archived images are sibling files in that locale directory and are referenced by basename from the HTML. CSS, JavaScript, and video resources remain external. Event notices are archived with localized notice `type: 2`. Account-specific state is not included.

## Read a Gacha

This browser JavaScript example loads the index, then joins one Gacha's metadata and Traditional Chinese text by `gachaId`:

```js
const base = "https://raw.githubusercontent.com/holodori-net/archive/main/";
const LOCALE = "cht";

const index = await fetch(`${base}index.json`).then((response) =>
  response.json(),
);
const gachaId = index.currentGachaIds[0];
const [metadata, text] = await Promise.all([
  fetch(`${base}gachas/${gachaId}/metadata.json`).then((response) =>
    response.json(),
  ),
  fetch(`${base}gachas/${gachaId}/${LOCALE}.json`).then((response) =>
    response.json(),
  ),
]);

console.log({ metadata, text });
```
