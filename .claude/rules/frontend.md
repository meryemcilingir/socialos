---
paths:
  - "web/**/*"
---

# Frontend rules

- TypeScript strict; no `any`, no non-null assertions to silence errors.
- Server state through TanStack Query only; no ad-hoc `useEffect` fetching.
- One HTTP client module that sends cookies and the antiforgery header; components never call `fetch` directly.
- Function components and hooks. Forms with React Hook Form + Zod schemas.
- Accessible by default: labels on inputs, keyboard navigation, visible focus states.
- Keep the UI plain until v0.5: no animation libraries or visual polish work unless the issue asks for it.
