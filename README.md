# Recently Added NF Overlay for Kometa

Netflix-style **"Recently Added"** overlay for [Kometa](https://kometa.wiki) to highlight newly added media in Plex.

![Recently Added Overlay Example](https://i.imgur.com/QSvQ3uc.jpeg)

## Features
- Adds a **Netflix-style "Recently Added"** badge to your Plex posters.
- Works automatically — items lose the overlay after **20 days**.
- Simple YAML configuration.
- Compatible with **Movies**, **TV Shows**, and any other video library.

## Repository Structure
```plaintext
/
├── overlays/
│   └── recently_added.png   # The overlay image
└── recently_added.yml       # The Kometa overlay config
```

## Installation

### 1. Download / Clone
```bash
git clone https://github.com/YOURUSERNAME/Recently-Added-NF-for-Plex.git
```

### 2. Place Files in Kometa Config
Copy the `overlays/recently_added.png` image and `recently_added.yml` into your Kometa `config/` folder (or wherever your overlays live).

### 3. Edit config.yml

#### Apply to ALL Libraries
Add to your global `overlays:` section:

```yaml
overlays:
  Recently Added:
    overlay_path:
      - recently_added.yml
```

This will run on all video libraries.

#### Apply to Specific Libraries Only
Instead of adding it globally, target specific libraries:

```yaml
libraries:
  Movies:
    overlay_path:
      - recently_added.yml
  TV Shows:
    overlay_path:
      - recently_added.yml
```

## recently_added.yml
```yaml
overlays:
  Recently Added:
    plex_all: true
    filters:
      added: 20
    overlay:
      name: Recently Added
      file: overlays/recently_added.png
      horizontal_align: left
      vertical_align: bottom
      horizontal_offset: 0
      vertical_offset: 0
```

## Running Kometa
To apply overlays only (faster):

```bash
kometa --run overlays
```

## FAQ

**Q: Will this affect my other overlays?**  
A: No — Kometa merges overlays. This will simply add "Recently Added" alongside your existing overlays.

**Q: Can I change the days?**  
A: Yes — change `added: 20` to any number of days you prefer.

**Q: Can I move the overlay to the right side?**  
A: Yes — update `horizontal_align: right` in the YAML.

## Support
If you post your setup on Reddit, please credit this repo and link here so others can use it.
