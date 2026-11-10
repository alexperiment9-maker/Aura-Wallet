# Aura Wallet: Security notes

Honest description of how the app is built, what protects users, and what does not.

## Design

- 100% client-side. The app is a set of static files (HTML, fonts, icons, a service worker). There is no backend, database, API, account system or analytics.
- All user data is kept in the browser's IndexedDB on the user's own device.
- No third-party code is loaded from outside. Fonts and the QR code library (`qr.js`, MIT licence, by Kazuhiko Arase) are bundled in the repo.
- The landing page (`welcome.html`) has the same security rule and no trackers.

## Protections in place

- **Content Security Policy** (in `index.html`): `default-src 'self'`, `connect-src 'self' data: blob:`, `object-src 'none'`, `base-uri 'none'`, `form-action 'none'`. The browser blocks the page from loading code from, or sending data to, any other server.
- **No referrer** is sent when a user opens an outside link (Google Calendar).
- **All text is escaped** before being shown (names, notes, category names, Squad card contents).
- **Imported data is validated**: backups and Squad codes are checked and cleaned field by field; invalid entries are dropped. Squad codes carry a checksum so accidental or casual edits are rejected.
- **Unbreakable categories**: no duplicate or empty names, deleting a category moves its entries to "Other", "Other" cannot be deleted.
- HTTPS is provided by the host (GitHub Pages).

## Known limits (stated plainly)

- Data on the device is **not encrypted** and there is **no app PIN**. The phone's screen lock is the protection.
- Backups are encrypted only if the user sets a password: AES-GCM 256-bit, key derived with PBKDF2-SHA256 (250,000 rounds), all done on the device with the browser's built-in Web Crypto. A forgotten password cannot be recovered. CSV exports are **not encrypted**.
- The Squad code checksum is **not cryptographic**. A determined person can forge a score. The leaderboard is an honour system.
- The CSP allows inline scripts and styles (`'unsafe-inline'`) because the app is a single file. Risk is reduced by escaping all user text and blocking outside connections.
- Browsers can clear site storage (for example when the device is low on space, or if the user clears browsing data). Backups are the only recovery.
- The web host can see standard request logs (IP address, time, browser) for the download of the app files. It never receives user entries.
- On iPhone, Safari may clear data of websites that are not opened for a while. Using the app from the Home Screen icon and making backups is the protection.
- UPI tip links hand over to the user's UPI app. The app cannot confirm or see payments.

## How anyone can verify

1. Open the app once, then switch to aeroplane mode. Everything still works.
2. In desktop Chrome, open DevTools → Network, use the app, and confirm that no request leaves for any other domain.
3. Read the source: it is one readable `index.html`.

## Reporting a problem

Tell the developer through the page or person you got the app link from. Please do not post personal data in public reports.
