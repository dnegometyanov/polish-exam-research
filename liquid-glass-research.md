# Apple Liquid Glass UI Design Language -- Comprehensive Research

> Introduced at WWDC 2025 (June 9, 2025) for iOS 26, iPadOS 26, macOS Tahoe 26, watchOS 26, tvOS 26.
> The most significant visual overhaul since iOS 7 (2013).

---

## 1. Visual Characteristics

Liquid Glass is a **translucent material that refracts content from below, reflects ambient light from around it, and has a lensing effect along its edges**. It mimics the optical properties of real glass -- most notably **refraction** based on Snell's Law (n1*sin(theta1) = n2*sin(theta2), with glass n=1.5).

### Core Visual Properties

| Property | Description |
|----------|-------------|
| **Refraction/Lensing** | Background elements are bent and warped at element edges (not scattered). Creates a gel-like feel. |
| **Transparency** | See-through backgrounds at very low opacity (5-20% white) |
| **Blur** | Frosted glass blur on content behind elements |
| **Specular Highlights** | Light reflections that respond to device motion and tilt |
| **Adaptive Shadows** | Depth shadows that separate glass from content |
| **Color Tinting** | Glass can be tinted with different colors while maintaining transparency |
| **Edge Distortion** | Content near curved edges appears subtly warped as if light passes through a different refractive index |

### Three Composition Layers (per Apple)

1. **Highlight** -- Light casting and movement (specular reflections)
2. **Shadow** -- Added depth for separation between foreground and background
3. **Illumination** -- The flexible, adaptive properties of the material

### Two Variants

- **Regular** -- Most versatile; has adaptive behaviors for light/dark mode
- **Clear** -- Does not have adaptive behaviors; stays consistent

---

## 2. CSS / Web Implementations

### 2.1 Basic Glass Card (Pure CSS)

```css
.glass-card {
  position: relative;
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 20px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
  color: #fff;
  overflow: hidden;
}
```

### 2.2 Enhanced Glass with Shine Effect

```css
.glass {
  position: relative;
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(2px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.8);
  border-radius: 2rem;
  box-shadow:
    0 8px 32px rgba(31, 38, 135, 0.2),
    inset 0 4px 20px rgba(255, 255, 255, 0.3);
}

.glass::after {
  content: '';
  position: absolute;
  top: 0; left: 0;
  width: 100%; height: 100%;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 2rem;
  backdrop-filter: blur(1px);
  box-shadow:
    inset -10px -8px 0px -11px rgba(255, 255, 255, 1),
    inset 0px -9px 0px -8px rgba(255, 255, 255, 1);
  opacity: 0.6;
  z-index: -1;
  filter: blur(1px) drop-shadow(10px 4px 6px black) brightness(115%);
}
```

### 2.3 Three-Layer System (DesignFast approach)

**Layer 1: Base Glass Structure**
```css
.glass-card {
  position: relative;
  border-radius: 20px;
  isolation: isolate;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  box-shadow: 0px 6px 24px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(1px);
  -webkit-backdrop-filter: blur(1px);
  border: 1px solid rgba(255, 255, 255, 0.3);
}
```

**Layer 2: Inner Shadow (::before)**
```css
.glass-card::before {
  content: '';
  position: absolute;
  inset: 0;
  z-index: 0;
  border-radius: 20px;
  box-shadow: inset 0 0 20px -5px rgba(255, 255, 255, 0.6);
  background: rgba(255, 255, 255, 0.05);
}
```

**Layer 3: Backdrop Distortion (::after)**
```css
.glass-card::after {
  content: '';
  position: absolute;
  inset: 0;
  z-index: -1;
  border-radius: 28px;
  backdrop-filter: blur(8px);
  filter: url(#glass-distortion);
  isolation: isolate;
}
```

### 2.4 SVG Filter for Refraction/Distortion

