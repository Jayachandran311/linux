# Linux — Text Processing

## `grep` — Search Text

```bash
grep "pattern" file.txt               # search in file
grep -i "pattern" file.txt            # case-insensitive
grep -r "pattern" /path/              # recursive search
grep -n "pattern" file.txt            # show line numbers
grep -l "pattern" *.conf              # list filenames that match
grep -v "pattern" file.txt            # invert — lines NOT matching
grep -c "pattern" file.txt            # count matching lines
grep -w "word" file.txt               # whole word only
grep -x "exact line" file.txt         # match whole line

# Context around matches
grep -A 3 "ERROR" app.log             # 3 lines After match
grep -B 2 "ERROR" app.log             # 2 lines Before match
grep -C 2 "ERROR" app.log             # 2 lines Before and After

# Extended regex (use -E or egrep)
grep -E "error|warning|critical" app.log
grep -E "^[0-9]{4}-[0-9]{2}-[0-9]{2}" logfile    # lines starting with date
grep -E "^$" file.txt                              # empty lines

# Only print matched part
grep -o "[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+" access.log   # extract IPs
grep -oE "[0-9]{1,3}(\.[0-9]{1,3}){3}" access.log          # IPs with -E

# Useful combinations
grep "ERROR" app.log | grep -v "DEBUG"     # errors except debug
grep -r "TODO" src/ --include="*.py"       # only in .py files
grep -rl "FIXME" src/                      # just filenames
```

---

## `sed` — Stream Editor

### Substitution (`s/find/replace/flags`)

```bash
sed 's/old/new/' file.txt           # replace first occurrence per line
sed 's/old/new/g' file.txt          # replace ALL occurrences per line
sed 's/old/new/gi' file.txt         # case-insensitive
sed 's/old/new/2' file.txt          # replace second occurrence only
sed 's/old/new/g; s/foo/bar/g'      # multiple substitutions

# Edit in-place
sed -i 's/localhost/db.internal/g' config.txt
sed -i.bak 's/v1/v2/g' config.txt  # keep backup as config.txt.bak

# Use different delimiter (useful when pattern contains /)
sed 's|/usr/local|/opt|g' config.txt
sed 's#old#new#g' file.txt
```

### Delete lines

```bash
sed '3d' file.txt                   # delete line 3
sed '2,5d' file.txt                 # delete lines 2-5
sed '/pattern/d' file.txt           # delete lines matching pattern
sed '/^$/d' file.txt                # delete empty lines
sed '/^#/d' file.txt                # delete comment lines
sed '/^[[:space:]]*$/d' file.txt    # delete blank/whitespace-only lines
```

### Print specific lines

```bash
sed -n '5p' file.txt                # print line 5 only
sed -n '5,10p' file.txt             # print lines 5-10
sed -n '/START/,/END/p' file.txt    # print between START and END markers
sed -n '/pattern/p' file.txt        # print lines matching pattern
```

### Other operations

```bash
sed '5i\New line here' file.txt     # insert before line 5
sed '5a\New line here' file.txt     # append after line 5
sed '1q' file.txt                   # print first line and quit (like head -1)
```

---

## `awk` — Pattern Scanning and Processing

`awk` processes text field by field. Default field separator: whitespace. Each line = a **record**.

### Basic syntax

```bash
awk 'pattern { action }' file
```

### Built-in variables

| Variable | Meaning |
|----------|---------|
| `$0` | Entire current line |
| `$1`, `$2`… | Fields 1, 2… |
| `$NF` | Last field |
| `NR` | Current line number (record number) |
| `NF` | Number of fields in current line |
| `FS` | Field separator (default: space) |
| `OFS` | Output field separator |
| `RS` | Record separator (default: newline) |

### Examples

```bash
# Print specific columns
awk '{print $1}' file.txt               # first field
awk '{print $1, $3}' file.txt           # first and third fields
awk '{print $NF}' file.txt              # last field
awk '{print NR, $0}' file.txt           # print line numbers

# Custom separator
awk -F: '{print $1, $3}' /etc/passwd    # use : as separator
awk -F',' '{print $2}' data.csv         # CSV second column

# Filtering
awk '$3 > 1000' /etc/passwd             # lines where field 3 > 1000
awk '/root/' /etc/passwd                # lines containing "root"
awk '!/^#/' config.file                 # skip comment lines

# Calculations
awk '{sum += $1} END {print "Total:", sum}' numbers.txt
awk 'NR > 1 {total += $3} END {print "Average:", total/(NR-1)}' data.csv

# Formatted output
awk -F: '{printf "%-20s %5d\n", $1, $3}' /etc/passwd

# Multi-condition
awk '$3 >= 1000 && $3 < 65534 {print $1}' /etc/passwd   # regular users

# BEGIN and END blocks
awk 'BEGIN {print "=== Users ==="} {print $1} END {print "=== Done ==="}' /etc/passwd

# Count occurrences
awk '{count[$1]++} END {for (k in count) print k, count[k]}' logfile.txt
```

