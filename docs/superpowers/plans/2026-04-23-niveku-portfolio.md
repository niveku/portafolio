# Niveku Portfolio Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and deploy a static Astro portfolio site at `niveku.vercel.app` with an About page, Projects section (6 real projects), Blog, and Contact page — all sourced from curated MDX content files.

**Architecture:** Astro 5 in static output mode. Content Collections with typed frontmatter schemas for projects and blog posts. No JavaScript framework, no backend, no CMS. Content lives in `src/content/` and is authored as MDX files — the Obsidian vault serves as drafting space only.

**Tech Stack:** Astro 5, Tailwind CSS v4 (via `@tailwindcss/vite`), MDX, Vercel (free tier)

> **Note:** Visual design (colors, typography, spacing) is deferred to Claude Design. The Tailwind classes written here are scaffold-only — functional but unstyled.

> **GitHub username:** Replace `YOUR_GITHUB_USERNAME` in Task 16 with your actual GitHub username. Email for contact page: `kevin.henao@gmail.com`.

---

## File Map

Files created or modified in this plan:

```
D:/Personal/Repo/Portafolio/
├── package.json                          [created by scaffold]
├── tsconfig.json                         [created by scaffold]
├── astro.config.mjs                      [created, then modified for Tailwind + MDX]
├── src/
│   ├── content.config.ts                 [Task 3 — Content Collections schema]
│   ├── styles/
│   │   └── global.css                    [Task 2 — Tailwind import]
│   ├── layouts/
│   │   └── BaseLayout.astro              [Task 4 — HTML shell, nav, footer]
│   ├── components/
│   │   ├── ProjectCard.astro             [Task 5 — project card component]
│   │   └── PostCard.astro               [Task 5 — blog post card component]
│   ├── pages/
│   │   ├── index.astro                   [Task 6 — Home page]
│   │   ├── about.astro                   [Task 7 — About page]
│   │   ├── contact.astro                 [Task 8 — Contact page]
│   │   ├── projects/
│   │   │   ├── index.astro               [Task 9 — Projects listing + filter]
│   │   │   └── [slug].astro              [Task 10 — Project detail page]
│   │   └── blog/
│   │       ├── index.astro               [Task 11 — Blog listing]
│   │       └── [slug].astro              [Task 12 — Blog post page]
│   └── content/
│       ├── projects/
│       │   ├── abinbev-invoice-automation.md    [Task 13]
│       │   ├── casaideas-sap-pipeline.md        [Task 13]
│       │   ├── cocacola-distribution.md         [Task 13]
│       │   ├── dimar-covid-geospatial.md        [Task 13]
│       │   ├── fura-reporting-maps.md           [Task 13]
│       │   └── openclaw.md                      [Task 13]
│       └── blog/
│           └── ai-workflows-claude-code.md      [Task 14]
└── docs/                                 [already exists]
```

---

## Task 1: Scaffold Astro project

**Files:**
- Create: `package.json`, `astro.config.mjs`, `tsconfig.json`, `src/pages/index.astro`, `src/env.d.ts`, `public/favicon.svg`

- [ ] **Step 1: Run the Astro scaffolder in the existing repo directory**

Open a terminal at `D:\Personal\Repo\Portafolio` and run:

```bash
npm create astro@latest . -- --template minimal --typescript strict --install --no-git --yes
```

The `.` targets the current directory. `--yes` skips all interactive prompts. If it asks about an existing directory, confirm yes. This will NOT overwrite `docs/` or `.git/`.

Expected output ends with: `✔ Project initialized!`

- [ ] **Step 2: Verify the scaffold created key files**

```bash
ls src/pages/ && cat astro.config.mjs
```

Expected: `index.astro` exists; `astro.config.mjs` exports `defineConfig({})`.

- [ ] **Step 3: Start dev server to verify scaffold works**

```bash
npm run dev
```

Open `http://localhost:4321` in the browser. Expected: Astro default "Welcome" page renders without errors. Stop the server (`Ctrl+C`).

- [ ] **Step 4: Commit**

```bash
git add package.json package-lock.json astro.config.mjs tsconfig.json src/ public/
git commit -m "feat: scaffold Astro 5 project"
```

---

## Task 2: Add Tailwind CSS v4

**Files:**
- Modify: `astro.config.mjs`
- Create: `src/styles/global.css`

- [ ] **Step 1: Install Tailwind v4 and its Vite plugin**

```bash
npm install tailwindcss @tailwindcss/vite
```

Expected: packages installed, no errors.

- [ ] **Step 2: Wire Tailwind into Astro via the Vite plugin**

Open `astro.config.mjs` and replace its entire contents with:

```javascript
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';
import mdx from '@astrojs/mdx';

export default defineConfig({
  vite: {
    plugins: [tailwindcss()],
  },
  integrations: [mdx()],
});
```

(MDX integration is included here so both are configured in one commit.)

- [ ] **Step 3: Install the MDX integration**

```bash
npm install @astrojs/mdx
```

- [ ] **Step 4: Create the global CSS file with Tailwind import**

Create `src/styles/global.css`:

```css
@import "tailwindcss";
```

- [ ] **Step 5: Verify dev server still starts**

```bash
npm run dev
```

No errors in the terminal. Stop server.

- [ ] **Step 6: Commit**

