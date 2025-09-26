# BASH

## Loops

### For

### Simple for loop

```bash
for i in {1..10}; do
  echo $i
done
```

### For loop to list files

```bash
for i in $(ls); do
  echo $i
done
```

### While

### Read a file line by line

```bash
while read -r line; do
  echo $line
done < file.txt
```

## Conditionals

### If

### Check if a file exists

```bash
if [ -f file.txt ]; then
  echo "file exists"
fi
```

### Check if a directory exists

```bash
if [ -d /tmp ]; then
  echo "directory exists"
fi
```

### Check if a variable is empty

```bash
if [ -z "" ]; then
  echo "variable is empty"
fi
```

### Check if a variable is not empty

```bash
if [ -n "hello" ]; then
  echo "variable is not empty"
fi
```

### Integer comparisons

```bash
if [ 1 -eq 1 ]; then
  echo "are equal"
fi

if [ 1 -ne 2 ]; then
  echo "are not equal"
fi

if [ 1 -gt 0 ]; then
  echo "is greater than"
fi

if [ 1 -lt 2 ]; then
  echo "is less than"
fi

if [ 1 -ge 1 ]; then
  echo "is greater or equal"
fi

if [ 1 -le 1 ]; then
  echo "is less or equal"
fi
```

## Variables

### Variable expansion examples

```bash
echo $variable
echo ${variable}
echo "the value of the variable is ${variable}"
```

### Variable assignment and expansion

```bash
variable="hello"
echo $variable
echo ${variable}
echo "the value of the variable is ${variable}"
```

## Arrays

### Array definition and usage

```bash
# Define an array
array=("one" "two" "three")

# Access individual elements
echo ${array[0]}
echo ${array[1]}
echo ${array[2]}

# Access all elements
echo ${array[@]}

# Get the number of elements in the array
echo ${#array[@]}
```

## Functions

### Simple function definition

```bash
function hello {
  echo "hello"
}

# Call the function
hello
```

### Function with an argument

```bash
function goodbye {
  echo "goodbye $1"
}

# Call the function with an argument
goodbye world
```

## Awk

### Print the first column of a file

```bash
awk '{print $1}' file.txt
```

### Print the last column of a file

```bash
awk '{print $NF}' file.txt
```

### Print the first column from files containing a specific word

```bash
grep -l "hello" * | xargs awk '{print $1}'
```

## Sed

### Replace the first occurrence of a string

```bash
sed 's/hello/goodbye/' file.txt
```

### Replace all occurrences of a string (global replacement)

```bash
sed 's/hello/goodbye/g' file.txt
```

### Replace a string and modify the file in-place

```bash
sed -i 's/hello/goodbye/' file.txt
```

### Delete lines containing a specific string

```bash
sed '/hello/d' file.txt
```

## Grep

### Search for a string in a single file

```bash
grep "hello" file.txt
```

### Search for a string in all files in the current directory

```bash
grep "hello" *
```

### Search for a string recursively starting from the current directory

```bash
grep -r "hello" .
```

### Search recursively and display only the names of files that contain the string

```bash
grep -rl "hello" .
```

## Find

### Find files by name

```bash
find . -name "file.txt"
```

### Find directories by name

```bash
find . -type d -name "directory"
```

### Find files by permissions

```bash
find . -type f -perm 777
```

### Find files modified within the last 7 days

```bash
find . -type f -mtime -7
```

### Find files larger than 1MB

```bash
find . -type f -size +1M
```

## xargs

### Build and execute commands from standard input

```bash
# Warning: This command can be dangerous. It will delete files listed by 'ls'.
ls | xargs rm
```

## curl

### Make a simple GET request

```bash
curl https://google.com
```

### Make a POST request with URL-encoded data

```bash
curl -X POST -d "param1=value1&param2=value2" https://google.com
```

### Make a POST request with a JSON file

```bash
curl -X POST -H "Content-Type: application/json" -d @file.json https://google.com
```
