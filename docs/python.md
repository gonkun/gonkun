# Python

## Useful Snippets

### Edit file content from multiline to one line with '\r\n'

```Python
with open("text_file.txt", "r") as f:
  lines = f.read().splitlines()

one_line = "\\r\\n".join(lines)
print(one_line)
```

