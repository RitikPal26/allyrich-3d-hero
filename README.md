# AllyRich 3D Interactive Hero

An ultra-luxury, full-screen interactive 3D landing page featuring a photorealistic 3D hand holding an iPhone, set against the custom ALLYRICH brand artwork (`#ac9c8d` serif typography) and displaying an opaque OLED screen with the signature white AR emblem.

![AllyRich 3D Hero Preview](assets/allyrich-hero-bg.png)

## Features

- 📱 **Interactive 3D Phone Model**: Photorealistic 3D phone held in hand with dynamic reflections, interactive rotation, and lighting via Spline 3D & WebGL.
- 🎨 **Custom Brand Background**: Edge-to-edge typography background artwork with internal 3D environment dome hidden for true transparency.
- 🛡️ **Zero Background Transparency Bleed**: Built-in fix for Spline's NodeMaterial shader uniform (`nodeU7 = 1.0`) ensuring the phone screen remains 100% solid, pitch-black OLED glass at every 3D angle.
- ⚡ **Zero Dependencies**: Pure HTML5, modern CSS, and vanilla ES modules. Runs instantly with standard Node.js or any static file server.
- 🚫 **No Watermarks**: Native WebGL pipeline watermark removal for clean brand presentation.

---

## Quick Start

### 1. Clone the repository
```bash
git clone https://github.com/RitikPal26/allyrich-3d-hero.git
cd allyrich-3d-hero
```

### 2. Start the local server
Using Node.js:
```bash
npm start
```
Or simply run:
```bash
node server.js
```

### 3. Open in Browser
Navigate to [http://localhost:3000](http://localhost:3000).

---

## Technical Architecture & Shader Fix

In Spline's scene export (`VVdTRfsKk3fopW3m/scene.splinecode`), the device display (`Place your image here 2`) uses a custom compiled `NodeMaterial`:
- Layer 0: Texture layer (mapped to uniform `nodeU7` for layer alpha, `nodeU0` for sampler)
- Layer 1: Video layer (mapped to uniform `nodeU16` for layer alpha, `nodeU9` for sampler)
- Layer 2: Matcap layer (mapped to uniform `nodeU20` for layer alpha = 0.4)
- Layer 3: Light layer (mapped to uniform `nodeU26` for layer alpha = 0.2)

### The Problem
When the WebGL canvas is made transparent (`setClearAlpha(0)`) to reveal the HTML background, Spline leaves Layer 0's compiled alpha uniform (`nodeU7`) at `0.0`. Consequently, only the matcap layer contributes alpha, leaving the screen 59% transparent and causing the background image to bleed through the phone.

### The Solution
[`assets/js/allyrich-3d-hero.js`](assets/js/allyrich-3d-hero.js) explicitly overrides and locks the compiled shader uniforms:
```javascript
if (o.material.uniforms) {
  if (o.material.uniforms.nodeU7) o.material.uniforms.nodeU7.value = 1.0;
  if (o.material.uniforms.nodeU16) o.material.uniforms.nodeU16.value = 0.0;
}
```
And registers an `onBeforeRender` hook so the values are never overwritten by Spline's animation loop.

---

## License

MIT © [Ritik Pal](https://github.com/RitikPal26)
