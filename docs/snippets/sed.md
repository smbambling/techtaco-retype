---
title: Sed
---

# Useful Sed Snippets and Oneliners

## Update multiple files with find + sed

```bash Remove lines matching pattern(gimmick) from Markdown files
find . -name "*.md" -type f | xargs -o sed -i '' '/gimmick/d'
```