```xml
<svg xmlns="http://www.w3.org/2000/svg" width="0" height="0"
     style="position:absolute; overflow:hidden">
  <defs>
    <filter id="glass-distortion" x="0%" y="0%" width="100%" height="100%">
      <feTurbulence type="fractalNoise" baseFrequency="0.008 0.008"
                    numOctaves="2" seed="92" result="noise"/>
      <feGaussianBlur in="noise" stdDeviation="2" result="blurred"/>
      <feDisplacementMap in="SourceGraphic" in2="blurred" scale="77"
                        xChannelSelector="R" yChannelSelector="G"/>
    </filter>
  </defs>
</svg>
```

### 2.5 Advanced SVG Filter Pipeline (Displacement Map approach)

This is the most physically accurate approach, using pre-generated displacement maps:

```xml
<svg colorInterpolationFilters="sRGB">
  <filter id="liquid-glass-button">
    <!-- Base blur -->
    <feGaussianBlur in="SourceGraphic" stdDeviation="1" result="blurred_source"/>

    <!-- Load displacement map (pre-rendered PNG encoding surface normals) -->
    <feImage href="/displacement-map.png"
             x="0" y="0" width="300" height="56"
             result="displacement_map"/>

    <!-- Refraction effect -->
    <feDisplacementMap in="blurred_source" in2="displacement_map"
                       scale="55"
                       xChannelSelector="R" yChannelSelector="G"
                       result="displaced"/>

    <!-- Saturation boost -->
    <feColorMatrix in="displaced" type="saturate" values="50"
                   result="displaced_saturated"/>

    <!-- Specular rim image -->
    <feImage href="/specular.png"
             x="0" y="0" width="300" height="56"
             result="specular_layer"/>

    <!-- Soften specular edges -->
    <feGaussianBlur in="specular_layer" stdDeviation="1"
                    result="specular_layer_blurred"/>

    <!-- Mask -->
    <feComposite in="displaced_saturated" in2="specular_layer_blurred"
                 operator="in" result="specular_saturated"/>

    <!-- Final blend -->
    <feBlend in="specular_saturated" in2="displaced" mode="normal"/>
  </filter>
</svg>
```

**Displacement map color encoding:**
- R channel = X-axis displacement (128 = neutral)
- G channel = Y-axis displacement (128 = neutral)
- 0 -> -scale, 128 -> 0, 255 -> +scale

### 2.6 CSS Theme Tokens (for framework use)

```css
@theme {
  --btn-radius: 60px;
  --btn-content-bg: hsl(0 100% 100% / 15%);
  --btn-filters: blur(4px) brightness(150%);
  --liquid-glass-filters: url(#liquid-glass-button) brightness(150%);
}
```

### 2.7 Liquid Shine Animation

```css
.glass-card::before {
  content: "";
  position: absolute;
  top: -50%; left: -50%;
  width: 200%; height: 200%;
  background: radial-gradient(
    circle at center,
    rgba(255, 255, 255, 0.2),
    transparent 70%
  );
  animation: liquidMove 6s infinite linear;
}

@keyframes liquidMove {
  0%   { transform: rotate(0deg) translate(0, 0); }
  50%  { transform: rotate(180deg) translate(50px, 50px); }
  100% { transform: rotate(360deg) translate(0, 0); }
}
```

### 2.8 Tailwind CSS Implementation

```html
<div class="
  bg-white/10
  backdrop-blur-lg
  border border-white/20
  shadow-lg
  rounded-[16px]
  backdrop-blur-[1px]
">
  <!-- Glass content -->
</div>
```

SVG filter for Tailwind distortion:
- `feTurbulence` with `baseFrequency="0.02"`
- `feDisplacementMap` with `scale="20"`
- 15-second infinite animation shifting background-position

### 2.9 CSS Application for Refraction

```css
.glass-panel {
  backdrop-filter: url(#liquidGlassFilterId);
}
```

### Notable CodePen Demos & GitHub Projects

