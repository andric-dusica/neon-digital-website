# Neon Digital - Astro

Marketing sajt za neondigital.rs - rebuild iz vanilla HTML/JS u Astro framework.

## Tech Stack

- **Astro 6** - meta-framework, server-side render statičkih stranica
- **Tailwind CSS 4** - utility-first CSS
- **Supabase** - dynamic content (galerija, logoi, slike)
- **Resend** - email service za kontakt formu
- **Swiper** - carousel na home stranici
- **Fancybox** - lightbox za galeriju
- **Vercel** - hosting i deployment

## Lokalno pokretanje

```bash
# Node 22+ je obavezan
nvm use 23.7.0

# Install dependencies
npm install --legacy-peer-deps

# Pokreni dev server
npm run dev
# -> http://localhost:4321/

# Build za produkciju
npm run build

# Preview build-a
npm run preview
```

## Environment varijable (.env)

```
SUPABASE_URL=https://gwodcdkzxwbepgxpxxvf.supabase.co
SUPABASE_ANON_KEY=...
RESEND_API_KEY=...
```

**Važno:** `.env` ne ide na GitHub. Na Vercel-u se postavlja u Project Settings → Environment Variables.

## Supabase dostupnost (Free plan)

Neon Digital koristi Supabase projekat `gwodcdkzxwbepgxpxxvf` za dinamički sadržaj sajta. Pošto je projekat na Free planu, Supabase ga može pauzirati kada tokom sedam dana ne vidi dovoljno database aktivnosti.

Od **16. avgusta 2026.** aktivan je dnevni, read-only health-check kroz GitHub Actions: [`.github/workflows/supabase-keepalive.yml`](.github/workflows/supabase-keepalive.yml). Svaki dan u **06:20 UTC** (08:20 po vremenu u Srbiji tokom letnjeg računanja vremena) workflow čita po jedan red iz postojećih tabela `our_work_images_videos`, `home_our_work_images_videos`, `client_logo` i `home_page`. Ne menja bazu, Storage, sadržaj sajta niti korisničke podatke.

Za workflow je u GitHub repository secrets potreban samo `SUPABASE_ANON_KEY`; vrednost se nikada ne upisuje u kod ili dokumentaciju. Run može ručno da se pokrene iz GitHub Actions stranice preko **Run workflow**. Ako run ne uspe, proveriti da li je secret i dalje postavljen i da li tabele/API dozvole postoje. Ako je Supabase projekat već pauziran, u Supabase Dashboardu treba kliknuti **Resume project**; workflow ga ne može sam reaktivirati.

## Struktura projekta

```
neon-digital-astro/
├── public/
│   ├── images/          # Statičke slike (logo, ikonice, hero)
│   ├── locales/         # Prevodi (en.json, sr.json)
│   └── vendor/fancybox/ # Fancybox CSS/JS
│
├── src/
│   ├── layouts/
│   │   └── BaseLayout.astro     # Head, body wrapper, nav, footer
│   ├── components/
│   │   ├── Nav.astro
│   │   ├── MobileMenu.astro
│   │   ├── Footer.astro
│   │   └── ContactCTA.astro
│   ├── lib/
│   │   └── supabase.ts          # Supabase klijent
│   ├── styles/
│   │   └── global.css           # Svi custom stilovi
│   └── pages/
│       ├── index.astro          # Home
│       ├── services.astro
│       ├── our-work.astro
│       ├── about-us.astro
│       ├── contact-us.astro
│       └── api/send-email.ts    # Email API ruta
│
├── astro.config.mjs
├── package.json
└── .env
```

## Gde se nalazi sadržaj

### Tekstovi (naslovi, opisi, dugmad)
- **`public/locales/en.json`** - engleski tekstovi
- **`public/locales/sr.json`** - srpski tekstovi

Tekstovi se vežu preko `data-i18n="kljuc.podkljuc"` atributa u HTML-u. Promeniš tekst u JSON fajlu, sajt se automatski ažurira.

### Slike

**Statičke slike (logo, ikonice, hero figure):**
- Smeštene u `public/images/`
- Reference u kodu: `/images/neon_logo.png`

**Dinamičke slike i video (galerija, logoi klijenata, team fotografije):**
- Smeštene u **Supabase Storage**
- URL-ovi su upisani u Supabase tabele:
  - `client_logo` - logoi klijenata na home strani
  - `home_page` - team fotografije (id 1, 2, 3)
  - `home_our_work_images_videos` - portfolio na home strani
  - `our_work_images_videos` - cela galerija na Our Work strani

**Bitno:** Promena slika u Supabase = treba **rebuild** (Vercel automatski rebuild-uje na svaki git push).

### Pricing i statički sadržaj
- Definisano u `.astro` fajlovima u `src/pages/`
- Npr. cene su u `services.astro`, kontakt info u `contact-us.astro`

## Česti zadaci

### Promeniti tekst na sajtu
1. Otvori `public/locales/en.json` i `public/locales/sr.json`
2. Pronađi ključ (npr. `hero.title`)
3. Promeni vrednost u oba fajla
4. Push na GitHub - Vercel automatski deploy

### Dodati novu sliku/video u galeriju
1. Upload sliku u Supabase Storage
2. Dodaj red u `our_work_images_videos` tabeli sa URL-om
3. Trigger Vercel rebuild (push bilo kakav commit ili klikni "Redeploy" u Vercel dashboard-u)

### Promeniti boje sajta
1. Otvori `src/styles/global.css`
2. Na vrhu fajla su CSS varijable (`--color-bg-body`, `--color-accent-pink`, itd.)
3. Promeni vrednosti

### Dodati novu stranicu
1. Kreiraj `src/pages/nova-stranica.astro`
2. Import `BaseLayout`
3. Push - automatski dostupna na `/nova-stranica`

### Dodati novi link u meni
1. Otvori `src/components/Nav.astro` i `src/components/Footer.astro`
2. Dodaj u `navLinks` array (otvori `MobileMenu.astro` takođe)
3. Dodaj prevod u `locales/en.json` i `locales/sr.json`

## Deployment

Vercel automatski deploy-uje na svaki push na `main` branch.

**URL-ovi:**
- Production: https://neondigital.rs (kad se prebaci domen)
- Preview: https://neon-digital-astro.vercel.app

## Stari sajt

Stari vanilla HTML sajt je u repo-u `andric-dusica/neon_digital`. Ne dirati - služi kao backup dok se ne potvrdi da novi radi savršeno.
