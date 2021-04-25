---
title: "Markdown Reference"
date: 2021-02-22T17:47:44Z
draft: false
tags: ["Markdown"]
cover:
    image: "images/cover.png"
    alt: "<alt text>"
    caption: "A Markdown Reference"
    relative: false # To use relative path for cover image, used in hugo Page-bundles
---

If you scroll to the bottom of any page of this site, you will notice the words "Powered by [Hugo](https://gohugo.io)", and all posts generated in a Hugo static web app are written in markdown. Having owned a [Github](https://github.com/markkerry) account for a few years now, I have some experience writing docs in markdown, but at times have to resort to looking up Markdown style guides.

Here is my quick reference for writing in Markdown.

<br>

## Headers

```markdown
# Header 1
## Header 2
### Header 3
```

Display as follows:

# Header 1

## Header 2

### Header 3

<br>

## Code/Config Snips

Add a code or config file snippets by wrapping it in three backticks (```) above and below the code block.

\```go

package main

import "fmt"

func main() {
  
}

\```


```go
package main

import "fmt"

func main() {
}
```

Or you can wrap a word in single backticks: \`Command` goes here

Or you can wrap a word in single backticks: `Command` goes here

## Escape Characters

In order to show the command above I had to add in a backslash to show the literal backtick like follows:

```markdown
Or you can wrap a word in single backticks: \`Command` goes here
```

<br>

## Bold and Italics

Bold __text__ can be defined in **two** different ways. Using double underscores ( __ ) or double asterisk ( ** )

```markdown
Bold __text__ can be defined in **two** different ways
```

Italic _text_ can be defined in *two* different ways. Using single underscores `'_'` or single asterisk `'*'`

```markdown
Italic _text_ can be defined in *two* different ways
```

<br>

## Lists

Ordered lists as follows

```markdown
1. Item 1
1. Item 2
1. Item 3
```

1. Item 1
1. Item 2
1. Item 3


Unordered lists

```markdown
* Item 1
* Item 2
  * Item 2a
  * Item 2b
```

* Item 1
* Item 2
  * Item 2a
  * Item 2b

<br>

## Images

```markdown
![imageName](images/docker.png)
```

![docker](images/docker.png)

<br>

## Links

Links can be written as follows:

```markdown
link to [Github](https://github.com/markkerry)
```

Which will display as follows:

link to [Github](https://github.com/markkerry)

<br>

## Images with Links

```markdown
[![Github](images/github.png)](https://github.com/markkerry)
```

Which will display as follows with a click-able image:

[![Github](images/github.png)](https://github.com/markkerry)

<br>

## Tables

Structure them as follows

```markdown
| Name | Age |
| ---- | --- |
| Mark | Old |
```

To display the following:

| Name | Age |
| ---- | --- |
| Mark | Old |

<br>

## Tasks

Can be written like this

```markdown
* [x] Task complete
* [ ] Task pending
```

And display as folllows

* [x] Task complete
* [ ] Task pending

<br>

## Blockquotes

```markdown
> Note: Please ensure you...
>> Also note...
```

> Note: Please ensure you...
>> Also note...

<br>

## Line Breaks

Occasionally, you may want to space out the paragraghs or create more space between the next header. You can acheive this by adding the `<br>` html tag on it's own line between the paragraghs where you want more space.

## Comments

You can add comments to your markdown document which will not be rendered to the reader.

```markdown
### Comment Example

<!--
Remember to Update this part
-->

Text to be updated
```

### Comment Example

<!--
Remember to Update this part
-->

Text to be updated
