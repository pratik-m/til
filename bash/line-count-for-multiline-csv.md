# Line Count for multiline CSV in Python for Bash

If you want to get the line count in unix, you use `wc -l`. This returns the line count of the file. 
But, what if you have a csv file which has its data spanned across multipe lines? Here is the one liner to get the line
count 

```
cat input.csv | python -c "import sys, csv; print(sum(1 for l in csv.reader(sys.stdin.read() )))"
```
