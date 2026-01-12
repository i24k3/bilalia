# Overview

## Why Astro?

Greetings! Let’s start with the big elephant in the room: **why choose Astro**?

There were a few options: static HTML sites, React, Next.js, and other full frameworks. So why Astro?

My reasoning was simple: this is a **Darul-Uloom site**. Most pages are static, while only a handful need to be dynamically generated — for example:

* `lectures/audio`
* `lectures/video`
* `notifications/`
* `events/`

I only wanted **dynamic routes where necessary**, keeping everything else simple and fast.

Another reason is complexity. Frameworks like React often bring a lot of dependencies — and those dependencies have dependencies. If the site breaks, you may have to debug across multiple layers (React → Express backend → Database → etc.), which can be overwhelming.

> You might ask: *“Wait, Astro also runs on Node and has dependencies — so what’s the difference?”*
> You’re right — dynamic pages require some Node dependencies. But I have **kept dependencies to a minimum**. If memory serves, the main ones are:

* `astro`
* `tailwindcss`
* `dompurify` (for sanitizing HTML from the database)
* …and a few others, **only what’s strictly needed**

---

## Project Structure Philosophy

The project is written **with future developers in mind**. Code and data are separated so that updates don’t require touching the code.

For example, consider the header component. At the top, you’ll find JS objects storing site data:

```javascript
const data = {
  sitetitle: "Foo"
}
```

If the site title changes, you only need to update the object:

```javascript
const data = {
  sitetitle: "Too"
}
```
---

## Project Structure

project follows a clear separation of concerns to keep the codebase
maintainable and easy to understand.

### `src/components`

Contains **reusable UI components** that are not tied to any specific page.
These components are used across multiple routes.

Examples:
- `Header.astro` – site-wide header
- `Footer.astro` – site-wide footer
- `Button.astro` – configurable button component
- `Pagination.astro` – used in events, notifications, and lectures
- `NavLinks.astro` – shared navigation links for header and footer

These components are driven by props and data, not hardcoded content.

---

### `src/sections`

Contains **page-specific sections** used to compose pages.
Each folder corresponds to a page and contains only the sections needed for it.

Examples:
- `sections/home/`
  - `HeroSection.astro`
  - `GallerySection.astro`
  - `NotificationSection.astro`

- `sections/about/`
  - `HistorySection.astro`
  - `FacilitiesSection.astro`
  - `LeadershipSection.astro`

These sections are imported into files inside `src/pages`.

---

### `src/pages`

Contains Astro route files.
Each file represents a URL and is responsible only for:
- data fetching (if required)
- importing sections
- applying layouts

Dynamic routes use Astro’s file-based routing:

- `[slug].astro`
- `[eventpage].astro`
- `[coursepage].astro`
- `[audiopage].astro`
- `[videopage].astro`
> the slug is used as the title for url i.e instead of using `notification/123` a better approach of `notification/academic-session-2026`
Furthermore the `*page].astro` files are used for listing down paginated list of elements.

---

### `src/layout`

Contains shared layout components.

- `BaseLayout.astro` wraps all pages and includes
  common elements like the header and footer.

---

### `src/lib`

Contains external integrations and utilities.

- `supabase.js` – handles database communication

---

### `src/styles`

Global styles and Tailwind configuration.

- `global.css`




## Quesions

time for some questions:

-  Question: Why is AudioCard/VideoCard in sections but NotificationTable in components?
> AudioCard and VideoCard are currently specific to lecture pages, so they live in sections/lectures/ to reflect their limited scope. If these cards are reused elsewhere later, we can move them to components/. In contrast, NotificationTable is designed as a reusable component, so it naturally lives in components/.

- Question: Singular vs plural routes; [slug].astro vs [coursepage].astro
> Singular and plural routes are used to separate pagination from detail pages for a cleaner URL structure. [coursepage].astro explicitly indicates a paginated course page, which improves developer clarity, especially in projects with multiple dynamic routes. This avoids confusion when opening multiple [slug].astro files.

- Question: HeroSection, NotificationSection, GallerySection under sections/home/ instead of components
> HeroSection, NotificationSection, and GallerySection are currently specific to the home page, so they are organized under sections/home/. If they become reusable across other pages in the future, we can relocate them to components/.

- Question: NotFound, supabase.js, mixing reusable pieces
> NotFound.astro serves as both a 404 page and a fallback for missing data. The supabase.js file is currently generic for simplicity, with the option to split queries per table in the future. Some UI pieces remain in sections to reduce unnecessary component proliferation and improve developer experience, especially when they are not widely reused yet.

