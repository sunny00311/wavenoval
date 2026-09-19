# WebNovel DOM Paragraph Inspector

A simple browser-console snippet for inspecting paragraph elements on a WebNovel chapter page.

This is useful when the website's HTML structure changes and you need to identify which elements contain the chapter text before writing a scraper.


## Download as a `.txt` File

Once the selector has been verified, the following snippet downloads the matching paragraphs as a plain-text file:

```js
(() => {
    const paragraphs = [...document.querySelectorAll('.dib.pr p')];

    if (paragraphs.length === 0) {
        console.error('No paragraphs found.');
        return;
    }

    const text = paragraphs
        .map(p => p.innerText.trim())
        .filter(Boolean)
        .join('\n\n');

    const blob = new Blob([text], {
        type: 'text/plain;charset=utf-8'
    });

    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');

    a.href = url;
    a.download = 'chapter.txt';

    document.body.appendChild(a);
    a.click();
    a.remove();

    URL.revokeObjectURL(url);

    console.log(`Downloaded ${paragraphs.length} paragraphs.`);
})();
```

---

## Troubleshooting

### `[]` is returned

If this:

```js
[...document.querySelectorAll('.dib.pr p')]
```

returns:

```text
[]
```

the selector is not matching the current page DOM.

Inspect the page again and look for the `<p>` elements containing the chapter text.

You can also test:

```js
document.querySelectorAll('p').length
```

Then inspect some paragraphs:

```js
[...document.querySelectorAll('p')]
    .slice(0, 20)
    .map((p, i) => `${i}: ${p.innerText}`)
```

### The output contains unrelated text

`.dib.pr p` may match paragraphs outside the chapter.

Inspect the matching elements:

```js
[...document.querySelectorAll('.dib.pr p')]
    .slice(0, 20)
    .forEach((p, i) => {
        console.log(i, p.parentElement, p.innerText);
    });
```

Find the common parent/container surrounding the actual chapter paragraphs and create a more specific CSS selector.

---

## Important Note

Web pages can change their HTML structure at any time. A selector that works today may stop working after a website update.

This repository is intended for **DOM inspection and learning browser-based web scraping techniques**. Only retrieve content you are authorized to access, and respect the website's terms of service and applicable copyright laws.
