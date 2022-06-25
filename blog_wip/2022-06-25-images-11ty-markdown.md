---
layout: blog
title: "Working with images in markdown files and 11ty"
description: "Best practices for working with images from capturing or internet in markdown files and eleventy. This is the workflow I set on this website."
tags: [Blog, Web Dev, Static Site Generators, 11ty]
keywords: "images photos 11ty eleventy markdown md plugins typora editor copy paste"
---

## 🎯 What we want?

- Capture the screen to the clipboard and then paste it into the editor. The captured image is automatically copied to a folder and can be viewed in the editor as well as on Github and on the 11ty website (after rendering).
- The same function for online images (copy and paste the URL).

## Tools

- [Typora](https://typora.io/) (WYSWYG).
- [VSCode](https://code.visualstudio.com/) (with extensions [Markdown All in One](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one) and [Markdown Image](https://marketplace.visualstudio.com/items?itemName=hancel.markdown-image)).

## Setting up `.eleventy`




## Setting up VSCode

## Setting up Typora

## Why it works?

As you know, all rendered html files and images [have to be stored in a output folder](https://www.11ty.dev/docs/config/), let's say `dist/`. What you see on the website (in the html file) is

```html
<img alt="alt-of-image" src="../img_blog/name-of-markdown/name-of-image.png">
```

and what you see in the markdown editor is,

```markdown
![alt-of-image](../img_blog/name-of-markdown/name-of-image.png)
```

They look like the same but actually not.