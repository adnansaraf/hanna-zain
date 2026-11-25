# Zain Weds Hanna — Wedding Invitation Website

A production-ready, ultra-fast wedding invitation website (Groom's side) crafted with clean HTML5, vanilla CSS, and vanilla JavaScript. Designed for zero-dependency deployment on Netlify with an optional Supabase guestbook.

---

## 🌟 Highlights & Features

- **Double-Door 3D Gate Cover**: Light warm champagne-pearl leaves (`#EFE5D0` to `#F6F2EA`) with vertical fluting, `#C9AE7C` hairlines, brass handles, and a center obsidian glass card (`rgba(20, 17, 15, 0.88)`). Scroll is locked until "OPEN INVITATION" is clicked, then opens with a smooth GPU-accelerated 3D door swing.
- **Fully Responsive**: Crafted mobile-first (360px–430px), tablet (768px), and desktop (1280px–1920px) with centered editorial styling.
- **Floating Wish Ticker**: Shows one wish at a time with a smooth floating-up animation, adaptive reading hold duration (2s base + 40ms/word beyond 15 words, capped at 6s), visible controls (pause/play, prev/next, counter), and a "View all wishes" modal with focus trap and pagination.
- **Anti-Spam & Secure Guestbook**: Honeypot protection, 60s cooldown in `localStorage`, strict character limits, and 100% `textContent`-only rendering for XSS immunity.
- **Live Supabase REST Guestbook**: Uses anonymous publishable keys only. Gracefully falls back to preview sample wishes when unconfigured.
- **Self-Hosted Assets**: Variable woff2 fonts (`Bodoni Moda`, `Jost`, `Aref Ruqaa`), critical fonts preloaded, custom SVG favicon, and a 1200x630 share image.
- **Zero Build Step**: Pure HTML/CSS/JS ready to drag onto Netlify Drop.

---

## 📁 Project Structure

```text
wedding-zain-hanna/
├── index.html                           # Accessible semantic HTML & noscript fallback
├── css/
│   └── style.css                        # Pure CSS (custom properties, 3D doors, animations)
├── js/
│   └── main.js                          # Hydration, cover gate, countdown, wish ticker, Supabase
├── config/
│   └── site.config.js                   # ALL content, links, dates, tokens, and credentials
├── assets/
│   ├── fonts/                           # Self-hosted woff2 fonts
│   │   ├── bodoni-moda-normal.woff2
│   │   ├── bodoni-moda-italic.woff2
│   │   ├── jost-latin.woff2
│   │   ├── aref-ruqaa-arabic.woff2
│   │   └── aref-ruqaa-arabic-700.woff2
│   └── images/
│       ├── share.jpg                    # 1200x630 Open Graph / Twitter share preview
│       ├── favicon.svg                  # SVG vector icon
│       └── favicon.png                  # 64x64 raster icon
├── supabase/
│   └── schema.sql                       # Database schema, indexes & RLS policies
├── netlify.toml                         # Security headers & cache headers
└── README.md                            # Setup guide & pre-flight checklist
```

---

## 🚀 Setup in 5 Steps

### Step 1: Extract or Open the Project
Unzip `wedding-zain-hanna.zip` into a local directory or inspect the folder.

### Step 2: Set up Supabase (Guestbook)
1. Go to [supabase.com](https://supabase.com/) and create a **new project** for this wedding invitation.
2. In your Supabase Dashboard, open the **SQL Editor** from the left navigation.
3. Paste and run the entire SQL script from `supabase/schema.sql`:
   ```sql
   create table if not exists public.wishes (
     id bigint generated always as identity primary key,
     name text not null check (char_length(btrim(name)) between 1 and 60),
     message text not null check (char_length(btrim(message)) between 1 and 300),
     visible boolean not null default true,
     created_at timestamptz not null default now()
   );
   create index if not exists wishes_created_idx on public.wishes (created_at desc);
   alter table public.wishes enable row level security;
   create policy "anyone can read visible wishes" on public.wishes for select to anon using (visible = true);
   create policy "anyone can send a wish" on public.wishes for insert to anon
     with check (visible = true and char_length(btrim(name)) between 1 and 60 and char_length(btrim(message)) between 1 and 300);
   revoke all on public.wishes from anon;
   grant select, insert on public.wishes to anon;
   ```
4. Navigate to **Project Settings > API**:
   - Copy **Project URL** (e.g., `https://xyzproject.supabase.co`)
   - Copy the **anon / public key** (Publishable Key). *Never use the `service_role` key!*

### Step 3: Configure `config/site.config.js`
Open `config/site.config.js` in any text editor and update the `guestbook` block:
```javascript
guestbook: {
  supabaseUrl: "https://your-project.supabase.co",
  publishableKey: "your-anon-publishable-key-here",
  tableName: "wishes",
  isSample: false // Set to false so guests only see real wishes
}
```

You can also customize:
- `cover.doorStyle`: `"light"` (champagne-pearl) or `"dark"` (warm obsidian).
- All wedding dates, couple names, venue location, Maps links, and Google Calendar links.

### Step 4: Test Locally
You can test the invitation in any web browser by serving the folder locally:
```bash
# Using Node.js:
npx serve .

# Or using Python:
python -m http.server 8080

# Or with VS Code Live Server extension
```
Open `http://localhost:8080` (or the port shown), click "OPEN INVITATION", test submitting a wish, verify the countdown, and check responsiveness.

### Step 5: Deploy to Netlify
1. Log into your [Netlify](https://app.netlify.com/) account.
2. Go to **Sites** and drag either the `wedding-zain-hanna` folder or `wedding-zain-hanna.zip` directly onto **Netlify Drop**.
3. In seconds, your invitation is deployed live with HTTPS, custom headers, and global CDN caching.

---

## 🛡️ Moderation Guide

To moderate or hide any guest wish:
1. Go to your Supabase Dashboard > **Table Editor**.
2. Select the `wishes` table.
3. Locate the wish row you want to hide and change `visible` from `true` to `false`.
4. Due to Row Level Security (RLS), hidden wishes become completely invisible to all website visitors.

---

## ✅ Pre-Delivery Checklist

Before sharing the link with guests:
- [ ] **Couple & Family Names**: "Dr. Sayed Zain Mashhoor", S/o Sayed Ibrahim Mashhoor & Thahira Jiffri; "Adv. Hanna Musthafa", D/o Mohamed Musthafa & Zuhra Musthafa.
- [ ] **Ceremony Details**: 26 December 2026, Saturday, 11:00 AM IST at Presidency Convention Center, Poopalam.
- [ ] **Google Maps Link**: Tapping "Open location in Maps" opens the venue correctly.
- [ ] **Google Calendar Link**: Tapping "Add to Calendar" opens Google Calendar with the exact UTC times (`20261226T053000Z` to `20261226T063000Z`).
- [ ] **Cover Gate**: Doors open with the warm champagne-pearl look on mobile & desktop, and scroll locks until opened.
- [ ] **Countdown**: Correctly counts down days, hours, minutes, and seconds to 26 December 2026, 11:00 AM IST.
- [ ] **Wish Ticker**: Loops smoothly, pauses on hover/touch, and "View all wishes" opens the full modal.
- [ ] **Supabase Connected**: Real keys entered in `site.config.js`, test wish sent and visible, `isSample: false` set.
- [ ] **Share Preview**: Open Graph image (`assets/images/share.jpg`) displays correctly in WhatsApp/iMessage/Telegram previews.
- [ ] **SEO Privacy**: Verified `noindex, nofollow` is in place for private family invitation.
