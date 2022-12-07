# BASH


## Common Commands

* Find files with an specific name and execute `gsed` for replace a string for another<br>
`find . -type f -name 'filename' | xargs gsed -i 's/<pattern_A>/<pattern_B>/g'`
* Find files with an specific name and execute `gsed` for delete a line where a string pattern is matched<br>
`find . -type f -name 'filename' | xargs gsed -i '/<pattern>/d'`

## Tutorials and Guides

### Best Practices
- [Shell Script Best Practices](https://sharats.me/posts/shell-script-best-practices/)