```bash
git add astro.config.mjs src/styles/global.css package.json package-lock.json
git commit -m "feat: add Tailwind CSS v4 and MDX integration"
```

---

## Task 3: Define Content Collections schema

**Files:**
- Create: `src/content.config.ts`

- [ ] **Step 1: Create the content directories**

```bash
mkdir -p src/content/projects src/content/blog
```

- [ ] **Step 2: Create the Content Collections config**

Create `src/content.config.ts`:

```typescript
import { defineCollection, z } from 'astro:content';
import { glob } from 'astro/loaders';

const projects = defineCollection({
  loader: glob({ pattern: '**/*.{md,mdx}', base: './src/content/projects' }),
  schema: z.object({
    title: z.string(),
    summary: z.string(),
    date: z.coerce.date(),
    category: z.enum(['Geospatial', 'Data Engineering', 'AI/Automation']),
    stack: z.array(z.string()),
    featured: z.boolean().default(false),
  }),
});

const blog = defineCollection({
  loader: glob({ pattern: '**/*.{md,mdx}', base: './src/content/blog' }),
  schema: z.object({
    title: z.string(),
    summary: z.string(),
    date: z.coerce.date(),
    tags: z.array(z.string()),
  }),
});

export const collections = { projects, blog };
```

- [ ] **Step 3: Add a placeholder content file so the collection isn't empty**

Create `src/content/projects/.gitkeep` — actually, add a minimal valid file instead so Astro can generate types:

Create `src/content/projects/placeholder.md`:

```markdown
---
title: "Placeholder"
summary: "Temporary placeholder."
date: 2026-04-23
category: Geospatial
stack: []
featured: false
---

Placeholder — will be replaced in Task 13.
```

- [ ] **Step 4: Start dev server so Astro generates collection types**

```bash
npm run dev
```

Wait for `ready` message. The `.astro/types.d.ts` file is now generated. Stop server.

- [ ] **Step 5: Run type check**

```bash
npx astro check
```

Expected: `Found 0 errors` (or warnings only — warnings are fine).

- [ ] **Step 6: Commit**

```bash
git add src/content.config.ts src/content/
git commit -m "feat: define Content Collections schema for projects and blog"
```

---

## Task 4: Create BaseLayout

**Files:**
- Create: `src/layouts/BaseLayout.astro`

- [ ] **Step 1: Create the layouts directory and BaseLayout**

Create `src/layouts/BaseLayout.astro`:

```astro
---
import '../styles/global.css';

interface Props {
  title: string;
  description?: string;
}

const { title, description = 'Geoscientist turned data & GIS engineer. Building spatial workflows, AI tools, and automation systems.' } = Astro.props;
const canonicalTitle = title === 'Home' ? 'Niveku' : `${title} — Niveku`;
---

<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content={description} />
    <title>{canonicalTitle}</title>
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
  </head>
  <body>
    <header>
      <nav>
        <a href="/">Niveku</a>
        <ul>
          <li><a href="/about">About</a></li>
          <li><a href="/projects">Projects</a></li>
          <li><a href="/blog">Blog</a></li>
          <li><a href="/contact">Contact</a></li>
        </ul>
      </nav>
    </header>

    <main>
      <slot />
    </main>

    <footer>
      <p>Kevin Henao &middot; Bogotá, Colombia &middot; Remote-first</p>
    </footer>
  </body>
</html>
```

- [ ] **Step 2: Replace the scaffold index.astro to use BaseLayout**

Replace the contents of `src/pages/index.astro` with:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="Home">
  <h1>Coming soon</h1>
</BaseLayout>
```

- [ ] **Step 3: Verify in browser**

```bash
npm run dev
```

Open `http://localhost:4321`. Expected: page renders with nav showing About / Projects / Blog / Contact links, and a footer. Stop server.

- [ ] **Step 4: Commit**

```bash
git add src/layouts/BaseLayout.astro src/pages/index.astro
git commit -m "feat: add BaseLayout with nav and footer"
```

---

## Task 5: Create ProjectCard and PostCard components

**Files:**
- Create: `src/components/ProjectCard.astro`
- Create: `src/components/PostCard.astro`

- [ ] **Step 1: Create the components directory and ProjectCard**

Create `src/components/ProjectCard.astro`:

```astro
---
interface Props {
  title: string;
  summary: string;
  category: string;
  stack: string[];
  slug: string;
}

const { title, summary, category, stack, slug } = Astro.props;
---

<article data-category={category}>
  <span>{category}</span>
  <h2>
    <a href={`/projects/${slug}`}>{title}</a>
  </h2>
  <p>{summary}</p>
  <ul>
    {stack.slice(0, 4).map((tech) => (
      <li>{tech}</li>
    ))}
    {stack.length > 4 && <li>+{stack.length - 4} more</li>}
  </ul>
</article>
```

- [ ] **Step 2: Create PostCard**

Create `src/components/PostCard.astro`:

```astro
---
interface Props {
  title: string;
  summary: string;
  date: Date;
  tags: string[];
  slug: string;
}

const { title, summary, date, tags, slug } = Astro.props;
const formattedDate = date.toLocaleDateString('en-US', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
});
---

<article>
  <time datetime={date.toISOString()}>{formattedDate}</time>
  <h2>
    <a href={`/blog/${slug}`}>{title}</a>
  </h2>
  <p>{summary}</p>
  <ul>
    {tags.map((tag) => (
      <li>{tag}</li>
    ))}
  </ul>
</article>
```

