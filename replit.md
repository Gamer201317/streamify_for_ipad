# Streamify

Jouw persoonlijke streamingdienst voor iPad Mini 4 — films, series en collections afspelen vanuit je eigen bestanden, zonder server of abonnement.

## Run & Operate

- `pnpm --filter @workspace/streamify run dev` — start de Streamify PWA (port 22822)
- `pnpm run typecheck` — volledige typecheck over alle packages

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite, Tailwind CSS v4
- Routing: Wouter
- Storage: IndexedDB (voortgang, bibliotheek, favorieten)
- Media import: `<input webkitdirectory>` (werkt op iPadOS 15)
- PWA: manifest.json + service worker in /public

## Where things live

- `artifacts/streamify/` — de Streamify PWA
- `artifacts/streamify/public/STREAMIFY_BIBLIOTHEEK.txt` — mapstructuur gids voor de gebruiker
- `artifacts/streamify/src/lib/types.ts` — TypeScript types (Movie, Series, Collection, etc.)
- `artifacts/streamify/src/lib/db.ts` — IndexedDB wrapper (voortgang, favorieten, bibliotheek)
- `artifacts/streamify/src/lib/library.ts` — parser voor webkitdirectory input
- `artifacts/streamify/src/contexts/LibraryContext.tsx` — global state
- `artifacts/streamify/src/pages/` — Home, Player, SeriesDetail, CollectionDetail
- `artifacts/streamify/public/manifest.json` — PWA manifest
- `artifacts/streamify/public/sw.js` — service worker

## Architecture decisions

- **Geen backend** — volledig client-side; video's worden als blob-URL's afgespeeld direct vanuit de geselecteerde bestanden
- **webkitdirectory input** in plaats van File System Access API — iPadOS 15 ondersteunt `showDirectoryPicker()` niet; de `<input webkitdirectory>` werkt wél op Safari/iPadOS 15
- **IndexedDB** voor persistentie — bibliotheekstructuur (zonder File objects) + voortgang + favorieten worden opgeslagen; bij herstart laadt de UI meteen maar moeten bestanden opnieuw geselecteerd worden via de ↺ knop om video's af te spelen
- **Blob URL's** — voor elk videobestand wordt een tijdelijke blob-URL aangemaakt die automatisch wordt opgeruimd na gebruik
- **PWA manifest + service worker** — app kan worden toegevoegd aan het beginscherm; basic offline caching van de app shell

## Product

- Startscherm met groot ▶ Start knop (eerste keer) of bibliotheek (daarna)
- Tabs: Films | Series | Collections | Favorieten
- Verder kijken — strip bovenaan met recent gestopte content
- Videospeler met: PiP, scrubber, skip +10/-10s, "Intro overslaan" (30-90s), mute
- Smart Autoplay — 5-seconden countdown naar volgende aflevering
- Zoekfunctie — zoekt door alle tabs
- Favorieten — hartje op elke poster
- Voortgang automatisch opgeslagen (elke 5 seconden)
- Verborgen import — logo 3 seconden ingedrukt houden
- Oranje/zwart design, geoptimaliseerd voor iPad Mini 4

## User preferences

- iPad Mini 4 geoptimaliseerd (touch-friendly, grote tik-targets)
- Oranje (#FF6200) en zwart (#0D0D0D) als hoofdkleuren
- Alle tekst in het Nederlands
- Geen accounts, geen server, geen advertenties

## Gotchas

- File objects van `webkitdirectory` input worden NIET opgeslagen in IndexedDB — alleen metadata en covers (als data-URL). Bij herstart van de app moet de gebruiker de mediamap opnieuw selecteren via ↺ om video's te kunnen afspelen. De bibliotheekstructuur en covers zijn wel direct beschikbaar.
- Op iPadOS 15 ondersteunt Safari geen `showDirectoryPicker()` — gebruik altijd de `<input type="file" webkitdirectory>` aanpak
- Covers moeten `cover.jpg` heten voor series/collections mappen

## Mapstructuur voor gebruiker

```
Streamify/          ← selecteer deze hoofdmap
  Movies/
    Film.mp4
    film.jpg        ← optionele cover
  Series/
    Breaking Bad/
      cover.jpg     ← altijd "cover.jpg"
      S01E01.mp4
  Collections/
    Jurassic Park/
      cover.jpg
      Jurassic Park.mp4
```

## Pointers

- Zie `public/STREAMIFY_BIBLIOTHEEK.txt` voor volledige mapstructuur gids
- Zie de `pnpm-workspace` skill voor workspace structuur en TypeScript setup
