# SCOPE: hosting guide

Static site (no build step). Backend: Supabase project `gzokrgqfhizpsaczmmfa` (Mumbai).

## Contact details
Personal contact details have been omitted from this public repository. Add a dedicated project contact address before opening the platform to external users. Please have a lawyer review the Privacy Policy and Terms before you take real users or real money. They match how the app works today but are not legal advice.

## Files
- `index.html` the whole app
- `_headers` security headers for **Netlify** and **Cloudflare Pages**
- `vercel.json` the same headers for **Vercel**
- `students/`, `startups/`, `micro-internships/`, `how-it-works/`, `faq/` content pages
- `learn/` hub with six guides (first internship, portfolio projects, hiring interns, skill-based hiring, pricing a task, resume)
- `about/`, `privacy/`, `terms/` trust and legal pages (linked from the signup screen)
- `sitemap.xml`, `robots.txt`, `llms.txt`, `og-image.png`, icons, `manifest.webmanifest`, `404.html`

GitHub Pages cannot send headers; the CSP in `index.html`'s `<meta>` still applies there.

## Deploy (pick one, about 2 minutes)
- **Netlify:** drag this folder onto app.netlify.com/drop
- **Cloudflare Pages:** Create project, Direct upload, choose this folder
- **Vercel:** `npx vercel --prod` inside this folder

## Layouts
- **Phone / tablet (under 1000px):** single column with a bottom tab bar.
- **Laptop / desktop (1000px and up):** side navigation rail, wider content, two-column task grid, centred swipe deck, centred pop-ups.
- **Keyboard:** on the Discover screen, `←` skips and `→` applies (ignored while typing).
- **Lite mode:** on slow machines (measured frame rate under ~42 fps), Data Saver, or 2 GB devices, the animated background switches off automatically. Nothing else changes.

## SEO and AI-search (GEO) setup
**What is included:** keyword-focused title and description, canonical links, social share image, structured data (Organization, WebSite, WebApplication, FAQPage, Breadcrumbs), `sitemap.xml`, `robots.txt` (search and AI crawlers allowed), `llms.txt`, and real content pages (students, startups, a micro-internship guide, how it works, FAQ), a Learn section with six guides, plus About, Privacy and Terms.

**After you deploy this folder, do these once:**
1. **Google Search Console** (search.google.com/search-console): *Add property* > *URL prefix* > `https://scope-prove-your-skills.netlify.app/`. Verify with the HTML tag method or via DNS, then *Sitemaps* > submit `sitemap.xml`. Then use *URL inspection* > *Request indexing* on the home page and the four pages.
2. **Bing Webmaster Tools** (bing.com/webmasters): import from Google Search Console. Bing also feeds ChatGPT search and other AI tools.
3. Test the share card: paste the URL into WhatsApp or LinkedIn and check the image appears.

**If you add a custom domain later (strongly recommended):** search-and-replace `https://scope-prove-your-skills.netlify.app` with the new domain in every file here (`index.html`, every page folder, `sitemap.xml`, `robots.txt`, `llms.txt`), redeploy, then update Supabase *Site URL* and *Redirect URLs*, and add the new domain to Search Console. A `*.netlify.app` address ranks much worse than your own domain.

**What moves rankings from here (cannot be done in code):** a custom domain, other sites linking to you (college groups, startup communities, LinkedIn, GitHub, Product Hunt), new helpful pages targeting searches such as "micro internships for students India", and a few real student and startup stories added to the site once you have them.

## Do these in the Supabase dashboard BEFORE launch (cannot be done from code)
1. **Authentication > URL Configuration:** set *Site URL* to your live domain and add it under *Redirect URLs* (password-reset links use this).
2. **Authentication > SMTP:** add a custom SMTP provider (Resend, Brevo, SendGrid). The built-in mailer is capped at a few emails per hour.
3. **Authentication > Providers > Email:** keep "Confirm email" ON; set minimum password length to 8.
4. **Authentication > Attack protection:** turn on CAPTCHA (Cloudflare Turnstile or hCaptcha) and *Leaked password protection* (Pro plan).
5. **Billing:** move to Pro before real users: free projects pause after a week of inactivity and have no daily backups.

## Pilot switches (SQL editor)
```sql
-- Startups must be approved by you before they can post:
update public.app_config set value='false' where key='auto_verify_startups';
update public.startups set verified=true where id = <startup id>;   -- approve one

-- Turn off simulated wallet top-ups / withdrawals (do this when no gateway is wired up):
update public.app_config set value='false' where key='demo_payments';

-- Make yourself an admin (never possible from the app):
update public.profiles set is_admin=true where id='<your auth user id>';
```

## Before taking real money
Payments are **simulated**. Wire a gateway (e.g. Razorpay) through a Supabase Edge Function that verifies webhooks and writes to `public.ledger`, then switch `demo_payments` off. Marketplace escrow in India has regulatory rules: get legal advice first.

## Optional hardening
Pin the Supabase script with Subresource Integrity:
`curl -s https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2.45.4/dist/umd/supabase.min.js | openssl dgst -sha384 -binary | openssl base64 -A`
then add `integrity="sha384-<hash>" crossorigin="anonymous"` to the `<script src>` tag.

## Known limits (by design, for the pilot)
- The leaderboard shows other students' display names and XP.
- Quiz answers live in the page source for display; XP is still awarded only by the server, once per question.
- Nothing stops one person making a startup account and a student account to trade XP. Add manual review or payment-gated verification before scale.
- The `submissions` storage bucket and its access rules exist, but the upload button is not wired yet (links only).

## Demo accounts (Try as Student / Try as Startup)
The demo buttons on the login screen run entirely in the browser (no Supabase session). In `index.html` the cloud layer routes demo users to the original local logic through `isDemo()` (email ends in `@scope.app` and no real session). Quiz, swipe/apply, wallet, posting, hiring and approvals all work locally for demo users; chat needs a real account. Real users are unaffected.