- [ ] **Step 3: Run type check**

```bash
npx astro check
```

Expected: `Found 0 errors`.

- [ ] **Step 4: Commit**

```bash
git add src/components/
git commit -m "feat: add ProjectCard and PostCard components"
```

---

## Task 6: Create Home page

**Files:**
- Modify: `src/pages/index.astro`

- [ ] **Step 1: Write the Home page**

Replace `src/pages/index.astro` with:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import ProjectCard from '../components/ProjectCard.astro';
import PostCard from '../components/PostCard.astro';
import { getCollection } from 'astro:content';

const allProjects = await getCollection('projects');
const featuredProjects = allProjects
  .filter((p) => p.data.featured)
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf())
  .slice(0, 3);

const allPosts = await getCollection('blog');
const latestPosts = allPosts
  .sort((a, b) => b.data.date.valueOf() - a.data.date.valueOf())
  .slice(0, 3);
---

<BaseLayout
  title="Home"
  description="Geoscientist turned data & GIS engineer. Building spatial workflows, AI tools, and automation systems."
>
  <section>
    <h1>Kevin Henao</h1>
    <p>
      Geoscientist turned data &amp; GIS engineer. Building spatial workflows,
      AI tools, and automation systems from Bogotá, Colombia — remote-first.
    </p>
    <nav aria-label="Primary actions">
      <a href="/about">About me</a>
      <a href="/projects">See all projects</a>
    </nav>
  </section>

  {featuredProjects.length > 0 && (
    <section>
      <h2>Featured Projects</h2>
      <div>
        {featuredProjects.map((project) => (
          <ProjectCard
            title={project.data.title}
            summary={project.data.summary}
            category={project.data.category}
            stack={project.data.stack}
            slug={project.id}
          />
        ))}
      </div>
      <a href="/projects">All projects →</a>
    </section>
  )}

  {latestPosts.length > 0 && (
    <section>
      <h2>Latest Posts</h2>
      <div>
        {latestPosts.map((post) => (
          <PostCard
            title={post.data.title}
            summary={post.data.summary}
            date={post.data.date}
            tags={post.data.tags}
            slug={post.id}
          />
        ))}
      </div>
      <a href="/blog">All posts →</a>
    </section>
  )}
</BaseLayout>
```

- [ ] **Step 2: Verify in browser**

```bash
npm run dev
```

Open `http://localhost:4321`. Expected: hero section renders. Featured projects and posts sections are hidden (no content yet — that's correct). Stop server.

- [ ] **Step 3: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: build Home page with hero and content previews"
```

---

## Task 7: Create About page

**Files:**
- Create: `src/pages/about.astro`

- [ ] **Step 1: Create the About page**

Create `src/pages/about.astro`:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout
  title="About"
  description="Kevin Henao — geoscientist turned data & GIS engineer based in Bogotá, Colombia."
>
  <article>
    <h1>About</h1>

    <section>
      <h2>Who I am</h2>
      <p>
        I started my career as a geoscientist — studying rock, stratigraphy, and
        the earth at Universidad de los Andes in Bogotá. The technical side of
        that work pulled me toward computation: spatial data, Python for
        geochemical analysis, GIS for operational workflows. Over 8+ years I
        evolved from geological reporting and map production into data
        engineering, cloud infrastructure, and now AI-assisted workflow
        automation.
      </p>
      <p>
        The thread connecting all of it is translating domain-heavy, ambiguous
        problems into working technical systems — whether that's a serverless
        invoice-processing pipeline on AWS, a geospatial monitoring dashboard
        for a government agency, or an AI agent workflow built on Claude Code
        and MCPs.
      </p>
      <p>
        I'm a co-author of <em>"Automating Core Data Integration"</em>, presented
        at ADIPEC 2025 (SPE D041S138R004), applying machine learning to
        geoscience data workflows.
      </p>
    </section>

    <section>
      <h2>What I work with</h2>

      <h3>Strong / production-proven</h3>
      <ul>
        <li>Python — data engineering, automation, geospatial analysis</li>
        <li>SQL — data modeling, analysis, workflow queries</li>
        <li>QGIS — spatial analysis, cartography, operational GIS workflows</li>
        <li>GIS / geospatial data — across multiple stacks and formats</li>
        <li>AWS — Lambda, Glue, Step Functions, S3, Athena, Redshift, DynamoDB, SQS, Cognito, IAM, CDK</li>
        <li>GeoPandas, pandas, scikit-learn</li>
        <li>ETL and data processing pipelines</li>
        <li>Jira, Confluence — intensive daily use; process design at team scale</li>
      </ul>

      <h3>Credible with careful framing</h3>
      <ul>
        <li>Docker, GitHub Actions, PySpark</li>
        <li>Power BI, DAX</li>
        <li>ArcGIS ecosystem (ArcGIS Online, Survey123, ArcGIS Pro)</li>
        <li>GDAL, Leapfrog, Datamine</li>
      </ul>

      <h3>Growing</h3>
      <ul>
        <li>Claude Code, MCPs, AI agent workflows</li>
        <li>OpenClaw — personal AI/automation system</li>
      </ul>
    </section>

    <section>
      <h2>Context</h2>
      <p>
        Based in Bogotá, Colombia. Remote-first since the pandemic — I've
        collaborated across time zones with technical and business stakeholders
        throughout my career. English at full professional proficiency (C2,
        certified via EF SET). Spanish native.
      </p>
      <p>
        BSc Geosciences, Universidad de los Andes (GPA 4.12/5, "Quiero
        Estudiar" scholarship). Colombian Professional Geology License (CPG),
        2017–present.
      </p>
    </section>
  </article>
</BaseLayout>
```