- **CodePen**: Pure CSS iOS 26 Liquid Glass Effect by daftplug: `codepen.io/daftplug/pen/QwbaYGO`
- **CodePen**: Liquid Glass Apple by wprod: `codepen.io/wprod/pen/raVpwJL`
- **CodePen**: Apple Liquid Glass UI (2025) by samarkandiy: `codepen.io/samarkandiy/pen/yyNvNGQ`
- **CodePen**: Apple Liquid Glass Effect by kevinbism: `codepen.io/kevinbism/pen/vEOpvjw`
- **GitHub**: nikdelvin/liquid-glass -- Pixel-perfect recreation with LiquidGlass, LiquidText, LiquidButton components (Astro + Tailwind + Anime.js)
- **GitHub**: yanglei1826877278/liquid-glass -- Generator for liquid glass effects
- **Web tool**: liquid-glass.pro -- Online generator with customizable parameters
- **React library**: liquid-glass-js.com -- Liquid Glass Card Component for React

### Browser Support

| Browser | Support |
|---------|---------|
| Chrome 76+ | Full (SVG backdrop-filter) |
| Firefox 103+ | Full |
| Edge 79+ | Full |
| Safari 15+ | **Partial** -- auto-fallback to basic glassmorphism; SVG filters in backdrop-filter NOT supported |
| Mobile Safari | SVG backdrop-filter NOT supported |

**Critical limitation**: True refraction distortion via SVG `feDisplacementMap` in `backdrop-filter` currently works **only in Chrome**. Safari and Firefox restrict backdrop-filter to built-in CSS filter functions only.

---

## 3. Design Tokens & Specific Values

### Color Values

| Token | Light Mode | Dark Mode |
|-------|-----------|-----------|
| Glass background | `rgba(255, 255, 255, 0.05)` to `rgba(255, 255, 255, 0.20)` | `rgba(0, 0, 0, 0.15)` to `rgba(0, 0, 0, 0.30)` |
| Glass border | `rgba(255, 255, 255, 0.3)` | `rgba(255, 255, 255, 0.1)` to `rgba(255, 255, 255, 0.2)` |
| Inner highlight | `rgba(255, 255, 255, 0.6)` | `rgba(255, 255, 255, 0.15)` |
| Outer shadow | `rgba(0, 0, 0, 0.2)` to `rgba(0, 0, 0, 0.3)` | `rgba(0, 0, 0, 0.4)` to `rgba(0, 0, 0, 0.6)` |
| Specular highlight | `rgba(255, 255, 255, 1.0)` | `rgba(255, 255, 255, 0.5)` |
| Deep shadow accent | `rgba(31, 38, 135, 0.2)` | `rgba(31, 38, 135, 0.35)` |

### Blur Values

| Context | Value |
|---------|-------|
| Subtle/base glass | `1px` - `2px` |
| Standard glass card | `8px` - `10px` |
| Heavy frosted glass | `15px` - `30px` |
| Saturation boost | `saturate(180%)` |
| Brightness multiplier | `brightness(115%)` - `brightness(150%)` |

### Border Radius

| Context | Value |
|---------|-------|
| Small controls/buttons | `8px` - `12px` |
| Standard cards | `20px` |
| Pill/capsule buttons | `60px` (or `border-radius: 9999px`) |
| Outer container (::after) | `28px` (when inner is 20px) |
| Range | `8px` - `32px` |

### Shadows

```css
/* Standard outer shadow */
box-shadow: 0 8px 32px rgba(0, 0, 0, 0.2);

/* With accent color */
box-shadow: 0 8px 32px rgba(31, 38, 135, 0.2);

/* Inner glow */
box-shadow: inset 0 0 20px -5px rgba(255, 255, 255, 0.6);

/* Combined (outer + inner highlight) */
box-shadow:
  0 8px 32px rgba(31, 38, 135, 0.2),
  inset 0 4px 20px rgba(255, 255, 255, 0.3);

/* Subtle depth */
box-shadow: 0px 6px 24px rgba(0, 0, 0, 0.2);
```

### SVG Filter Values

