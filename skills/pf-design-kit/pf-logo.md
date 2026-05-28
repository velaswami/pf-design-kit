# PF Brand Assets

Never generate or create logos. Always use Prismforce logos exactly as provided. Logo files are available in four variants for different contexts.

---

## Logo Variants

| Variant | Use When | File |
|---------|----------|------|
| **Light Background** | Dark text on white/light backgrounds | `assets/logos/light-background.svg` |
| **Dark Background** | White text on navy/dark backgrounds | `assets/logos/dark-background.svg` |
| **On Light Background** | Compact logo + text on light surfaces | `assets/logos/on-light-background.svg` |
| **On Dark Background** | Compact logo + text on dark surfaces | `assets/logos/on-dark-background.svg` |

---

## Logo Usage Rules

1. **Lock aspect ratio before resizing**: Never set width and height independently — always set one dimension and let the other scale. In HTML: set only `width` or only `height`, never both. In PowerPoint/Word: hold Shift while dragging, or enable "Lock aspect ratio" in Format Picture. Minimum 100px wide.
2. **Breathing room**: Add min 16px padding on all sides
3. **Color**: Logo colors are locked — no recoloring or fill changes
4. **Multiple logos**: Use mark-only version (embedded below) for compact spaces, full logo+text for primary placement
5. **Web outputs**: Use inline SVG or `assets/logos/*.svg` file paths
6. **PPTX/DOCX outputs**: Reference PNG files at `assets/logos/dark-background.png`, `assets/logos/light-background.png`, `assets/logos/on-dark-background.png`, `assets/logos/on-light-background.png`

---

## Logo Mark SVG (Embedded)

Prismforce mark for inline use (SVG, responsive):

```svg
<svg width="48" height="45" viewBox="0 0 37 31" fill="none" xmlns="http://www.w3.org/2000/svg">
  <path fill-rule="evenodd" clip-rule="evenodd" d="M8.85639 30.0482L0.941011 30.3378C0.311519 30.5333 -0.230279 29.8542 0.099932 29.2838L13.8102 6.80497C14.2254 6.08762 15.3252 6.51776 15.1432 7.32655L9.34806 29.5156C9.29129 29.7683 9.10367 29.9714 8.85639 30.0482Z" fill="#1F40CB"/>
  <path fill-rule="evenodd" clip-rule="evenodd" d="M16.848 8.51117L18.7497 0.555848C18.9018 -0.0802717 19.7526 -0.204505 20.0804 0.361426L31.9971 20.9411C32.4119 21.6574 31.4935 22.3967 30.8822 21.8382L17.0638 9.21408C16.8694 9.03648 16.7868 8.76728 16.848 8.51117Z" fill="#00A2ED"/>
  <path fill-rule="evenodd" clip-rule="evenodd" d="M35.9366 29.902L12.0638 30.0138C11.235 30.0179 11.0511 28.8512 11.8412 28.6002L29.7754 22.8974C30.0258 22.8177 30.2997 22.88 30.4909 23.06L36.4295 28.651C36.9055 29.0992 36.5906 29.8989 35.9366 29.902Z" fill="#1F40CB"/>
</svg>
```