- [ ] **Step 2: Verify in browser**

```bash
npm run dev
```

Open `http://localhost:4321/about`. Expected: three sections render — narrative, stack, context. Stop server.

- [ ] **Step 3: Commit**

```bash
git add src/pages/about.astro
git commit -m "feat: build About page from Master Profile and Career Narrative"
```

---

## Task 8: Create Contact page

**Files:**
- Create: `src/pages/contact.astro`

- [ ] **Step 1: Create the Contact page**

Create `src/pages/contact.astro`:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="Contact" description="Get in touch with Kevin Henao.">
  <article>
    <h1>Contact</h1>
    <p>
      The best way to reach me is by email. I'm also active on LinkedIn and
      GitHub.
    </p>
    <ul>
      <li>
        Email: <a href="mailto:kevin.henao@gmail.com">kevin.henao@gmail.com</a>
      </li>
      <li>
        LinkedIn:
        <a
          href="https://www.linkedin.com/in/kevinjhenao/"
          target="_blank"
          rel="noopener noreferrer"
        >
          linkedin.com/in/kevinjhenao
        </a>
      </li>
      <li>
        GitHub:
        <a
          href="https://github.com/niveku"
          target="_blank"
          rel="noopener noreferrer"
        >
          github.com/niveku
        </a>
      </li>
    </ul>
    <p>
      I'm open to fully remote roles and select freelance projects. Response
      time is usually within 48 hours.
    </p>
  </article>
</BaseLayout>
```

> **Before committing:** verify the LinkedIn URL (`linkedin.com/in/kevinjhenao`) and GitHub URL (`github.com/niveku`) are correct — update them if not.

- [ ] **Step 2: Verify in browser**

```bash
npm run dev
```

Open `http://localhost:4321/contact`. Expected: links render correctly. Stop server.

- [ ] **Step 3: Commit**

```bash
git add src/pages/contact.astro
git commit -m "feat: build Contact page with email, LinkedIn, and GitHub links"
```

---

## Task 9: Create Projects index page

**Files:**
- Create: `src/pages/projects/index.astro`

- [ ] **Step 1: Create the projects directory and index page**

Create `src/pages/projects/index.astro`:

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import ProjectCard from '../../components/ProjectCard.astro';
import { getCollection } from 'astro:content';

const allProjects = await getCollection('projects');
const sorted = allProjects.sort(
  (a, b) => b.data.date.valueOf() - a.data.date.valueOf()
);

const categories = ['All', 'Geospatial', 'Data Engineering', 'AI/Automation'] as const;
---

<BaseLayout title="Projects" description="Geospatial, data engineering, and AI/automation projects by Kevin Henao.">
  <section>
    <h1>Projects</h1>
    <p>
      A selection of professional and personal projects across geospatial data,
      data engineering, and AI-assisted automation.
    </p>

    <nav aria-label="Filter by category">
      {categories.map((cat) => (
        <button data-filter={cat === 'All' ? '' : cat}>
          {cat}
        </button>
      ))}
    </nav>

    <div id="projects-grid">
      {sorted.map((project) => (
        <ProjectCard
          title={project.data.title}
          summary={project.data.summary}
          category={project.data.category}
          stack={project.data.stack}
          slug={project.id}
        />
      ))}
    </div>
  </section>

  <script>
    const buttons = document.querySelectorAll<HTMLButtonElement>('[data-filter]');
    const cards = document.querySelectorAll<HTMLElement>('[data-category]');

    buttons.forEach((btn) => {
      btn.addEventListener('click', () => {
        const filter = btn.dataset.filter ?? '';
        cards.forEach((card) => {
          const visible = filter === '' || card.dataset.category === filter;
          card.style.display = visible ? '' : 'none';
        });
      });
    });
  </script>
</BaseLayout>
```

- [ ] **Step 2: Verify in browser**

```bash
npm run dev
```

Open `http://localhost:4321/projects`. Expected: heading and filter buttons render. No project cards yet (placeholder is filtered out later). Stop server.

- [ ] **Step 3: Commit**

```bash
git add src/pages/projects/
git commit -m "feat: build Projects index page with category filter"
```

---

## Task 10: Create Project detail page

**Files:**
- Create: `src/pages/projects/[slug].astro`

- [ ] **Step 1: Create the dynamic project detail route**

Create `src/pages/projects/[slug].astro`:

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import { getCollection, render } from 'astro:content';

export async function getStaticPaths() {
  const projects = await getCollection('projects');
  return projects.map((project) => ({
    params: { slug: project.id },
    props: { project },
  }));
}

const { project } = Astro.props;
const { Content } = await render(project);

const formattedDate = project.data.date.toLocaleDateString('en-US', {
  year: 'numeric',
  month: 'long',
});
---