| Parameter | Value | Purpose |
|-----------|-------|---------|
| feTurbulence baseFrequency | `0.008` - `0.02` | Wave pattern density |
| feTurbulence numOctaves | `2` | Noise complexity |
| feGaussianBlur stdDeviation | `1` - `2` | Edge softness |
| feDisplacementMap scale | `20` - `77` (up to `55` for buttons) | Distortion intensity |
| feColorMatrix saturate | `50` | Color vibrancy |
| xChannelSelector | `R` | Red channel for X displacement |
| yChannelSelector | `G` | Green channel for Y displacement |

### Transition/Animation

```css
transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
```

---

## 4. Component Patterns

### Tab Bar

- **Floats above content** as a Liquid Glass layer
- **Shrinks on scroll down** -- minimizes to keep focus on content
- **Expands fluidly** when user scrolls back up
- On iPhone: bottom tab bar with glass treatment
- On iPad: can adapt between tab bar and sidebar automatically
- **New**: Dedicated Search tab on iOS
- **New**: `tabViewBottomAccessory` for persistent controls (e.g., music player) above the tab bar
- Avoid screen-specific actions in persistent navigation
- Uses symbols (icons) over text for consistent cross-platform recognition

### Navigation Bar

- **Transparent** background with liquid glass buttons
- Buttons within the nav bar are individual glass elements
- Gives more space to content -- content extends edge to edge
- Remove unnecessary backgrounds and borders from custom implementations

### Cards

- Semi-transparent background with blur
- Rounded corners (typically 20px)
- Subtle white border
- Outer shadow for depth
- Inner highlight for the glass reflection effect

### Buttons

- **Capsule shape preferred** on iOS/iPadOS (radius = half container height)
- **Rounded rectangles** for macOS Mini/Small/Medium controls
- macOS Large and X-Large controls adopt capsule shape
- Hover effects with glass intensification
- Three shape types: Fixed (constant radius), Capsule (half-height radius), Concentric (radius - padding from parent)

### Modals / Sheets

- **Spring from triggering action** (not always from bottom)
- **Dimming layer** signals modality when sheet appears
- Glass + dimming centers user attention
- Material becomes **more opaque and larger** during deep engagement
- Clear source-to-surface relationship

### Action Sheets

- Now "spring directly from the triggering action"
- Establish clear spatial relationship between trigger and result

### Controls (Toggles, Sliders, Pickers)

- Transform into liquid glass during interaction
- Segmented pickers use glass treatment
- Sliders get glass treatment on the thumb/track

### Scroll Edge Effects

- **Soft (Default on iOS/iPadOS)**: Subtle transitions for interactive elements
- **Hard (macOS)**: Stronger boundaries for text controls and pinned headers
- Never mix different edge effect styles

---

## 5. iPad-Specific Patterns

### Floating Sidebar

- **Inset and built with Liquid Glass**
- Refracts content behind it while reflecting content and wallpaper from around it
- Content extends to the edge for immersive experience underneath the sidebar
- Creates mirrored copies of background content, blurs them, and places under glass
- Can be toggled between sidebar and tab bar views

### Split Views

- Search appears at the **trailing edge of the navigation bar** for split views
- Sidebar + content area with glass treatment on the sidebar panel

### Toolbars

- Floating above content like other glass elements
- iPad bridges between iPhone utility and Mac expansiveness
- Device context: wide, expansive canvas with depth

### Tab Bar on iPad

- Adapts between tab bar and sidebar using `UITab` and `UITabGroup`
- Automatic adaptivity -- switch between modes
- Tab bar + sidebar form cohesive navigation language

---

## 6. Typography

### System Fonts

- **SF Pro** remains the primary system font for iOS 26
- **SF Pro** features: 9 weights, italics, small caps, fractions, inferior/superior numerals, indices, arrows
- **New York** available as a serif alternative

### Font Weights

