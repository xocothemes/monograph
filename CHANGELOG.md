# Changelog

All notable changes to Monograph are documented here.

## 1.0.1 - 2026-10-21

- Updated Astro to 7.4.1 and other dependencies.
- Added `AGENTS.md` with guidance for AI coding assistants.
- Files check out with LF line endings on every platform.

## 1.0.0 - 2026-08-03

- Initial release.
- Text-first home page: a large latest post, a whitespace-separated feed with no thumbnails, and a sticky sidebar holding the about blurb, a subscribe form, featured posts, and categories with post counts.
- Article pages with a single 720px reading column, an "By author in category" byline, a share row above the feature image, copy-link, previous/next navigation, and related posts.
- Read time calculated from the post body at build time, so no post carries it in frontmatter.
- Categories as the only taxonomy: one short configured list, a `/categories/` index with descriptions and counts, and a generated archive per category.
- Category, author, archive, search, about, contact, privacy, and 404 pages.
- Client-side search with a command-palette overlay (`Ctrl`/`⌘ + K` or `/`), tiered ranking across titles, categories, excerpts, and authors, and a dedicated results page. Neither surface lists anything before the first keystroke.
- Markdown and MDX content collections with validated frontmatter, dual light/dark syntax highlighting, a copy button on every code block, and import-free `Callout` and `CodeGroup` components.
- Monochrome design system with a single ink-blue accent, self-hosted Geist, and system-aware light and dark modes behind a header toggle switch that applies before first paint.
- Restrained hover motion on one easing curve: titles pick up the accent, inline links wipe an underline in, arrows lean toward their destination, and icon buttons lift.
- Sticky header that hides while scrolling down and slides back in on the way up.
- Feature images are optional, capped in height, and never appear in a feed.
- Responsive Astro images, canonical URLs, sitemap, RSS, Open Graph, Twitter/X cards, and JSON-LD.
- Accessible skip link, landmarks, labelled controls, a `role="switch"` theme toggle, visible focus states, a focus-trapped mobile menu, and reduced-motion support.
- Provider-neutral newsletter and contact forms that run as working demos until an endpoint is configured.
- Theme tokens in a single CSS block, organized into cascade layers so Tailwind utilities and component styles override them predictably.
