# Saturn Interactive

Client-only WebGL visualization of Saturn and related solar-system context. The app is a single `index.html` with an inline ES module—no bundler, no compile step.

**Production:** [https://content-interactives.github.io/Saturn](https://content-interactives.github.io/Saturn)

---

## Architecture

| Layer | Implementation |
|--------|----------------|
| Rendering | `THREE.WebGLRenderer` (optional antialiasing; reduced on mobile) |
| Overlay labels | `CSS2DRenderer` / `CSS2DObject` (three.js addons) |
| Camera | `PerspectiveCamera` + `OrbitControls` |
| Scene graph | Textured `Mesh` (Saturn), `Points` starfield, grouped ring geometry, optional Earth mesh |
| Module resolution | Native `<script type="importmap">` pointing at unpkg |

Three.js and addons load from CDN via import map:

- `three` → `three@0.162.0` (`build/three.module.js`)
- `three/addons/` → `examples/jsm/` (same version)

Other remote assets: Font Awesome 6.5 (styles only), optional Earth albedo from `threejs.org` examples.

---

## Repository layout

| Path | Role |
|------|------|
| `index.html` | Document shell, inline CSS, UI markup, and the main `type="module"` script |
| `8k_saturn.jpg` | Diffuse map for Saturn (referenced by `TextureLoader`) |
| `Flexi_Telescope.svg` | Branding / mascot image in the UI |
| `package.json` | Dev server script only (`http-server`); not required for production hosting |
| `Standards.md` | Curriculum / NGSS alignment (non-technical mirror of standards copy) |

---

## Local development

Serving over HTTP is required: ES modules and texture loads fail or are inconsistent under `file://` (CORS).

```bash
npm install
npm run dev
```

The dev script runs `npx http-server -p 8081`. Open `http://localhost:8081`.

Equivalent without npm:

```bash
python -m http.server 8000
```

Then open `http://localhost:8000`.

---

## Runtime behavior (summary)

- **Mobile detection** (`userAgent`, touch points, viewport width under 768px): lowers star count, sphere subdivision, caps pixel ratio, disables antialiasing, and adjusts `OrbitControls` (damping, speeds, touch mappings).
- **Saturn**: `SphereGeometry` + `MeshStandardMaterial` with local 8k texture; ambient + directional lighting.
- **Rings**: Procedural multi-band ring meshes (named bands, opacity/color tuned per band); created on demand in the scene.
- **Earth**: Lazy `Mesh` using a preloaded remote texture when the lesson UI requests it.
- **Orbit demo**: Separate scaled Earth/Saturn orbits (~1 yr vs ~29.5 yr period) for calendar/slider sync.
- **Labels / facts**: DOM-driven UI (e.g. fact box, Flexi bubble) coordinated with 3D picks or menu actions inside the module script.

---

## Deployment

Static hosting only (e.g. GitHub Pages): publish the repo root so `index.html` is the default document and asset paths (`./8k_saturn.jpg`, etc.) resolve correctly.

---

## Product integration

CK-12 and other embed targets: see in-repo tracking for production vs master links (placeholders where pending).

- **CK-12 Intent Response** — production / master URLs: pending
- **CK-12 Flexbooks** — book/lesson link: pending

---

## Educational alignment

Subject and NGSS references are summarized in [`Standards.md`](Standards.md) (middle school science, Saturn topic, MS-ESS1-3, MS-ESS1-4, MS-ESS2-4).
