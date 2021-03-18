# Bash

## Usage (keyboard shortcuts, etc.)

### Edit command in editor
* To edit the current command in your editor (e.g. vim): press `ctrl+x, ctrl+e`
* To edit the previous command, i.e. **f**ix the **c**ommand: enter `fc`

### Move the cursor
* Back one word: `alt+b`
* Forward one word: `alt+f`

## Advanced stuff

### Loop over items containing spaces

Works out-of-the-box for glob expansions. I.e. you can do `for file in *.txt` and it will correctly handle file names containing spaces.

If you want to loop over something else, say the lines of a file, you need to use `read` with `while`.
Note that by default `read` reads from STDIN, so if you perform any kind of I/O inside the loop, you may want to redirect it.
```bash
# below example redirects the input stream to file descriptor 3
# -u3 instructs read to take values from there
while read -u3 value; do
  echo "$value"
done 3< values.txt
# works with process substitution as well
while read -u3 value; do
  echo "$value"
done 3< <(cat values.txt)
```
For reference, the predefined file descriptors are:
* 0: STDIN
* 1: STDOUT
* 2: STDERR
