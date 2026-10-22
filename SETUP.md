# DREAM BY OISHY — free GitHub Pages deployment

This project is a static storefront. GitHub Pages hosts the site for free. Secure online product management uses Supabase Auth + a database with Row Level Security (free tier subject to provider limits).

## Files
- `index.html` — storefront, cart, WhatsApp checkout, and admin UI.
- `supabase-setup.sql` — database table and security policies.
- `SETUP.md` — this guide.

## A. Create GitHub account and publish the static site
1. Sign up at https://github.com/.
2. Click https://github.com/new and create a **Public** repository named `dream-by-oishy`.
3. Upload `index.html` to the repository root. Keep the name exactly `index.html`.
4. Open repository **Settings → Pages**.
5. Under Build and deployment, choose **Deploy from a branch**, branch `main`, folder `/(root)`, and Save.
6. Wait for deployment. The URL normally looks like `https://YOUR-GITHUB-USERNAME.github.io/dream-by-oishy/`.

## B. Configure Supabase for secure product management
1. Create a project at https://supabase.com/ (free plan availability/limits may change).
2. In the project dashboard, open **SQL Editor → New query**.
3. Open `supabase-setup.sql`. Replace every occurrence of `YOUR_ADMIN_AUTH_USER_UUID` with the real UUID of the admin account you create in the next step, then run the SQL. Do not run it before replacing the UUID.
4. In Supabase, go to **Authentication → Users** and create an admin user with an email and a strong password. Copy the user's UUID. If you created the user before running the SQL, use that UUID in the SQL file.
5. Open **Project Settings → API** (or the API Keys page). Copy the Project URL and the publishable/anon key. Never use a `service_role` or secret key in browser code.
6. Edit `index.html` and find `const CONFIG = { ... }` near the top of the script:
   - `whatsapp`: your WhatsApp number with country code, digits only. Bangladesh example format: `8801XXXXXXXXX`; don't include `+`, spaces, or hyphens.
   - `currency`: keep `BDT`.
   - `supabaseUrl`: your Supabase Project URL.
   - `supabaseAnonKey`: your publishable/anon key.
   - `adminUserId`: the exact admin user's UUID.
7. Commit the updated `index.html` to GitHub. GitHub Pages redeploys automatically.
8. Visit `https://YOUR-GITHUB-USERNAME.github.io/dream-by-oishy/#admin`. Sign in with the Supabase admin email/password. Add and delete products. Database policies restrict writes to the UUID specified in the SQL.

### Important security notes
- Never put a Supabase `service_role` key or any secret key in `index.html`.
- The anon/publishable key is intended for browser use; Row Level Security policies protect the table.
- The admin UUID in the HTML is not itself a secret. The database RLS policies are the security boundary. Use a strong password and enable MFA if available.
- If you change the admin account, update the UUID in both the SQL policies and `index.html`.
- This setup supports adding, editing, and deleting products, including price, category, stock, color, and image URL. You can use an image URL for product photos; upload the image to a public image host you trust.
- Product list changes are stored in Supabase, so they appear to all visitors without re-uploading `index.html`.
- WhatsApp checkout opens a prefilled message; the customer must press Send in WhatsApp. No order is recorded in a database by this static site.
- A free GitHub Pages site is public and does not provide a custom domain or server-side secrets.

## C. Test before sharing
- Open the public site on a phone.
- Add an in-stock item, open the bag, and change quantity.
- Enter a test name, phone and address.
- Configure your real WhatsApp number before trying checkout; don't publish with the number blank if you want to accept orders.
- Test `#admin` in a private/incognito window: without the admin login, product writes must not work.
- Confirm a non-admin authenticated Supabase user cannot insert/update/delete products. The RLS policies are intended to enforce this.

## Troubleshooting
- **Sample products remain visible:** Supabase settings are still placeholders, or the database connection failed.
- **No products / database warning:** confirm the table exists, the anon key and project URL are correct, and the SELECT policy exists.
- **Admin can sign in but cannot add/delete:** check that the UUID in `index.html` exactly matches the UUID in the SQL policies.
- **WhatsApp button says configure number:** add your full number in `CONFIG.whatsapp` and commit the file.
