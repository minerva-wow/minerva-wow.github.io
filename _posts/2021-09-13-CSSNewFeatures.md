---
title: "Modern CSS Features: Moving Beyond Preprocessors"
date: 2025-09-13 00:28:20 +0800
categories: [front-end, css]
tags: [css, new-feature]     # TAG names should always be lowercase
---

Frontend technologies continue to evolve rapidly, and CSS is no exception. Over the past few years, CSS has been working hard to eliminate the need for preprocessors and become more developer-friendly. Many features that we once relied on preprocessors or hacky workarounds to achieve are now becoming standardized. Browser compatibility is also steadily improving, with even Safari making efforts to catch up.

Let's explore some of the most significant modern CSS features that are changing how we write styles.

## CSS Native Nesting

You can now write nested CSS directly without needing Sass or Less:

```css
.card {
  padding: 20px;
  
  & .title {
    font-size: 24px;
    color: #333;
    
    &:hover {
      color: #666;
    }
  }
  
  & .content {
    margin-top: 10px;
  }
}
```

This native nesting syntax brings the organizational benefits of preprocessors directly to CSS, making stylesheets more readable and maintainable.

## CSS Conditional Features

While we don't have an `@if` function yet, CSS has gained many powerful conditional capabilities:

```css
/* Container Queries */
@container (min-width: 300px) {
  .card {
    display: grid;
    grid-template-columns: 1fr 1fr;
  }
}

/* Style Queries */
@container style(--theme: dark) {
  .button {
    background: #333;
  }
}
```

These conditional features allow for more dynamic and context-aware styling.

## Anchor Positioning

This feature makes positioning incredibly powerful and flexible:

```css
.tooltip {
  position: absolute;
  position-anchor: --my-anchor;
  top: anchor(bottom);
  left: anchor(center);
}

.button {
  anchor-name: --my-anchor;
}
```

Anchor positioning simplifies the creation of tooltips, dropdowns, and other positioned elements that need to maintain relationships with their reference elements.

## Container Queries

Apply styles based on container size rather than viewport size:

```css
.sidebar {
  container-type: inline-size;
}

@container (min-width: 250px) {
  .widget {
    display: flex;
  }
}
```

Container queries enable truly component-based responsive design, where components adapt based on their available space rather than the overall screen size.

## Enhanced CSS Mathematical Functions

CSS now supports more sophisticated mathematical operations:

```css
/* Trigonometric functions for circular layouts */
.circle-item:nth-child(1) { 
  transform: translate(calc(cos(0deg) * 100px), calc(sin(0deg) * 100px)); 
}
.circle-item:nth-child(2) { 
  transform: translate(calc(cos(60deg) * 100px), calc(sin(60deg) * 100px)); 
}
.circle-item:nth-child(3) { 
  transform: translate(calc(cos(120deg) * 100px), calc(sin(120deg) * 100px)); 
}

/* More powerful clamp, min, max */
.responsive-text {
  font-size: clamp(1rem, 4vw, 2rem);
}
```

These mathematical functions open up new possibilities for creating dynamic, mathematically-driven designs like circular layouts and complex animations.