| Weight | Recommended Use |
|--------|----------------|
| Ultralight | Avoid (hard to read, especially small) |
| Thin | Avoid |
| Light | Avoid |
| **Regular** | Body text, standard content |
| **Medium** | Emphasized body text |
| **Semibold** | Subheadings, important labels |
| **Bold** | Headings, navigation titles |
| Heavy | Sparingly for impact |
| Black | Sparingly for maximum emphasis |

### Key Changes in iOS 26

- **Bolder text rendering** for improved clarity over Liquid Glass
- **Left-aligned typography** in critical moments (alerts, onboarding)
- Strengthened structure and readability to maintain contrast against translucent backgrounds
- Default text size: **17pt**
- Support **Dynamic Type** for accessibility
- Use high-contrast text (white or black) to ensure readability over glass

### Width Variants

- SF Pro available in: **Condensed**, **Regular**, **Expanded** widths

---

## 7. Dark Mode vs Light Mode

### Adaptation Behavior

- Liquid Glass **automatically adapts** to light and dark modes
- The **Regular** variant has adaptive behaviors
- The **Clear** variant does **not** adapt (remains consistent)
- System colors are adjusted across Light, Dark, and **Increased Contrast** appearances

### Light Mode

- Glass backgrounds: `rgba(255, 255, 255, 0.05)` to `rgba(255, 255, 255, 0.20)`
- Stronger white highlights and borders
- Subtle outer shadows
- Brightness boost to maintain luminosity

### Dark Mode

- Glass backgrounds shift to darker tints or slightly higher opacity whites
- **Critical**: If background is too dark, glass becomes invisible -- use a slightly opaque white tint instead of black shade
- Inner highlights reduced in intensity
- Outer shadows deepened
- Border opacity reduced but still present
- Extra care needed for contrast

### App Icon Modes

Apple requires icon support for three appearance modes:
1. **Default** (standard appearance)
2. **Clear** (transparent/glass look)
3. **Tinted** (user-selected tint color)

The **silhouette must remain identical** across all modes so recognition never drops.

### CSS Implementation Pattern

```css
:root {
  --glass-bg: rgba(255, 255, 255, 0.15);
  --glass-border: rgba(255, 255, 255, 0.3);
  --glass-shadow: rgba(0, 0, 0, 0.2);
  --glass-highlight: rgba(255, 255, 255, 0.6);
  --glass-blur: 10px;
}

@media (prefers-color-scheme: dark) {
  :root {
    --glass-bg: rgba(255, 255, 255, 0.08);
    --glass-border: rgba(255, 255, 255, 0.15);
    --glass-shadow: rgba(0, 0, 0, 0.5);
    --glass-highlight: rgba(255, 255, 255, 0.2);
    --glass-blur: 12px;
  }
}

.glass-element {
  background: var(--glass-bg);
  border: 1px solid var(--glass-border);
  box-shadow: 0 8px 32px var(--glass-shadow),
              inset 0 0 20px -5px var(--glass-highlight);
  backdrop-filter: blur(var(--glass-blur));
  -webkit-backdrop-filter: blur(var(--glass-blur));
  border-radius: 20px;
}
```

---

## 8. Key Design Principles

1. **Glass floats above content** -- it is a navigation/control layer, not a content layer
2. **Never use glass on glass** -- stacking Liquid Glass elements creates clutter and confusion
3. **Content extends edge to edge** underneath the glass layer
4. **Embrace translucency** while maintaining strong text and icon contrast
5. **Test in both light and dark modes** separately
6. **Performance**: Restrict glass effects to a small number of floating UI elements (toolbars, modals, nav bars) -- heavy use of SVG backdrop-filter is expensive on low-power devices
7. **Concentric shapes**: When nesting glass elements, inner radius = outer radius - padding
8. **Full adoption required** by iOS 27 -- Apple stated the option to retain old designs will be removed

---

## 9. Performance Considerations for Web