<BaseLayout title={project.data.title} description={project.data.summary}>
  <article>
    <header>
      <span>{project.data.category}</span>
      <h1>{project.data.title}</h1>
      <p>{project.data.summary}</p>
      <time datetime={project.data.date.toISOString()}>{formattedDate}</time>
      <ul aria-label="Tech stack">
        {project.data.stack.map((tech) => (
          <li>{tech}</li>
        ))}
      </ul>
    </header>

    <Content />

    <footer>
      <a href="/projects">← All projects</a>
    </footer>
  </article>
</BaseLayout>
```

- [ ] **Step 2: Verify in browser**

```bash
npm run dev
```

Open `http://localhost:4321/projects/placeholder`. Expected: page renders with "Placeholder" title and empty body. Stop server.

- [ ] **Step 3: Commit**

```bash
git add src/pages/projects/[slug].astro
git commit -m "feat: build Project detail page"
```

---

## Task 11: Create Blog index page

**Files:**
- Create: `src/pages/blog/index.astro`

- [ ] **Step 1: Create the blog directory and index page**

Create `src/pages/blog/index.astro`:

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import PostCard from '../../components/PostCard.astro';
import { getCollection } from 'astro:content';

const allPosts = await getCollection('blog');
const sorted = allPosts.sort(
  (a, b) => b.data.date.valueOf() - a.data.date.valueOf()
);
---

<BaseLayout
  title="Blog"
  description="Technical writing on geospatial data engineering, AI workflows, and automation by Kevin Henao."
>
  <section>
    <h1>Blog</h1>
    <p>
      Technical notes on geospatial data, data engineering, and AI-assisted
      workflow automation.
    </p>

    {sorted.length === 0 && (
      <p>Posts coming soon.</p>
    )}

    <div>
      {sorted.map((post) => (
        <PostCard
          title={post.data.title}
          summary={post.data.summary}
          date={post.data.date}
          tags={post.data.tags}
          slug={post.id}
        />
      ))}
    </div>
  </section>
</BaseLayout>
```

- [ ] **Step 2: Verify in browser**

```bash
npm run dev
```

Open `http://localhost:4321/blog`. Expected: heading, description, and "Posts coming soon." render. Stop server.

- [ ] **Step 3: Commit**

```bash
git add src/pages/blog/
git commit -m "feat: build Blog index page"
```

---

## Task 12: Create Blog post page

**Files:**
- Create: `src/pages/blog/[slug].astro`

- [ ] **Step 1: Create the dynamic blog post route**

Create `src/pages/blog/[slug].astro`:

```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import { getCollection, render } from 'astro:content';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts.map((post) => ({
    params: { slug: post.id },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await render(post);

const formattedDate = post.data.date.toLocaleDateString('en-US', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
});
---

<BaseLayout title={post.data.title} description={post.data.summary}>
  <article>
    <header>
      <time datetime={post.data.date.toISOString()}>{formattedDate}</time>
      <h1>{post.data.title}</h1>
      <p>{post.data.summary}</p>
      <ul aria-label="Tags">
        {post.data.tags.map((tag) => (
          <li>{tag}</li>
        ))}
      </ul>
    </header>

    <Content />

    <footer>
      <a href="/blog">← All posts</a>
    </footer>
  </article>
</BaseLayout>
```

- [ ] **Step 2: Commit**

```bash
git add src/pages/blog/[slug].astro
git commit -m "feat: build Blog post page"
```

---

## Task 13: Add all six project content files

**Files:**
- Delete: `src/content/projects/placeholder.md`
- Create: `src/content/projects/abinbev-invoice-automation.md`
- Create: `src/content/projects/casaideas-sap-pipeline.md`
- Create: `src/content/projects/cocacola-distribution.md`
- Create: `src/content/projects/dimar-covid-geospatial.md`
- Create: `src/content/projects/fura-reporting-maps.md`
- Create: `src/content/projects/openclaw.md`

- [ ] **Step 1: Delete the placeholder file**

```bash
rm src/content/projects/placeholder.md
```

- [ ] **Step 2: Create AB InBev Invoice Automation**

Create `src/content/projects/abinbev-invoice-automation.md`:

```markdown
---
title: "AB InBev Invoice PDF Automation"
summary: "Acting architect on a serverless pipeline that normalized invoice PDFs from 30+ supplier formats into structured data for downstream business workflows."
date: 2023-06-01
category: Data Engineering
stack: ["AWS Lambda", "AWS Step Functions", "AWS Textract", "AWS SQS", "AWS DynamoDB", "AWS S3", "AWS CloudWatch", "Python"]
featured: true
---

## Problem

AB InBev received invoices from 30+ suppliers in highly variable PDF formats — different layouts, numeric conventions, partially handwritten fields, and inconsistent structure. Downstream business systems needed clean, normalized data. There was no automated path from raw PDFs to structured records.

## My Role

I was the implementation lead and acting architect in practice. I took the initial requirement — "extract and normalize invoice data" — and drove it from requirements clarification through architecture decisions, PDF readability improvements, cost-aware design, and testing-oriented delivery.

## What I Built

A fully serverless pipeline on AWS:

- **Ingest:** PDFs land in S3, triggering a Lambda function.
- **Extraction:** AWS Textract handles OCR and structured field extraction.
- **Orchestration:** Step Functions coordinate the extraction, validation, and retry flow.
- **Queue:** SQS decouples ingestion from downstream processing for resilience.
- **Storage:** DynamoDB holds normalized records; S3 retains originals.
- **Observability:** CloudWatch alarms and logs for operational visibility.

I also ran a benchmark phase to quantify extraction accuracy across provider types before committing to the architecture, and designed preprocessing steps to improve PDF readability for challenging formats.

## Outcome

The pipeline handled invoices from roughly 30–40 supplier types. The architecture was cost-aware (Textract is priced per page — batch sizing and preprocessing reduced unnecessary spend). Ambiguous formats were flagged for human review rather than silently dropped.
```

