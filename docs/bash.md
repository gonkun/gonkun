# BASH


## Common Commands

* Find files with an specific name and execute `gsed` for replace a string for another
```
  find . -type f -name 'filename' | xargs gsed -i 's/<pattern_A>/<pattern_B>/g'
```
<br>

* Find files with an specific name and execute `gsed` for delete a line where a string pattern is matched
```
  find . -type f -name 'filename' | xargs gsed -i '/<pattern>/d'
```
<br>

## Tutorials and Guides

### Best Practices
- [Shell Script Best Practices](https://sharats.me/posts/shell-script-best-practices/)