- Heavy `backdrop-filter` + SVG filters cause frame drops on low-power devices
- Limit glass effects to **small, floating UI elements** (toolbars, modals, nav bars)
- SVG `feDisplacementMap` in `backdrop-filter` is **Chrome-only**
- For Safari/Firefox fallback, use basic glassmorphism (blur + semi-transparent bg)
- Filter dimensions don't auto-adjust -- ensure SVG filter images match element size
- Consider `will-change: transform` for GPU acceleration
- Test on mobile devices where GPU resources are limited

---

## Sources

- [Apple Newsroom: Delightful and Elegant New Software Design](https://www.apple.com/newsroom/2025/06/apple-introduces-a-delightful-and-elegant-new-software-design/)
- [Meet Liquid Glass -- WWDC25 (Apple Developer)](https://developer.apple.com/videos/play/wwdc2025/219/)
- [Get to Know the New Design System -- WWDC25 (Apple Developer)](https://developer.apple.com/videos/play/wwdc2025/356/)
- [Build a UIKit App with the New Design -- WWDC25](https://developer.apple.com/videos/play/wwdc2025/284/)
- [CSS-Tricks: Getting Clarity on Apple's Liquid Glass](https://css-tricks.com/getting-clarity-on-apples-liquid-glass/)
- [LogRocket: How to Create Liquid Glass Effects with CSS and SVG](https://blog.logrocket.com/how-create-liquid-glass-effects-css-and-svg/)
- [DEV.to: Recreating Apple's Liquid Glass Effect with Pure CSS](https://dev.to/kevinbism/recreating-apples-liquid-glass-effect-with-pure-css-3gpl)
- [DEV.to: Apple's Liquid Glass Revolution -- CSS Guide](https://dev.to/gruszdev/apples-liquid-glass-revolution-how-glassmorphism-is-shaping-ui-design-in-2025-with-css-code-1221)
- [DEV.to: WWDC 2025 Apple's Liquid Glass Design System](https://dev.to/arshtechpro/wwdc-2025-apples-liquid-glass-design-system-52an)
- [DesignFast: CSS Liquid Glass Effects Tutorial](https://designfast.io/liquid-glass)
- [kube.io: Liquid Glass in the Browser -- Refraction with CSS and SVG](https://kube.io/blog/liquid-glass-css-svg/)
- [FlyonUI: Liquid Glass Effects in Tailwind CSS](https://flyonui.com/blog/liquid-glass-effects-in-tailwind-css/)
- [Mitkov Systems: CSS Liquid Glass How To](https://www.mitkov-systems.de/en/blog/css-liquid-glass-how-to)
- [FreeFrontend: 16 CSS Liquid Glass Effects](https://freefrontend.com/css-liquid-glass/)
- [GitHub: nikdelvin/liquid-glass](https://github.com/nikdelvin/liquid-glass)
- [LiquidGlass.pro -- Generator](https://www.liquid-glass.pro/)
- [Liquid Glass JS -- React Component](https://liquid-glass-js.com/)
- [Donny Wals: Exploring Tab Bars on iOS 26](https://www.donnywals.com/exploring-tab-bars-on-ios-26-with-liquid-glass/)
- [InspiringApps: Preparing Your App for iOS 26](https://www.inspiringapps.com/blog/ios-18-lessons-preparing-ios-19-app-development)
- [Apple HIG: Typography](https://developer.apple.com/design/human-interface-guidelines/typography)
- [Apple Developer: Fonts](https://developer.apple.com/fonts/)
- [CodePen: Pure CSS iOS 26 Liquid Glass Effect](https://codepen.io/daftplug/pen/QwbaYGO)
- [CodePen: Liquid Glass Apple](https://codepen.io/wprod/pen/raVpwJL)
- [CodePen: Apple Liquid Glass UI 2025](https://codepen.io/samarkandiy/pen/yyNvNGQ)
- [CodePen: Apple Liquid Glass Effect](https://codepen.io/kevinbism/pen/vEOpvjw)
- [Liquid Glass -- Wikipedia](https://en.wikipedia.org/wiki/Liquid_Glass)