---

## `cut` — Extract Fields

```bash
cut -d: -f1 /etc/passwd               # field 1, delimiter :
cut -d, -f1,3 data.csv                # fields 1 and 3, delimiter ,
cut -d: -f1-3 /etc/passwd             # fields 1 through 3
cut -c1-10 file.txt                   # characters 1-10 (fixed-width)
cut -c5- file.txt                     # from character 5 to end
```

---

## `sort` and `uniq`

```bash
sort file.txt                    # sort alphabetically
sort -r file.txt                 # reverse sort
sort -n numbers.txt              # numeric sort
sort -rn numbers.txt             # reverse numeric sort
sort -k2 file.txt                # sort by field 2
sort -k2,2 -k1,1 file.txt        # sort by field2, then field1
sort -t: -k3 -n /etc/passwd      # sort by UID

sort -u file.txt                 # sort and remove duplicates

uniq file.txt                    # remove consecutive duplicates (pre-sort first)
uniq -c file.txt                 # count occurrences
uniq -d file.txt                 # only print duplicate lines
uniq -u file.txt                 # only print unique lines

# Common pattern: count frequency
sort data.txt | uniq -c | sort -rn     # most frequent items first
cat access.log | awk '{print $1}' | sort | uniq -c | sort -rn | head -10
```

---

## `tr` — Translate Characters

```bash
echo "hello" | tr 'a-z' 'A-Z'       # lowercase to uppercase
echo "Hello World" | tr 'A-Z' 'a-z' # uppercase to lowercase
echo "hello   world" | tr -s ' '    # squeeze multiple spaces to one
echo "hello:world" | tr ':' '\n'     # replace : with newline
cat file.txt | tr -d '\r'            # remove carriage returns (Windows)
cat file.txt | tr -d '\n'            # join all lines
echo "abc123def" | tr -d '0-9'      # remove all digits
```

---

## `wc` — Word Count

```bash
wc file.txt                   # lines, words, bytes
wc -l file.txt                # lines only
wc -w file.txt                # words only
wc -c file.txt                # bytes only
wc -m file.txt                # characters only

ls | wc -l                    # count files in directory
grep "ERROR" app.log | wc -l  # count error lines
```

---

## `diff` — Compare Files

```bash
diff file1.txt file2.txt              # show differences
diff -u file1.txt file2.txt           # unified format (like git diff)
diff -y file1.txt file2.txt           # side-by-side
diff -r dir1/ dir2/                   # compare directories recursively
diff -q dir1/ dir2/                   # only show filenames that differ

# Apply a patch
diff -u original.txt modified.txt > changes.patch
patch original.txt < changes.patch
```

---

## Practical Pipelines

```bash
# Top 10 most frequent IPs in nginx log
awk '{print $1}' /var/log/nginx/access.log | sort | uniq -c | sort -rn | head -10

# Count lines per log level
awk '{print $3}' app.log | sort | uniq -c | sort -rn

# Find all unique usernames in passwd
cut -d: -f1 /etc/passwd | sort

# Extract all email addresses from a file
grep -oE "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" file.txt | sort -u

# Remove blank lines and comments from config
grep -vE '^\s*(#|$)' /etc/nginx/nginx.conf

# Show disk usage sorted by size
du -sh /var/log/* 2>/dev/null | sort -h | tail -20

# Find the most recently modified files
find . -type f -printf '%T@ %p\n' | sort -rn | head -10 | awk '{print $2}'
```

---

## Key Takeaways

> - `grep -r "pattern" .` for recursive search; `-i` for case-insensitive; `-n` for line numbers
> - `sed 's/old/new/g' file` for substitution; `-i` to edit in-place
> - `awk -F: '{print $1}' /etc/passwd` — `awk` is the go-to for column-based processing
> - `sort | uniq -c | sort -rn` is the classic frequency-count pipeline
> - `cut -d: -f1` extracts columns from delimited files
> - Master these tools — they're fundamental to log analysis and data processing
