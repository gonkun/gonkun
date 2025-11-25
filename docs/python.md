# Python

## Useful Snippets

### Edit file content from multiline to one line with '\r\n'

```Python
with open("crt_with_intermediate.crt", "r") as f:
  lines = f.read().splitlines()

one_line = "\\r\\n".join(lines)
print(one_line)
```