- [ ] **Step 3: Create Casaideas SAP Pipeline**

Create `src/content/projects/casaideas-sap-pipeline.md`:

```markdown
---
title: "Casaideas SAP-to-Cloud Pipeline"
summary: "Built key stages of an end-to-end data pipeline migrating daily SAP extracts into a cloud analytics layer for a major Latin American retail chain."
date: 2023-01-01
category: Data Engineering
stack: ["AWS S3", "AWS Glue", "PySpark", "SQL", "Python"]
featured: false
---

## Problem

Casaideas, a large Latin American home goods retailer, needed to migrate operational data from their SAP system into a cloud-based analytics layer. Daily SAP extracts needed to be ingested, cleaned, and made available in a consistent, traceable form for downstream reporting and business intelligence.

## My Role

Technical contributor responsible for key stages of the extraction-to-final-data flow. I implemented data processing steps in PySpark, worked on traceability mechanisms, and handled schema validation and consistency checks across the pipeline.

## What I Built

- Extraction layer: SAP daily files land in S3 and are catalogued.
- Processing: AWS Glue jobs with PySpark transform raw extracts — cleaning, type normalization, deduplication, and business-rule application.
- Traceability: lineage tracking so every downstream record can be traced back to its source extract and transformation step.
- Output: final tables in a queryable format for analytics consumption.

## Outcome

A reliable daily pipeline delivering clean, traceable inventory and operational data to the analytics layer. The traceability design was deliberate — retail data (especially inventory and stock) needs auditability when business users question numbers.
```

- [ ] **Step 4: Create Coca-Cola Distribution**

Create `src/content/projects/cocacola-distribution.md`:

```markdown
---
title: "Coca-Cola Chile Distribution Data Flow"
summary: "Data engineering contributor on a large-scale logistics and distribution data pipeline for Coca-Cola Chile, with direct client interaction and quality-gated delivery."
date: 2023-09-01
category: Data Engineering
stack: ["AWS", "Python", "SQL", "PySpark", "GitHub Actions", "SonarQube"]
featured: false
---

## Problem

Coca-Cola Chile's distribution and logistics operation generated data across production, transport, stock, losses, and damage — at operational scale. The data needed to flow reliably into analytical systems, with strong quality guarantees before any output reached client-facing dashboards.

## My Role

Technical contributor with direct client interaction. I handled ETL development, data modeling, traceability work, requirement refinement, and issue resolution. I also participated in quality-gate workflows — coverage checks, Sonar analysis, and benchmark comparisons before delivery.

## What I Built

- ETL flows processing distribution and logistics data across multiple operational dimensions.
- Data modeling for stock, transport, and loss tracking.
- Traceability mechanisms linking outputs back to source data.
- Quality gates: unit test coverage, SonarQube analysis, and benchmark validation runs before each delivery.
- Client communication: translating ambiguous business requirements into implementation specs and reporting back on tradeoffs.

## Outcome

A delivery model that combined technical rigor (quality gates, coverage, Sonar) with stakeholder transparency. Working directly with the client required handling ambiguity in real time — converting verbal requirements into actionable specs on short cycles.
```

- [ ] **Step 5: Create DIMAR COVID Geospatial**

Create `src/content/projects/dimar-covid-geospatial.md`:

```markdown
---
title: "DIMAR COVID Geospatial Monitoring"
summary: "Built a geospatial COVID monitoring workflow for Colombia's national maritime authority using ArcGIS Online and Python, supporting institutional decision-making during the pandemic."
date: 2020-06-01
category: Geospatial
stack: ["ArcGIS Online", "Python", "QGIS", "Survey123"]
featured: false
---

## Problem

During the COVID-19 pandemic, DIMAR (Colombia's Dirección General Marítima) needed to monitor the geographic spread and operational impact of the pandemic across its maritime jurisdiction — ports, coastal zones, and naval installations. There was no existing spatial monitoring infrastructure for this use case.

## My Role

I designed and implemented the geospatial monitoring workflow end-to-end: data sourcing, spatial processing, and delivery via ArcGIS Online dashboards accessible to decision-makers across the institution.

## What I Built

- Data collection: structured intake via Survey123 for field reporting from maritime installations.
- Spatial processing: Python scripts to clean, aggregate, and georeference incident and operational data.
- Visualization: ArcGIS Online dashboards showing geographic distribution, trends, and operational status across DIMAR's jurisdiction.
- Update cadence: near-daily refresh cycle during peak pandemic monitoring period.

## Outcome

A functional institutional monitoring tool used by DIMAR leadership during the pandemic response. The project demonstrated that lightweight GIS infrastructure (Survey123 + ArcGIS Online + Python) could be deployed quickly for institutional decision support without heavy infrastructure investment.
```

- [ ] **Step 6: Create Fura Reporting Maps**

Create `src/content/projects/fura-reporting-maps.md`:

