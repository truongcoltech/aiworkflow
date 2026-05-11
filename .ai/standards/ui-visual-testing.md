# UI and Visual Testing — V1.0

## Scope — load this standard when the task touches:

- Next.js pages or components
- Layout, spacing, typography, or color changes
- Loading, error, or empty states
- Form interactions, modals, notifications
- Responsive breakpoints

## Required checks per UI change

### Component changes

- [ ] Render in isolation (Storybook or direct render test if available)
- [ ] Verify: default, loading, error, empty states all render without crash
- [ ] Verify: keyboard accessible (tab order, focus rings, Enter/Space activation)
- [ ] Verify: renders at 375px (mobile) and 1280px (desktop) without overflow or clipping

### Page / flow changes

- [ ] Walk the changed user flow end-to-end in a browser
- [ ] Golden path completes without JS error or layout break
- [ ] Error states surface visibly — no silent failure
- [ ] No overflow or clipping at 375px and 1280px

### Content rendering (article body, AI-generated output)

- [ ] Long content does not break layout
- [ ] Vietnamese text and special characters render correctly
- [ ] Workflow status labels and badges are accurate

## Screenshot / visual diff

- MVP: manual screenshot comparison before/after is acceptable.
- Phase 2: automate with Playwright screenshot diff or Chromatic.
- When taking screenshots: capture both mobile and desktop for any layout change.

## Accessibility minimum (MVP)

- All interactive elements keyboard-reachable.
- All images have meaningful `altText` (enforced by `ImageAsset.altText` field).
- Body text meets WCAG AA contrast.
- Form inputs have associated labels.

## When this standard does NOT apply

- Logic-only changes (agent, service, worker) with no UI touch
- Type-only or config-only changes with no rendered output

## Agent note

AI agents do not have browser access. Any task with UI changes must include this item in DONE WHEN:

```
- [ ] Verified in browser at 375px and 1280px (executor must do this manually — agent cannot)
```
