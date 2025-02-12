# Markdown language tips & tricks

I found recently that I can make nice formatting while creating notes on my NextCloud Notes and on GitHub.
Here I will share what I have learned so far.

## Hashes in markdown mean headings

```markdown
# Titel
## Heading1
### Heading2
#### Heading3
```
# Titel
## Heading1
### Heading2
#### Heading3

## Fenced code

starting with three backtricks and the name of the language you fence \"\`\`\`markdown", \"\`\`\`bash\" and ending with three apostrophs \"\`\`\`\"

````markdown

```bash
#!/bin/bash
echo "My bash script."
```

````

result

```bash
#!/bin/bash

echo "My bash script."
```

## Escape special caracters and fenced code

this is done with backslash "\\"

if you need to escape fanced code add more backticks

`````markdown
````markdown
```bash
#!/bin/bash
echo "My bash script."
```
````
`````

## Source

[markdownguide.org](https://www.markdownguide.org/basic-syntax/)




