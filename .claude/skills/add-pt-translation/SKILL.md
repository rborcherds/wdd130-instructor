---
name: add-pt-translation
description: Translate an English-only HTML help page in this project to bilingual English/Portuguese, adding language toggle controls to the header and the setLang script. Use when a page in the helps/ directory needs Portuguese translation added.
argument-hint: <path-to-html-file>
arguments: [filepath]
allowed-tools: Read Write
---

## Task

Add Brazilian Portuguese translation to the HTML file at: `$filepath`

Read the file first, then apply all transformations described below, then write the result back.

---

## Background

The site's `helps/styles/styles.css` already contains every CSS rule needed for bilingual pages:

- `html[lang="en"] .lang-pt { display: none; }` — hides Portuguese content when English is active
- `html[lang="pt"] .lang-en { display: none; }` — hides English content when Portuguese is active
- `.header-layout` — CSS grid, two columns: nav on the left (`1fr`), language selector on the right (`auto`)
- `.nav-header`, `.lang-selector`, `.lang-btn`, `.lang-btn.active` — fully styled

No CSS changes are needed. Only the HTML file changes.

---

## Transformation rules

### 1. Header

Replace whatever `<header>` exists with this exact structure:

```html
<header class="header-layout">

  <nav class="nav-header">
    <a href="index.html">
      <span class="lang-en">Resources Home</span>
      <span class="lang-pt">Início dos Recursos</span>
    </a>
  </nav>
  <div class="lang-selector">
    <button onclick="setLang('en')" class="lang-btn" id="btn-en">English</button>
    <button onclick="setLang('pt')" class="lang-btn" id="btn-pt">Português</button>
  </div>

</header>
```

### 2. All visible text in `<main>` and `<footer>`

Wrap every user-visible text node in a matching `lang-en` / `lang-pt` span pair. Examples:

**Paragraph:**
```html
<!-- Before -->
<p>Some English text here.</p>

<!-- After -->
<p>
  <span class="lang-en">Some English text here.</span>
  <span class="lang-pt">Algum texto em português aqui.</span>
</p>
```

**Heading:**
```html
<!-- Before -->
<h1>Page Title</h1>

<!-- After -->
<h1>
  <span class="lang-en">Page Title</span>
  <span class="lang-pt">Título da Página</span>
</h1>
```

**List item (text only):**
```html
<!-- Before -->
<li>Do this step first.</li>

<!-- After -->
<li>
  <span class="lang-en">Do this step first.</span>
  <span class="lang-pt">Faça esta etapa primeiro.</span>
</li>
```

**Paragraph that contains a link — wrap only the surrounding text, not the URL:**
```html
<!-- Before -->
<p>Read more at <a href="https://example.com">https://example.com</a> for details.</p>

<!-- After -->
<p>
  <span class="lang-en">Read more at</span>
  <span class="lang-pt">Leia mais em</span>
  <a href="https://example.com">https://example.com</a>
  <span class="lang-en">for details.</span>
  <span class="lang-pt">para mais detalhes.</span>
</p>
```

### 3. Do NOT translate or wrap

- `<pre><code>` blocks — always keep as-is; they contain commands students must type exactly
- `<img>` elements — keep `src` and `alt` values unchanged
- Raw URL text inside `<a>` tags when the link text IS the URL (e.g. `https://...`)
- The language button labels (`English` / `Português`) inside `.lang-selector`

---

## Translation guidelines

- Use Brazilian Portuguese (pt-BR), clear and direct, appropriate for university students.
- Do **not** translate technical terms: `commit`, `push`, `pull`, `branch`, `staged`, `origin`, `main`, `clone`, `merge`, `revert`, `sync`, `repository`, `URL`, `git`.
- Do **not** translate product/brand names: VSCode, GitHub, GitHub Pages, Notepad, Microsoft Teams, BYU Pathway Worldwide, BYU-Idaho, WDD 130.
- Preserve all HTML attributes exactly (`href`, `src`, `alt`, `class`, `id`, `target`, `rel`).
- Keep `alt` attributes on images in English.

---

## 4. Script — add immediately before `</body>`

```html
  <script>
    function setLang(lang) {
      document.documentElement.lang = lang;
      localStorage.setItem('preferredLang', lang);
      document.getElementById('btn-en').classList.toggle('active', lang === 'en');
      document.getElementById('btn-pt').classList.toggle('active', lang === 'pt-BR');
    }

    const savedLang = localStorage.getItem('preferredLang') || 'en';
    setLang(savedLang);
  </script>
```

---

## Reference implementations

Two completed examples live in this project:

- `helps/git-github.html` — trilingual (EN/PT/ES) long page with nested lists, headings, images, external links. **Note:** this page also has Spanish (`lang-es` spans and a third button) — use only the EN/PT span pattern when adding Portuguese-only translation.
- `helps/retaking-class.html` — shorter page with ordered list, `<pre><code>` block, mixed paragraph/link content

Read either file if you need to verify a pattern before applying it.

To add Spanish on top of a Portuguese-translated page, use the `add-es-translation` skill instead.
