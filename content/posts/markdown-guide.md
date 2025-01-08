---
title: "Markdown Syntax Guide"
date: 2025-01-05
draft: false
tags: ["markdown", "hugo", "guide"]
math: true
---

Markdown is a lightweight and easy-to-use syntax for styling all forms of writing. This guide will cover the essential elements you might need when writing content for your Hugo website.

---

## Headings

Use `#` symbols for headings. The number of `#` indicates the level of the heading.

```markdown
# H1 Heading
## H2 Heading
### H3 Heading
#### H4 Heading
##### H5 Heading
###### H6 Heading
```

# Welcome to My Blog

## About Me

### Interests

#### Projects

##### Achievements

###### Contact Information

---

## Paragraphs and Line Breaks

Paragraphs are created by leaving blank lines between blocks of text. For line breaks within a paragraph, end the line with two spaces.

```markdown
This is a paragraph.

And this is another one.
```

Welcome to my blog. Here, I share my thoughts and experiences.

I hope you find something useful here!

---

## Emphasis

You can emphasize text using asterisks (`*`) or underscores (`_`).

- **Bold:** `**bold text**` or `__bold text__`
- *Italic:* `*italic text*` or `_italic text_`
- ***Bold Italic:*** `***bold italic text***` or `___bold italic text___`

This is **very important** information.

Here's some *emphasized text*.

And this is ***really important***.

---

## Lists

Create unordered lists with `-`, `+`, or `*`. Ordered lists use numbers followed by a period.

```markdown
Unordered list:
- Item 1
- Item 2
- Item 3

Ordered list:
1. First item
2. Second item
3. Third item
```

Unordered list:
- Apples
- Bananas
- Oranges

Ordered list:
1. Wake up
2. Eat breakfast
3. Go to work

---

## Links

Links are created using `[text](URL)`.

```markdown
[Google](https://www.google.com)
```

Check out [Hugo's official website](https://gohugo.io) for more information.

---

## Images

Images are added using `![alt text](image URL)`.

```markdown
![Alt Text](/path/to/image.png)
```

![My Logo](/img/avatar-200.png)

---

## Code Blocks

Inline code uses backticks (`), while code blocks use triple backticks (```). You can also specify the language for syntax highlighting.

```markdown
Inline code: `code here`

Code block:
```python
def hello_world():
    print("Hello, world!")
```

Inline code: `print("Hello, world!")`

Code block:
```javascript
function greet(name) {
    console.log("Hello, " + name);
}
```

---

## Code Collapse

Hugo shortcodes allow you to create collapsible code blocks using the `collapse` shortcode. This feature is particularly useful when you want to hide long code snippets to make your content more readable and organized.

The collapsible blocks can be customized with a title parameter, which appears as the clickable header. When readers click on the title, the code block expands or collapses accordingly.

{{< collapse title="View JavaScript Example" >}}
```javascript
function fibonacci(n) {
    if (n <= 1) return n;
    return fibonacci(n-1) + fibonacci(n-2);
}
```
{{< /collapse >}}

{{< collapse title="View CSS Example" >}}
```css
.container {
    display: flex;
    justify-content: center;
    align-items: center;
}
```
{{< /collapse >}}

---

## Blockquotes

Blockquotes are created using `>`.

```markdown
> This is a blockquote.
```

> Always remember that you are absolutely unique. Just like everyone else.

---

## Horizontal Rules

Horizontal rules are created using three or more hyphens (`---`), asterisks (`***`), or underscores (`___`).

```markdown
---
```

---

## Tables

Tables are created using pipes (`|`) and dashes (`-`).

```markdown
| Column 1 | Column 2 | Column 3 |
| --- | --- | --- |
| Row 1 Data 1 | Row 1 Data 2 | Row 1 Data 3 |
| Row 2 Data 1 | Row 2 Data 2 | Row 2 Data 3 |
```

| Name | Age | Location |
| --- | --- | --- |
| Alice | 30 | New York |
| Bob | 25 | Los Angeles |

---

## Footnotes

Footnotes are created using `[^key]` and defined at the bottom of the document.

```markdown
This is a sentence with a footnote.[^1]

[^1]: This is the footnote text.
```

Hugo is a popular static site generator.[^hugo]

[^hugo]: Hugo is written in Go and is known for its speed.

---

## LaTeX Formulas

### Inline Formulas

Euler's identity $e^{i\pi} + 1 = 0$ is one of the most beautiful equations in mathematics.

### Block Formulas

The quadratic formula is given by:

$$ x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a} $$

### Complex Formulas

A definite integral:

$$ \int_{-\infty}^{\infty} e^{-x^2} \, dx = \sqrt{\pi} $$

Schrödinger Equation:

$$
i\hbar\frac{\partial}{\partial t}\Psi(\mathbf{r},t) = \hat H \Psi(\mathbf{r},t)
$$

## Front Matter

Hugo uses front matter to define metadata for each page. Commonly used fields include `title`, `date`, `draft`, and `tags`.

```yaml
---
title: "My First Post"
date: 2023-10-01T12:00:00Z
draft: false
tags: ["markdown", "hugo"]
---
```

---

## Conclusion

Markdown offers a simple yet powerful way to create content for Hugo sites. By mastering the syntax, you can effectively build and manage your website articles with ease.