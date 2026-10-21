# Astro Website Agent Guide

## Scope and priorities

These instructions apply to the entire repository unless a more specific
`AGENTS.md` exists in a subdirectory.

When instructions conflict, follow this order:

1. The user's current request
2. The nearest applicable `AGENTS.md`
3. Established repository conventions
4. This file

Build the site as a reusable, production-ready Astro project. Prefer clear,
accessible, secure, fast code over clever abstractions.

## Before making changes

- Inspect `package.json`, the lockfile, `astro.config.*`, `tsconfig.json`, the
  relevant source files, and the README before choosing an implementation.
- Check for `src/content.config.*`, `src/live.config.*`, adapters, integrations,
  UI frameworks, CSS tooling, test tooling, and existing utility modules.
- Use the package manager selected by the lockfile. Do not create a second
  lockfile or switch package managers.
- Use existing scripts from `package.json`. Do not assume script names.
- Preserve the project's rendering mode, adapter, directory structure, naming,
  formatting, and component patterns unless the task requires a change.
- Confirm whether a page is prerendered or rendered on demand before using
  browser-only, build-time, or request-time APIs.
- Do not upgrade Astro, integrations, or other dependencies unless requested or
  required for the requested change.

## Change discipline

- Make the smallest coherent change that fully solves the task.
- Do not reformat, rename, or refactor unrelated code.
- Reuse existing components, utilities, tokens, and content models before
  creating new ones.
- Keep public component props, content schemas, routes, and config interfaces
  backward compatible unless a breaking change is explicitly requested.
- Add a dependency only when the platform and existing dependencies cannot
  reasonably solve the problem. Explain the need in the final summary.
- Do not edit generated output such as `dist/`, `.astro/`, coverage reports, or
  generated type files.
- Do not commit secrets, credentials, real personal data, or environment files.
- If a high-impact requirement is genuinely ambiguous, ask before making an
  irreversible or architecture-changing choice.

## Astro implementation

- Use `.astro` components for static, server-rendered, and content-focused UI.
- Use the project's existing UI framework only for interaction that benefits
  from it. Do not introduce a framework for a static component.
- Astro and framework components should render static HTML by default. Add a
  `client:*` directive only when browser-side interaction is required.
- Choose the least eager suitable hydration strategy:
  - `client:load` for immediately visible, immediately interactive UI.
  - `client:idle` for lower-priority interaction.
  - `client:visible` for interaction that can wait until near the viewport.
  - `client:media` when hydration is genuinely conditional on a media query.
  - `client:only` only when server rendering is impossible, with an appropriate
    loading fallback.
- Keep browser globals such as `window`, `document`, and `localStorage` out of
  server and component frontmatter code. Access them only in browser-executed
  code and guard them when necessary.
- Define component props with TypeScript, normally using `interface Props`.
  Avoid `any`; narrow unknown or external data at the boundary.
- Keep layouts responsible for document structure, shared metadata, global
  wrappers, and slots. Keep UI components focused on one responsibility.
- Use Astro routing conventions and the project's existing trailing-slash,
  base-path, and output settings. Do not hardcode deployment paths.
- Generate URLs with `Astro.url`, `Astro.site`, `new URL()`, or a centralized
  helper as appropriate. Do not hardcode production origins in components.
- Use content collections for repeatable, queryable content when they improve
  the data model. Define schemas and loaders in the configuration style already
  used by the installed Astro version. Do not migrate collection APIs as a
  side effect of unrelated work.
- Filter drafts and unpublished entries consistently in production.
- Treat `set:html` as a security boundary. Use it only with trusted or properly
  sanitized HTML; prefer normal escaped template expressions otherwise.
- Use environment variables through the mechanism already established by the
  project. Values exposed to client code are public. Never expose a secret with
  a `PUBLIC_` variable or serialize it into HTML.

## Current Astro APIs

This project runs on Astro 7. Older Astro patterns are common in training
data, so check the installed version in `package.json` and the current docs at
https://docs.astro.build (or the Astro Docs MCP server,
`https://mcp.docs.astro.build/mcp`) instead of relying on memory.

