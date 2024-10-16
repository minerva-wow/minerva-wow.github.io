---
title: Fonts and Performance
date: 2024-10-16 22:24:45 +0800
categories: [front-end]
tags: [fonts, optimization]    
---
When building modern websites, fonts play a crucial role in how your content is perceived. However, including custom fonts, especially external fonts, can negatively impact performance. This guide will help you decide whether to include external fonts and how to do so with the least impact on your site's performance.



## Should You Include External Fonts?

The decision to include external fonts depends on several factors, including design requirements, performance goals, and user experience. Here's a breakdown of scenarios where custom fonts might be necessary or avoidable:

### 1. **Use System Fonts for Speed**
   **When to use**: If speed and performance are top priorities, system fonts are a great choice.

   **Benefits**: System fonts (e.g., Arial, Helvetica, Times New Roman) are already preloaded on most devices, which means no extra network requests are needed.
   
   ```css
   body {
     font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
   }
   ```
   **Why it works**: By using fonts that are native to most operating systems, you reduce latency since there's no need to download additional resources.

### 2. **Custom Fonts for Branding and Aesthetics**
   **When to use**: If your brand relies heavily on specific typography or visual aesthetics, custom fonts might be necessary.

   **Trade-off**: Custom fonts require additional resources to load, which can increase the time it takes for the page to render, impacting performance.

   In this case, the goal should be to optimize font loading to minimize the impact on performance.



## How to Include Fonts Efficiently

If you decide to use custom fonts, here are several strategies to help you minimize the performance impact.

### 1. **Use Modern Formats (WOFF2)**

   Modern font formats like WOFF2 are highly compressed and provide excellent performance compared to older formats such as TTF or OTF.

   ```css
   @font-face {
     font-family: 'MyCustomFont';
     src: url('mycustomfont.woff2') format('woff2'),
          url('mycustomfont.woff') format('woff');
     font-weight: normal;
     font-style: normal;
   }
   ```
   **Why it works**: WOFF2 provides the best balance between compression and browser support.

### 2. **Self-Host Your Fonts**

   **Why self-hosting is better**: While services like Google Fonts are popular, they introduce external network requests, which can increase latency, especially for users far from the font provider's CDN. Self-hosting your fonts means they are served directly from your domain, which could lead to faster loading times, especially if your server is optimized.

   Example:

   ```css
   @font-face {
     font-family: 'OpenSans';
     src: url('/fonts/OpenSans-Regular.woff2') format('woff2'),
          url('/fonts/OpenSans-Regular.woff') format('woff');
   }
   ```

   **Additional benefit**: Self-hosting allows you to control caching headers, which can significantly improve performance.

### 3. **Use the `font-display` Property**

   The `font-display` property determines how a font is displayed while it is loading. There are several options:
   
   - **`auto`**: The default behavior, the browser decides how to handle the font display.
   - **`swap`**: Displays fallback text immediately and swaps in the custom font once it's loaded.
   - **`fallback`**: Shows fallback fonts, but gives the custom font a short window to load.
   - **`optional`**: Displays fallback fonts permanently if the custom font doesn’t load fast enough.
   
   Example:

   ```css
   @font-face {
     font-family: 'OpenSans';
     src: url('/fonts/OpenSans-Regular.woff2') format('woff2');
     font-display: swap;
   }
   ```
   
   **Why it works**: Using `font-display: swap` ensures text is readable even if the custom font hasn’t loaded yet, reducing perceived load time.

### 4. **Limit Font Weights and Styles**

   Loading multiple weights and styles of a font can significantly impact performance. Instead of including many variations, limit the font weights and styles to only those necessary for your design.

   ```css
   @font-face {
     font-family: 'OpenSans';
     src: url('/fonts/OpenSans-Regular.woff2') format('woff2');
     font-weight: 400; /* Regular weight only */
     font-display: swap;
   }
   ```

   **Why it works**: By reducing the number of font weights, you decrease the total size of font files that need to be downloaded.

### 5. **Preload Key Fonts**

   Preloading fonts tells the browser to download them earlier in the page loading process, improving performance.

   Example:

   ```html
   <link rel="preload" href="/fonts/OpenSans-Regular.woff2" as="font" type="font/woff2" crossorigin="anonymous">
   ```

   **Why it works**: Preloading the most critical fonts helps reduce the time before the fonts are rendered on the screen.



## Measuring Font Performance

After implementing custom fonts, it’s essential to measure their impact. Tools like [Google Lighthouse](https://developers.google.com/web/tools/lighthouse) and [WebPageTest](https://www.webpagetest.org/) provide detailed performance insights and highlight any font-related issues.

Key metrics to focus on:
- **First Contentful Paint (FCP)**: Measures when the first piece of content is rendered.
- **Time to Interactive (TTI)**: When the page becomes fully interactive.
- **Cumulative Layout Shift (CLS)**: Measures visual stability and how font loading affects layout shifts.



## Conclusion

Fonts are a critical part of web design but can impact your site's performance if not handled carefully. By following best practices such as using modern formats, self-hosting, limiting font weights, and leveraging the `font-display` property, you can ensure a balance between aesthetics and performance.

Implement these strategies to enhance your website's performance without compromising on design.


**References:**
- [MDN: @font-face](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face)
- [MDN: font-display](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display)