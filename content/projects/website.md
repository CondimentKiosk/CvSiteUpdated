---
title: "Personal Portfolio Website"
---

## Personal Portfolio Website

## **September 2025 - Ongoing**

**Tech stack:** Hugo, Markdown, Go, HTML/CSS, Git, CLI

---

## Overview  

A fully custom personal website to act as a project portfolio and extension of my CV. Hugo static site generator was utilised, combining clean features with modern design practices like light/dark mode, project pages, and easy scalability for adding future content or adding new site pages.

---

## Goals  

- Create a professional home for my CV, portfolio, and contact links.  
- Learn Hugo’s templating system and how to customise themes.  
- Practice deployment workflows with GitHub Pages.  

---

## Development in Full Swing  

{{< figure src="/assets/covfefe.jpg" width="400px" >}}

---

## Code Snippet  

```toml
title = "Josh CV Site"
theme = "hugo-coder"
languagecode = "en"
defaultcontentlanguage = "en"

[markup.highlight]
style = "github-dark"
colorScheme = "auto"
hidecolorschemetoggle = false

# Menu links
[[menu.main]]
name = "About"
weight = 1
url = "about/"
[[menu.main]]
name = "Projects"
weight = 2
url = "projects/"