- Do not use APIs removed in Astro 6: `Astro.glob()` (use `import.meta.glob()`),
  `<ViewTransitions />` (use `<ClientRouter />`), `src/content/config.ts`,
  `entry.slug`, `entry.render()`, and `getEntryBySlug()`. Collections are
  defined in `src/content.config.ts` with a loader such as `glob()` or `file()`;
  entries have an `id` and are rendered with `render(entry)` from
  `astro:content`.
- Import `z` from `astro/zod`, not from `astro:content` or `astro:schema`. It is
  Zod 4: use `z.email()` and `z.url()` rather than `z.string().email()`, and the
  `error` option rather than `message`.
- The `.astro` compiler no longer repairs markup. Close every non-void element
  and keep nesting valid (no block elements inside `<p>`).
- HTML is compressed with JSX whitespace rules (`compressHTML: 'jsx'`), so
  whitespace between inline elements on separate lines is dropped. Use `{" "}`
  where a space must survive.
- Markdown and MDX use the Sätteri processor by default. remark and rehype
  plugins run only with `@astrojs/markdown-remark` installed and
  `markdown.processor` set to `unified()`. Keep the processor the project
  already uses.
- `import.meta.env` values are inlined at build time and never coerced
  (`"true"` stays a string). Prefer `astro:env` with a schema for new variables.
- `getStaticPaths()` params must be strings. Inside it, use
  `import.meta.env.SITE` rather than `Astro.site`.
- `src/fetch.ts` is reserved for Astro's advanced routing. Do not create a file
  with that name for anything else.
- Add official integrations with `astro add`, run through the project's package
  manager, rather than wiring them up by hand.

## Components and styling

- Prefer semantic HTML and shallow markup over wrapper-heavy component trees.
- Use design tokens or CSS custom properties for repeated colors, spacing,
  typography, radii, shadows, and layout values.
- Keep global styles and resets centralized. Prefer component-scoped styles for
  component-specific rules.
- Follow the existing CSS methodology. Do not mix in a second styling system
  without a clear requirement.
- Build mobile-first, test narrow and wide viewports, and avoid fixed dimensions
  that break with zoom, localization, or longer content.
- Do not render empty sections or broken controls when optional content is
  absent.
- Keep animation restrained and provide a reduced-motion treatment.

## Accessibility

Accessibility is a completion requirement, not a follow-up.

- Use native semantic elements before ARIA.
- Provide a descriptive page title, a clear main landmark, a useful heading
  hierarchy, and normally one primary `h1`.
- Use `<a>` for navigation and `<button>` for actions.
- Ensure all functionality works with a keyboard and has visible focus styles.
- Provide a skip link when repeated navigation precedes the main content.
- Give every form control an accessible label. Connect instructions and errors
  with `aria-describedby` when appropriate, and expose invalid state
  programmatically.
- Do not use placeholder text as the only label or color as the only indication
  of state.
- Give meaningful images useful alt text and decorative images `alt=""`.
- Give icon-only controls an accessible name.
- Preserve logical focus order. Menus, dialogs, tabs, accordions, and disclosure
  widgets must follow their expected keyboard and focus behavior.
- Do not add a focus trap except for a genuinely modal interface, and restore
  focus when it closes.
- Meet WCAG AA contrast expectations for text, controls, focus indicators, and
  meaningful graphics.
- Respect `prefers-reduced-motion` and avoid autoplaying disruptive media.

## SEO and document metadata

- Set a unique, descriptive `<title>` and a useful meta description for each
  indexable page.
- Centralize shared metadata in a layout or SEO component while allowing
  page-level overrides.
- Use canonical URLs derived from the configured site and actual route. Keep
  canonical, trailing-slash, pagination, and base-path behavior consistent.
- Add Open Graph and social-card metadata where the project supports sharing.
  Canonical and social image URLs must be absolute in production.
