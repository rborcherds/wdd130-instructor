---
name: add-es-translation
description: Translate an English/Portuguese bilingual HTML help page in this project to trilingual English/Portuguese/Spanish, adding a Spanish button to the language selector and lang-es spans throughout. Use when a page in the helps/ directory needs Spanish translation added.
argument-hint: <path-to-html-file>
arguments: [filepath]
allowed-tools: Read Write
---

## Task

Add Spanish translation to the HTML file at: `$filepath`

Read the file first, then apply all transformations described below, then write the result back.

---

## Background

The site's `helps/styles/styles.css` already contains all CSS rules needed for trilingual pages:

- `html[lang="en"] .lang-pt, html[lang="en"] .lang-es { display: none; }` — hides PT and ES when English is active
- `html[lang="pt"] .lang-en, html[lang="pt"] .lang-es { display: none; }` — hides EN and ES when Portuguese is active
- `html[lang="es"] .lang-en, html[lang="es"] .lang-pt { display: none; }` — hides EN and PT when Spanish is active
- `.header-layout`, `.nav-header`, `.lang-selector`, `.lang-btn`, `.lang-btn.active` — fully styled

No CSS changes are needed. Only the HTML file changes.

---

## Transformation rules

### 1. Header — add Spanish button

Add a third button to the `.lang-selector` div:

```html
<div class="lang-selector">
  <button onclick="setLang('en')" class="lang-btn" id="btn-en">English</button>
  <button onclick="setLang('pt')" class="lang-btn" id="btn-pt">Português</button>
  <button onclick="setLang('es')" class="lang-btn" id="btn-es">Español</button>
</div>
```

Also add a `lang-es` span to the nav link:

```html
<nav class="nav-header">
  <a href="index.html">
    <span class="lang-en">Resources Home</span>
    <span class="lang-pt">Início dos Recursos</span>
    <span class="lang-es">Inicio de Recursos</span>
  </a>
</nav>
```

### 2. All visible text in `<main>` and `<footer>`

For every existing `lang-en` / `lang-pt` span pair, add a matching `lang-es` span immediately after the `lang-pt` span with the Spanish translation.

**Paragraph:**
```html
<p>
  <span class="lang-en">Some English text here.</span>
  <span class="lang-pt">Algum texto em português aqui.</span>
  <span class="lang-es">Algún texto en español aquí.</span>
</p>
```

**Heading:**
```html
<h1>
  <span class="lang-en">Page Title</span>
  <span class="lang-pt">Título da Página</span>
  <span class="lang-es">Título de la Página</span>
</h1>
```

**List item:**
```html
<li>
  <span class="lang-en">Do this step first.</span>
  <span class="lang-pt">Faça esta etapa primeiro.</span>
  <span class="lang-es">Haga este paso primero.</span>
</li>
```

**Paragraph with a link — wrap only surrounding text:**
```html
<p>
  <span class="lang-en">Read more at</span>
  <span class="lang-pt">Leia mais em</span>
  <span class="lang-es">Lea más en</span>
  <a href="https://example.com">https://example.com</a>
</p>
```

**`<pre>` blocks** — add a new `<pre class="lang-es">` block immediately after the existing `<pre class="lang-pt">` block with the Spanish translation of the imperative-mood text.

### 3. Do NOT translate or wrap

- The content inside `<pre><code>` blocks that contains exact commands students must type (e.g., `git commit -m`, `git push`, etc.)
- `<img>` elements — keep `src` and `alt` values unchanged
- Raw URL text inside `<a>` tags when the link text IS the URL
- The language button labels (`English` / `Português` / `Español`) inside `.lang-selector`

---

## Translation guidelines

- Use Latin American Spanish, clear and direct, appropriate for university students. Use `usted` (formal) rather than `tú` (informal).
- Do **not** translate technical terms: `commit`, `push`, `pull`, `branch`, `staged`, `origin`, `main`, `clone`, `merge`, `revert`, `sync`, `repository`, `URL`, `git`, `patch`.
- Do **not** translate product/brand names: VSCode, GitHub, GitHub Pages, Notepad, Microsoft Teams, BYU Pathway Worldwide, BYU-Idaho, WDD 130.
- Preserve all HTML attributes exactly (`href`, `src`, `alt`, `class`, `id`, `target`, `rel`).
- Keep `alt` attributes on images in English.
- For links to English-only resources, add `(Solo en inglés)` after the preceding span text.

---

## 4. Script — replace immediately before `</body>`

Replace the existing script with this 3-language version:

```html
  <script>
    function setLang(lang) {
      document.documentElement.lang = lang;
      localStorage.setItem('preferredLang', lang);
      document.getElementById('btn-en').classList.toggle('active', lang === 'en');
      document.getElementById('btn-pt').classList.toggle('active', lang === 'pt');
      document.getElementById('btn-es').classList.toggle('active', lang === 'es');
    }

    function detectBrowserLang() {
      const browserLang = navigator.language || navigator.userLanguage || 'en';
      const lang = browserLang.toLowerCase();
      if (lang.startsWith('pt')) return 'pt';
      if (lang.startsWith('es')) return 'es';
      return 'en';
    }

    const savedLang = localStorage.getItem('preferredLang') || detectBrowserLang();
    setLang(savedLang);
  </script>
```

**Auto-detection logic:** Portuguese and Spanish browsers default to their native language. All other browsers default to English. A previously saved `localStorage` preference always takes priority.

---

## Reference implementation

`helps/git-github.html` — complete trilingual page (EN/PT/ES) with nested lists, headings, images, external links, and `<pre>` blocks.

Read this file if you need to verify any pattern before applying it.
