---
title: From Clunky to Clean - Refactoring a JavaScript Image Toggle
date: 2023-11-26 01:30:11 +0800
categories: [front-end,js] 
tag: [refactoring,js]
---

A simple image toggle can teach us key JS optimization principles. Let's break down the refactoring process.

## Starting Point
Here's a basic implementation most developers might write:

```javascript
function picLink(event) {
    event.preventDefault();
    let allImages = document.querySelectorAll("img");
    let selectedImg = document.querySelector(`img[id="${this.dataset.img}"]`);

    if (selectedImg.style.display === "block") {
        selectedImg.style.display = "none";
    } else {
        allImages.forEach(img => {
            img.style.display = img.id === selectedImg.id ? "block" : "none";
        });
    }
}
```

## The Problems
- Repeated DOM queries hammer performance 
- `this` binding is fragile
- Complex selector logic
- Nested conditionals hurt readability

## Clean Solution
```javascript
const images = document.querySelectorAll("img");

const toggleImage = ({currentTarget}) => {
    const targetImage = document.getElementById(currentTarget.dataset.img);
    images.forEach(img => 
        img.style.display = img === targetImage ? 
            (img.style.display === "block" ? "none" : "block") : 
            "none"
    );
};
```

Key changes:
- Cache DOM selections
- Destructure event object
- Direct ID lookup
- Single expression toggle

This cleaner version runs faster and reads better. More importantly, it reveals core JS patterns that improve any codebase.
