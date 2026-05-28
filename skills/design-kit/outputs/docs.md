# PF Docs Output Guide

> **This file is not yet written.** Until it is, use the guidance below as a fallback.

---

## Current Guidance: html.md Category 3

This file is not yet written as a standalone guide. The SKILL.md router loads `outputs/html.md` directly for `doc` output type. Follow **Category 3: Reports & Explainers** in that file for structure guidance.

Apply PF token constraints from `pf-tokens.md` (already loaded by SKILL.md):
- Font: Plus Jakarta Sans exclusively
- Colors: CSS custom properties from pf-tokens.md only — no hardcoded hex outside `:root`
- For PPTX/DOCX outputs: reference logo PNG files per `pf-logo.md`

---

## What docs.md will cover when written

- Document-specific layout rules (margins, heading hierarchy, page flow)
- One-pager structure (single-page constraint, hierarchy of information)
- Brochure patterns (two-column, call-to-action, brand-forward)
- PPTX/DOCX output guidance with logo and font embedding rules
- Report structure (exec summary, body, appendix)
