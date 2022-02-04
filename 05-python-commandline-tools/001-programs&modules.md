To create a Python program that can run from the command line, the first thing we do is to add the following to the bottom of the file:

```python
if __name__ == '__main__':
```

check tells us whether the file is running as a standalone program or whether it is being imported as a module by some other program. When we import a Python file as a module in another program, the `__name__` variable is automatically set to the name of the file. When we run a Python file as a standalone program, on the other hand, `__name__` is always set to the special string `"__main__"`

```python
def main():
    # code goes here


if __name__ == "__main__":
    main()
```


## Handling command-line options

The main function in a program usually starts by parsing any options the user gave on the command line. The most commonly used library for doing this in Python is argparse, which can handle options with or without arguments, convert arguments from strings to numbers or other types, display help, and many other things.

```python
# script_template.py

import argparse


def main(args):
    print('Input file:', args.infile)
    print('Output file:', args.outfile)


if __name__ == '__main__':
    USAGE = 'Brief description of what the script does.'
    parser = argparse.ArgumentParser(description=USAGE)
    parser.add_argument('infile', type=str,
                        help='Input file name')
    parser.add_argument('outfile', type=str,
                        help='Output file name')
    args = parser.parse_args()
    main(args)
```

If script_template.py is run as a standalone program at the command line, then __name__ == '__main__' is true, so the program uses argparse to create an argument parser. It then specifies that it expects two command-line arguments: an input filename (infile) and an output filename (outfile). The program uses parser.parse_args() to parse the actual command-line arguments given by the user and stores the result in a variable called args, which it passes to main. That function can then get the values using the names specified in the parser.add_argument calls.

This is how we would call this program:

```shell
$ cd ~/zipf
$ python script_template.py in.csv out.png
```

And this would the output:

```shell
Input file: in.csv
Output file: out.png
```

### Another program: counting words

```python
# bin/countwords.py

"""
Count the occurrences of all words in a text
and output them in CSV format.
"""

import sys
import argparse
import string
import csv
from collections import Counter


def collection_to_csv(collection, num=None):
    """Write collection of items and counts in csv format."""
    collection = collection.most_common()
    if num is None:
        num = len(collection)
    writer = csv.writer(sys.stdout)
    writer.writerows(collection[0:num])


def count_words(reader):
    """Count the occurrence of each word in a string."""
    text = reader.read()
    chunks = text.split()
    npunc = [word.strip(string.punctuation) for word in chunks]
    word_list = [word.lower() for word in npunc if word]
    word_counts = Counter(word_list)
    return word_counts


def main(args):
    """Run the command line program."""
    with open(args.infile, 'r') as reader:
        word_counts = count_words(reader)
    collection_to_csv(word_counts, num=args.num)


if __name__ == '__main__':
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument('infile', type=str,
                        help='Input file name')
    parser.add_argument('-n', '--num',
                        type=int, default=None,
                        help='Output n most frequent words')
    args = parser.parse_args()
    main(args)
```

Run the program:

```shell
$ python bin/countwords.py data/dracula.txt -n 10
```

Now, let's make changes to `add_argumrnt`:

```python
def main(args):
    """Run the command line program."""
    word_counts = count_words(args.infile)
    collection_to_csv(word_counts, num=args.num)


if __name__ == '__main__':
    parser = argparse.ArgumentParser(description=__doc__)
    parser.add_argument('infile', type=argparse.FileType('r'),
                        nargs='?', default='-',
                        help='Input file name')
    parser.add_argument('-n', '--num',
                        type=int, default=None,
                        help='Output n most frequent words')
    args = parser.parse_args()
    main(args)
```

There are two changes to how add_argument handles infile:

1. Setting type=argparse.FileType('r') tells argparse to treat the argument as a filename and open that file for reading. This is why we no longer need to call open ourselves, and why main can pass args.infile directly to count_words.
2. The number of expected arguments (nargs) is set to ?. This means that if an argument is given it will be used, but if none is provided, a default of '-' will be used instead. argparse.FileType('r') understands '-' to mean “read from standard input”; this is another Unix convention that many programs follow.

After these changes, we can create a pipeline like this to count the words in the first 500 lines of a book:

```shell
$ head -n 500 data/dracula.txt | python bin/countwords.py --n 10
```


### Positional arguments

All below options are valid:

```shell
parser.add_argument('-n', type=int, help='Limit output')
parser.add_argument('--num', type=int, help='Limit output')
parser.add_argument('-n', '--num',
                    type=int, help='Limit output')
```

The convention is for - to precede a short (single letter) option and -- a long (multi-letter) option. The user can provide optional arguments at the command line in any order they like.

Positional arguments have no leading dashes and are not optional: the user must provide them at the command line in the order in which they are specified to add_argument (unless nargs='?' is provided to say that the value is optional).