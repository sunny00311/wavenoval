# WebNovel DOM Paragraph Inspector

A simple browser-console snippet for inspecting paragraph elements on a WebNovel chapter page.

This is useful when the website's HTML structure changes and you need to identify which elements contain the chapter text before writing a scraper.

## What This Does

The following JavaScript:

```js
[...document.querySelectorAll('.dib.pr p')]
    .slice(0, 10)
    .map((p, i) => `${i}: ${p.innerText}`)
```

does the following:

1. Finds all `<p>` elements inside elements with the classes `.dib.pr`.
2. Converts the NodeList into a normal JavaScript array.
3. Takes the first 10 paragraphs.
4. Extracts the visible text from each paragraph.
5. Adds an index (`0`, `1`, `2`, etc.) to each result.
6. Prints the results in the browser console.

Example output:

```text
0: The light fixtures on the walls gave off a bright glow, illuminating the entire room.

1: Duane sat propped up against the headboard, studying the room's furnishings...

2: A few minutes ago, he was just a student working part-time jobs...

3: He recalled the original owner's memories and discovered this was a fantasy world...
```

---

## Instructions

### 1. Open the chapter

Open the WebNovel chapter you want to inspect in your browser.

Make sure the chapter content has finished loading.

### 2. Open Developer Tools

In Chrome or another Chromium-based browser:

```text
F12
```

or:

```text
Ctrl + Shift + I
```

You can also right-click the page and select:

```text
Inspect
```

### 3. Open the Console

Inside Developer Tools, select:

```text
Console
```

### 4. Paste the code

Paste:

```js
[...document.querySelectorAll('.dib.pr p')]
    .slice(0, 10)
    .map((p, i) => `${i}: ${p.innerText}`)
```

Press:

```text
Enter
```

### 5. Check the Output

You should see an array containing the first 10 paragraphs.

For example:

```text
[
  "0: The light fixtures on the walls...",
  "1: Duane sat propped up against...",
  "2: A few minutes ago...",
  ...
]
```

If the output contains the actual chapter paragraphs, `.dib.pr p` is a useful selector for that page's current DOM structure.

---

## Copy All Paragraphs

After confirming that `.dib.pr p` contains the chapter text, remove `.slice(0, 10)`:

```js
[...document.querySelectorAll('.dib.pr p')]
    .map((p, i) => `${i}: ${p.innerText}`)
```

This displays all matching paragraphs.

---

## Copy the Text Without Numbers

If you only want the paragraph text:

```js
[...document.querySelectorAll('.dib.pr p')]
    .map(p => p.innerText.trim())
    .filter(Boolean)
    .join('\n\n')
```

This produces:

```text
The light fixtures on the walls gave off a bright glow, illuminating the entire room.

Duane sat propped up against the headboard, studying the room's furnishings...

A few minutes ago, he was just a student working part-time jobs...
```

You can then copy the result from the console.

---

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