```markdown
---
title: "Fura Exploration — Geological Reporting & Licensing Maps"
summary: "Owned geospatial information production and cartographic reporting for a Colombian emerald mining company, delivering maps for investor reports, environmental licensing, and regulatory submissions."
date: 2019-01-01
category: Geospatial
stack: ["QGIS", "GIS", "Python", "Leapfrog", "Datamine"]
featured: false
---

## Problem

Fura Gems, a Colombian emerald exploration and mining company, needed reliable, audience-calibrated maps and geospatial information for multiple formal reporting contexts: investor-facing technical reports, environmental licensing submissions, and regulatory compliance documentation. Each audience required different spatial content, projection standards, and cartographic conventions.

## My Role

I owned map production and geospatial information management for the reporting function. This was not a support role — I was the single point of accountability for geospatial deliverables across all reporting contexts.

## What I Built

- A managed geospatial data repository covering geological, environmental, topographic, and licensing layers for Fura's concession areas.
- Cartographic production workflows in QGIS: different map series for each audience type, with appropriate projections, symbology, and annotation.
- Integration with geological modeling outputs from Leapfrog for 3D-to-2D representation in reports.
- Licensing and concession boundary maps for formal submissions to Colombian regulatory bodies.
- Investor-facing map series for technical reports circulated to institutional stakeholders.

## Outcome

Maps delivered on time for every reporting cycle across investor relations, environmental licensing, and regulatory submissions. The work also contributed to Fura's first formal Colombian emerald resource estimation — a technically rigorous document I co-authored applying Colombian mining reporting standards.
```

- [ ] **Step 7: Create OpenClaw**

Create `src/content/projects/openclaw.md`:

```markdown
---
title: "OpenClaw — Personal AI Automation System"
summary: "A personal AI workflow system built on Claude Code, MCPs, and custom skills — designed to operate across development, career management, and knowledge work at significantly higher throughput."
date: 2026-01-01
category: AI/Automation
stack: ["Claude Code", "MCPs", "Obsidian", "Python", "Windows"]
featured: true
---

## Problem

Modern AI tools are powerful individually but fragmented — each tool requires its own context, workflow, and mental switching cost. I wanted a unified operating model where AI assistance is persistent, contextual, and integrated into real workflows rather than a separate tool I context-switch into.

## What I Built

OpenClaw is a personal system built on top of Claude Code's plugin and skill architecture:

- **Skills:** Custom reusable instructions for recurring workflows — career OS operations, code review patterns, content production, session management.
- **MCPs (Model Context Protocol servers):** Integrations that give the AI direct access to my Obsidian vault, calendar, and file system — enabling it to reason across my actual context rather than isolated prompts.
- **Workflow automation:** Recurring operations (CV tailoring, interview prep, project documentation, research synthesis) are expressed as skills that invoke the right tools in the right sequence.
- **Memory system:** Persistent file-based memory across sessions so context accumulates rather than resetting.

## Operating Model

I use OpenClaw as a daily operating system for technical work: drafting and maintaining career materials, exploring codebases, designing and implementing features, synthesizing research, and managing the Obsidian vault that backs this portfolio.

## Outcome

A measurably faster personal workflow across development, writing, and knowledge management. The system is under continuous iteration — each improvement compounds across all the workflows that depend on it.
```

- [ ] **Step 8: Verify all projects build correctly**

```bash
npm run dev
```

Open `http://localhost:4321/projects`. Expected: all 5 project cards render (OpenClaw and AB InBev are featured). Open one detail page to verify content renders. Stop server.

- [ ] **Step 9: Run build check**

```bash
npm run build
```

Expected: build completes with no errors. 8 static pages generated (home, about, projects index, 6 project detail pages, blog index, contact).

- [ ] **Step 10: Commit**

```bash
git add src/content/projects/
git commit -m "feat: add six project content files"
```

---

## Task 14: Add initial blog post

**Files:**
- Create: `src/content/blog/ai-workflows-claude-code.md`

- [ ] **Step 1: Create the first blog post**

Create `src/content/blog/ai-workflows-claude-code.md`:

```markdown
---
title: "Building AI Workflows with Claude Code and MCPs"
summary: "How I use Claude Code, custom skills, and Model Context Protocol servers to operate across development, writing, and knowledge management — and what the experience reveals about how AI tooling actually compounds."
date: 2026-04-23
tags: ["AI", "claude-code", "automation", "workflow", "MCP"]
---

Most AI tools are used as standalone assistants: you open a chat, ask a question, get an answer, and move on. Each session starts cold. Context resets. The AI knows nothing about your project, your codebase, your decisions from yesterday.

Claude Code changes this — but only if you build around it intentionally.

## What Claude Code actually is

Claude Code is a CLI for Claude that runs in your terminal with direct access to your file system, shell, and tools. Unlike a chat interface, it can read your code, run commands, write files, and chain operations across a session. It's agentic — it takes actions, not just generates text.

The interesting layer on top of this is the plugin and skill system. Skills are reusable instruction sets stored as Markdown files. MCPs (Model Context Protocol servers) are integrations that give the AI direct access to external systems — your Obsidian vault, your calendar, a database, an API.

## The compounding effect

A skill is written once and invoked many times. Once I have a skill for "write a cover letter from this job description using my career materials," I don't have to re-explain the task, the format, or the context. The skill carries it.

MCPs add persistent context. When Claude Code can read my Obsidian vault directly, it isn't working from copy-pasted notes — it's reading the actual source of truth. When I ask it to check my career narrative against a new role, it pulls the current version, not a version I remembered to paste.

The result is that each improvement to the system compounds. A better career OS in Obsidian means better AI-assisted career work. A better skill for code review means faster, more consistent reviews across every future project.

## What breaks

The weak point is context accumulation over long sessions. Long conversations fill the context window and degrade performance. Claude Code has compaction built in, but the discipline of keeping sessions focused — one task, one context — still matters.

Skills also require maintenance. An instruction that worked for one version of a workflow may need updating as the workflow evolves. I treat skills the same way I treat code: they need review, they accumulate debt, and a bad skill causes consistent bad outputs.

## The practical setup

My current stack:

- **Claude Code** as the primary interface for development and writing work.
- **Custom skills** for career OS operations, code review, session management, and content production.
- **Obsidian MCP** for direct vault access — career materials, project notes, and knowledge base.
- **File-based memory** so context accumulates across sessions rather than resetting daily.

The system is called OpenClaw. It's personal infrastructure — not a product, not a framework — just a set of conventions that makes AI assistance practical for how I actually work.

## What geospatial engineers can use this for

If you work in GIS, data engineering, or scientific software:

- **Documentation automation:** generate technical documentation from code structure + domain context from your notes.
- **Data pipeline review:** describe your pipeline in natural language, have the AI identify edge cases and failure modes.
- **Spatial analysis documentation:** connect the AI to your project files and have it describe what each processing step does.
- **Career materials:** maintain a living technical portfolio that the AI can query when tailoring applications.

The tooling is early. The patterns are forming. But the compounding effect is already real — and the gap between people who build workflows around AI and people who use it ad-hoc is widening fast.
```

- [ ] **Step 2: Verify in browser**

```bash
npm run dev
```

Open `http://localhost:4321/blog`. Expected: the post card renders. Click through to `http://localhost:4321/blog/ai-workflows-claude-code` — post body renders with all sections. Stop server.

- [ ] **Step 3: Verify home page now shows the post**

Open `http://localhost:4321`. Expected: "Latest Posts" section appears with the blog post card.

- [ ] **Step 4: Run full build**

```bash
npm run build
```

Expected: build completes with no errors.

- [ ] **Step 5: Commit**

```bash
git add src/content/blog/
git commit -m "feat: add first blog post on AI workflows with Claude Code"
```

---

## Task 15: Final verification and Vercel deploy

**Files:**
- Create: `vercel.json` (optional — Astro on Vercel works zero-config, but explicit config is clearer)

- [ ] **Step 1: Run type check and build one final time**

```bash
npx astro check && npm run build
```

Expected: `Found 0 errors` then successful build.

- [ ] **Step 2: Preview the production build locally**

```bash
npm run preview
```

Open `http://localhost:4321`. Navigate through all pages:
- `/` — hero, featured projects (OpenClaw + AB InBev), latest post visible
- `/about` — three sections render
- `/projects` — all 6 project cards render; filter buttons work
- `/projects/openclaw` — detail page renders
- `/projects/abinbev-invoice-automation` — detail page renders
- `/blog` — post card renders
- `/blog/ai-workflows-claude-code` — full post renders
- `/contact` — links render

Stop server.

- [ ] **Step 3: Push to GitHub**

Create a new repository named `portafolio` (or `niveku`) on GitHub at `github.com/YOUR_GITHUB_USERNAME`, then:

```bash
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/portafolio.git
git branch -M main
git push -u origin main
```

- [ ] **Step 4: Deploy to Vercel**

1. Go to [vercel.com](https://vercel.com) and sign in with GitHub.
2. Click "Add New Project".
3. Import the `portafolio` repository.
4. Framework preset: Astro (Vercel auto-detects it).
5. No environment variables needed.
6. Click "Deploy".

Expected: build completes in ~1–2 minutes. Vercel assigns `portafolio-xxx.vercel.app` or you can rename it to `niveku.vercel.app` in project settings → Domains.

- [ ] **Step 5: Verify the live site**

Open the Vercel URL. Check all pages load. Check that the filter on `/projects` works in the deployed version.

- [ ] **Step 6: Final commit (add Vercel config if needed)**

If Vercel auto-detected everything correctly, no config file is needed. If there were build issues, create `vercel.json`:

```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "astro"
}
```

Then:

```bash
git add vercel.json
git commit -m "chore: add explicit Vercel build config"
git push
```

---

## Self-Review Notes

**Spec coverage:**
- ✅ Home with hero, featured projects, latest posts
- ✅ About with narrative, stack, context
- ✅ Projects index with category filter
- ✅ Projects detail pages (6 projects)
- ✅ Blog index and post pages
- ✅ Contact with email + GitHub + LinkedIn
- ✅ Astro static output
- ✅ Tailwind CSS v4 via `@tailwindcss/vite`
- ✅ Content Collections with typed schema
- ✅ Vercel deployment

**Deferred (per spec):**
- Visual design — handled by Claude Design separately
- Custom domain — deferred
- Analytics, CMS, contact form — out of scope

**LinkedIn URL:** Task 8 uses `linkedin.com/in/kevinjhenao` — verify this matches Kevin's actual LinkedIn handle before publishing.

**GitHub URL:** Task 8 uses `github.com/niveku` — verify before publishing.
