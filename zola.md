_Last Updated: 2024-03-25_

### Quick Links
- [Documentation](https://www.getzola.org/documentation)
- [CLI usage](https://www.getzola.org/documentation/getting-started/cli-usage/)
- [Directory structure](https://www.getzola.org/documentation/getting-started/directory-structure/)
- [Configuration](https://www.getzola.org/documentation/getting-started/configuration/)
- [Content](https://www.getzola.org/documentation/content/overview/)
- [Templates](https://www.getzola.org/documentation/templates/overview/)
- [Themes](https://www.getzola.org/documentation/themes/overview/)
- [Deployment (GitHub Pages)](https://www.getzola.org/documentation/deployment/github-pages/)


## Content

Zola uses the `content` directory structure, consisting of `pages` and `sections`, to determine the site structure.

Assets, such as images, spreadsheets, or code can be colocated with a corresponding page by using a dedicated `page` directory (`page-dir/`) and accessed directly through its Markdown (`page-dir/index.md`). Assets may also be hidden and not copied to the target site directory (`public/`) by specifying glob patterns in the `ignored_content` setting in the config file.

### Sections and Pages

Example content structure with sections and pages:

```
content/
├── .                      <-- section (`content` root directory)
├── blog/                  <-- section (has _index.md file)
│   ├── 2023/
│   │   ├── article-1/     <-- page (has ./index.md file)
│   │   │   ├── cover.jpg
│   │   │   └── index.md
│   │   └── article-2.md   <-- page (has .md file)
│   ├── 2024/
│   │   ├── article-3.md   <-- page (has .md file)
│   │   └── article-4.md   <-- page (has .md file)
│   └── _index.md
├── _index.md
└── about.md               <-- page (has .md file)
```

Section front matter (defaults), available to templates as `section.content`:

```toml
title = ""
description = ""
draft = false
sort_by = "none"  # ("date", "update_date", "title", "title_bytes", "weight", "slug", "none")
weight = 0
template = "section.html"  # "<filename>.html"
page_template =    # "<filename>.html"
paginate_by = 0
paginate_path = "page"  # "<path>/<to>/<paginate>/<dir>"
paginate_reversed = false
insert_anchor_links = "none"  # ("left", "right", "heading", "none")
in_search_index = true
render = true
redirect_to =    # "<path>/<to>/<some>/<content>"
transparent = false
aliases = []
generate_feed = false

# Your own data.
[extra]
```
**Note**: Section configuration options apply only to the direct pages, not to any subsections' pages.

Page front matter (defaults), available to templates as `page`:

```toml
title = ""
description = ""
date =    # format: YYYY-MM-DD (2012-10-02) or RFC3339 (2002-10-02T15:00:00Z) without quotes
updated =    # format: YYYY-MM-DD (2012-10-02) or RFC3339 (2002-10-02T15:00:00Z) without quotes
weight = 0
draft = false
slug = ""
path = ""  # "<path>/<to>/<some>/<content>"
aliases = []
authors = []
in_search_index = true
template = "page.html"

# The taxonomies for this page. The keys need to be the same as the taxonomy
# names configured in `config.toml` and the values are an array of String objects. For example,
# tags = ["rust", "web"].
[taxonomies]

# Your own data.
[extra]
```

### Sass

Zola allows Sass compilation. [Sass](https://sass-lang.com/guide/) is a popular CSS[^1] preprocessor that adds special features (e.g., variables, nested rules) to facilitate the maintenance of large sets of CSS rules.

Guide:
- [Preprocessing](https://sass-lang.com/guide/#preprocessing)
- [Variables](https://sass-lang.com/guide/#variables)
- [Nesting](https://sass-lang.com/guide/#nesting)
- [Partials](https://sass-lang.com/guide/#partials)
- [Modules](https://sass-lang.com/guide/#modules)
- [Mixins](https://sass-lang.com/guide/#mixins)
- [Inheritance](https://sass-lang.com/guide/#inheritance)
- [Operators](https://sass-lang.com/guide/#operators)

[^1]: CSS Reference: [https://developer.mozilla.org/en-US/docs/Web/CSS/Reference](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference)

### Shortcodes

Zola shortcodes are template snippets defined in the `templates/shortcodes` for creating macros that can be used to a.) inject more complex HTML and b.) ease repetitive data-based tasks in `content/` (.md) files. Shortcodes may also inject Markdown but are limited as they are processed before the pages' Markdown is parsed.

Tera macros function similarly to shortcodes but are used to expand and operate on text in `template/` (.html) files.

### Internal links & Table of Contents

### Taxonomies

### Other


## Templates

A few variables are available on all templates (except feeds and the sitemap):
- `config`: the language aware configuration
- `current_path`: the relative path (full URL without `base_url`) of the current page
- `current_url`: the full URL for the current page
- `lang`: the language for the current page

Tip: If you are not sure what variables are available in a template, you can place `{{ __tera_context }}` in the template to print the whole context.

By default, Zola will look for three templates:
  - `index.html` -- applied to the site homepage
  - `section.html` -- applied to all sections
  - `page.html` -- applied to all pages

Custom templates (.html) may also be created in the `templates/` directory and applied to a page (.md) by adding `template = "<my_template>.html"` to a page's front matter.

### Tera

Zola uses the [Tera](https://github.com/Keats/tera) templating engine, whose documentation can be found [here](https://keats.github.io/tera/docs/#templates).

Docs:
  - [Introduction](https://keats.github.io/tera/docs/#introduction)
    - {{ and }} for expressions
    - {% and %} for statements
      - {%- to trim whitespace before a statement and -%} to trim whitespace after
    - {# and #} for comments
  - [Data structures](https://keats.github.io/tera/docs/#data-structures)
  - [Manipulating data](https://keats.github.io/tera/docs/#manipulating-data)
  - [Control structures](https://keats.github.io/tera/docs/#control-structures)
  - [Inheritance](https://keats.github.io/tera/docs/#inheritance)
  - [Built-ins](https://keats.github.io/tera/docs/#built-ins)

Zola adds a few [filters](https://www.getzola.org/documentation/templates/overview/#built-in-filters) and [functions](https://www.getzola.org/documentation/templates/overview/#built-in-functions) in addition to Tera's built-ins.

#### TLDR; Modularity in Zola

Use inheritance (Tera `extends`) to replicate parent templates while tailoring their labeled `block` contents

Use partials (Tera `include`) to bring in self-contained components of HTML

Use macros (Tera `macro`) to add callable units of code that return argument-dependent pieces of HTML

Use shortcodes (`templates/shortcodes`) to inline HTML and add macro-like functionality in `content/` (Markdown) files

**Note**: Mixing usage of `extends` and `include` is generally not recommended as Tera's current parser does not embed `include`d templates in a shared AST but rather treats them as separate nodes[^2]. This means that `include`'s functionality is a lot less refined until the interop with `extends` is fully supported. Currently, you cannot include templates that inherit others nor overwrite `blocks` defined in an included template's namespace.

[^2]: Author's [comment](https://github.com/Keats/tera/issues/454#issuecomment-546947190). Other relevant issues: [#664](https://github.com/Keats/tera/issues/664), [#592](https://github.com/Keats/tera/issues/592).

### Sections and Pages

Page variables:

```ts
content: String;
title: String?;
description: String?;
date: String?;
updated: String?;
slug: String;
path: String;
authors: Array<String>;
draft: Bool;
components: Array<String>;
permalink: String;
summary: String?;
taxonomies: HashMap<String, Array<String>>;
extra: HashMap<String, Any>;
toc: Array<Header>,
word_count: Number;
reading_time: Number;
lower: Page?;
higher: Page?;
year: Number?;
month: Number?;
day: Number?;
assets: Array<String>;
ancestors: Array<String>;
relative_path: String;
colocated_path: String?;
lang: String;
translations: Array<TranslatedContent>;
backlinks: Array<{permalink: String, title: String?}>;
```

Section variables:

```ts
content: String;
title: String?;
description: String?;
path: String;
components: Array<String>;
permalink: String;
extra: HashMap<String, Any>;
pages: Array<Page>;
subsections: Array<String>;
toc: Array<Header>,
word_count: Number;
reading_time: Number;
assets: Array<String>;
ancestors: Array<String>;
relative_path: String;
lang: String;
translations: Array<TranslatedContent>;
backlinks: Array<{permalink: String, title: String?}>;
generate_feed: bool;
```

`Header`:
```ts
level: 1 | 2 | 3 | 4 | 5 | 6;
id: String;
title: String;
permalink: String;
children: Array<Header>;
```

`TranslatedContent`:
```ts
lang: String?;
title: String?;
permalink: String;
path: String;
```

### Pagination and Taxonomies

### Feeds, Sitemap, and More