- Ensure the root `html` element has the correct `lang`.
- Keep important content and navigation in rendered HTML. Normal navigation
  must use crawlable links.
- Add structured data only when it is useful and fully matches visible content.
- Keep robots and sitemap behavior environment-aware so previews and staging
  deployments are not accidentally indexed.
- Add multilingual canonical and `hreflang` handling only when the site actually
  supports those locales.

## Images, fonts, and media

- Prefer imported local images and Astro's image pipeline when optimization is
  useful. Remember that files in `public/` are copied as-is.
- Configure allowed remote image sources before using optimized remote images.
- Supply meaningful alt text plus intrinsic width and height, or otherwise
  reserve the correct aspect ratio to prevent layout shift.
- Provide accurate `sizes` and responsive sources for large images.
- Lazy-load below-the-fold media. Do not lazy-load the likely LCP image.
- Avoid oversized default assets and unnecessary format variants.
- Prefer self-hosted `woff2` fonts. Include only the families, weights, and
  styles in use.
- Preload only a critical above-the-fold font or image when measurement or page
  structure justifies it.
- Use an intentional `font-display` strategy and a compatible fallback stack.
- Make video and audio usable with the relevant controls, labels, captions, or
  transcripts.

## Performance and client code

- Preserve Astro's default advantage: static HTML and no client runtime unless
  needed.
- Prefer progressive enhancement so core content and navigation remain usable
  if JavaScript fails.
- Avoid large global scripts, duplicate listeners, and framework runtimes for
  isolated behavior.
- Keep third-party scripts optional, consent-aware when applicable, and
  documented. Load analytics, embeds, chat, or marketing code only when enabled.
- Avoid layout shifts from images, fonts, embeds, banners, and asynchronous UI.
- Consider LCP, CLS, and INP when changing above-the-fold content or interaction.
- Do not claim a performance improvement without a measurement or a clear
  reduction in shipped work.

## Forms and security

- Prefer native form behavior and server-side validation, adding client-side
  validation as an enhancement.
- Use suitable input types and autocomplete attributes.
- Keep submitted values after validation errors when safe, and provide clear
  success and failure feedback.
- Validate and sanitize untrusted input on the server. Client validation is not
  a security control.
- Do not log secrets or sensitive form data.
- Do not add a form provider, analytics service, cookie banner, or other
  third-party integration by default. Make integrations configurable.

## Theme and content quality

- Centralize common site settings such as name, default metadata, navigation,
  social links, and footer content.
- Make common customization possible without editing many unrelated files.
- Use realistic, clearly replaceable demo content. Do not publish lorem ipsum,
  broken links, fake testimonials presented as real, or invalid structured data.
- Keep dates, authors, taxonomies, excerpts, and archive behavior consistent.
- Document any new configuration, environment variable, content field, or
  integration in the README or the nearest relevant documentation.

## Verification

Run the checks relevant to the files changed, using repository scripts and the
selected package manager.

At minimum:

1. Run the project's formatting or lint check when available.
2. Run Astro/TypeScript validation when available.
3. Run focused tests for changed behavior, then the broader test suite when
   practical.
4. Run the production build for changes that affect rendering, routes, content,
   configuration, or dependencies.
5. Inspect the final diff for unrelated edits, generated files, secrets, debug
   output, placeholder content, and accidental public API changes.

Before starting a dev server, check whether one is already running. When it
detects an AI agent (on macOS and Linux) or is given `--background`, `astro dev`
runs as a background process; manage it with `astro dev status`,
`astro dev logs`, and `astro dev stop`, and check readiness at `/_astro/status`.

For UI work, also verify the affected pages at representative mobile and desktop
sizes, keyboard operation, visible focus, missing-content states, and browser
console errors. Check the rendered HTML and metadata when SEO or content output
changes.

Do not report a check as passing unless it was run. If a check cannot be run,
state which check was skipped and why.

## Completion report

In the final response:

- Summarize the user-visible result.
- List the important files changed.
- Report the validation commands run and their outcomes.
- Note any remaining risk, follow-up, or unverified behavior.
