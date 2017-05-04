# Argparse Basics

Argparse is a built in python library that can be used to build simple and
sophisticated cli tools. It supports positional arguments, optional arguments,
auto generates help usage, nested subparsers to build more complicated clis
(ala git), and more. You can start using it very quickly and yet build
sophisticated interfaces.

The [pydocs for argparse](https://docs.python.org/2/library/argparse.html) are
surprisingly decent and only start to let you down when you need to do some fancy
stuff (like trying to extend
[argparse.ArgumentParser](https://docs.python.org/2/library/argparse.html#argparse.ArgumentParser))

[Argparse Basics Demo](https://github.com/macgregor/demos/blob/master/python/argparse/demo.py)


{% method %}
## Getting started

Save this in a file (say `demo.py`) and run it...

Type casting/validation, help usage, input checking. That's alot of value from
6 lines of python.

{% sample lang="python" %}
```python
import argparse

#create an ArgumentParser for out program
parser = argparse.ArgumentParser(description='python demo for argpase - basic usage')

#add arguments, lots of configurability here these are the most basic
parser.add_argument('one')
parser.add_argument('two', help='second argument')
parser.add_argument('three', type=int, help='this one is an integer')

#parse arguments, by default pulls in sys.args or you can pass them in as a list
args = parser.parse_args()

#retrieve the arguments from the returned object
print args.one
print args.two
print str(args.three)
```

{% sample lang="bash" %}
```bash
16:48:03 > mstratto > .../demos/python/argparse > master > python demo.py --help
usage: demo.py [-h] one two three

python demo for argpase - basic usage

positional arguments:
  one
  two         second argument
  three       this one is an integer

optional arguments:
  -h, --help  show this help message and exit

16:48:10 > mstratto > .../demos/python/argparse > master > python demo.py a
usage: demo.py [-h] one two three
demo.py: error: too few arguments

16:49:26 > mstratto > .../demos/python/argparse > master > python demo.py a b c
usage: demo.py [-h] one two three
demo.py: error: argument three: invalid int value: 'c'

16:50:14 > mstratto > .../demos/python/argparse > master > python demo.py a b 3
a
b
3
```
{% endmethod %}

## Adding Arguments

[argparse.ArgumentParser.add_argument](https://docs.python.org/2/library/argparse.html#argparse.ArgumentParser.add_argument)

### Positional Arguments
This is what we used in the example above.
```python
parser.add_argument('one')
```
This creates a positional argument that will be stored in `one` in the parsed Namespace.
If not enough positional arguments are specfied the program will halt. You can
specify help text to explain to users what this field is for:
```python
parser.add_argument('one', help='this is the first required argument')
```
You can also specify a type (default is string) and argparse will attempt to
automatically convert it or throw an error:
```python
parser.add_argument('one', help='this is the first required argument', type=int)
```

### Optional Arguments
Sort of key-value pairs that are optional. They are specified in either short
(e.g. `-o`) or long form (e.g. `--output`). You can specify both as well.
```python
parser.add_argument('-o', '--output', help='short and long form together')
```
When both are specified, the destination name in the parsed args namespace will
be the long form. I've seen certain special characters replaced as well, for
example `--output-dir` would become `args.output_dir`:
```python
parser.add_argument('-o', '--output-dir')
args = parser.parse_args()
print args.output_dir
```
You can also specify what you want the destination name to be if you want it to
be different:
```python
parser.add_argument('-o', '--output-dir', dest='out')
args = parser.parse_args()
print args.out
```

#### Defaults
Since optional arguments are not required you may need to check for their existance
before trying to use them in code or risk `NoneType` problems:
```python
parser.add_argument('-o', '--output-dir', dest='out')
args = parser.parse_args()
if args.out:
  method_that_hates_uninitialized_obejcts(args.out)
```
You can possibly use defaults to get around these checks:
```python
parser.add_argument('-o', '--output-dir', dest='out', default='out.txt')
args = parser.parse_args()
method_that_hates_uninitialized_obejcts(args.out)
```
Note: you cant specify defaults on positional arguments, they are required so it
wouldnt make since to provide a default.

#### Flags
A common pattern is to have boolean flags you optionally pass to the cli tool, this
is simple enough with argparse using the `action` field:
```python
#python demo.py --dry-run
parser.add_argument('--dry-run', action='store_true', default=False)
```
This will create `args.dry_run` with a default value of `False` if it isnt provided
and set `args.dry_run` to `True` if it is

### Lists
Both positional and optional arguments can take any number of arguments so that
the variable in the parsed arguments is a `list(obj)`:
```python
parser = argparse.ArgumentParser(description='python demo for argpase - lists usage')
parser.add_argument('list', help='this is required list of arguments', nargs='+')
parser.add_argument('--optional-list-fixed', help='this is an optional list of 3 arguments', nargs=3)
parser.add_argument('--optional-list-int', help='this is an optional list of ints', nargs='+', type=int)
```

## Section 1
Talk about stuff.

use `code` examples:
```
No language indicated, so no syntax highlighting.
```

## Section 2
And another...
```python
s = "Python syntax highlighting"
print s
```

{% method %}
## Code Samples w/ gitbooks-theme-api

This fancy theme lets you specify examples in multiple languages

{% sample lang="js" %}
```bash
$ npm install gitbook-api
```

{% sample lang="go" %}
```bash
$ go get github.com/GitbookIO/go-gitbook-api
```

{% common %}
This will appear for both JavaScript and Go.
{% endmethod %}


## Conclusion
Sum up the article

### Additional Resources
* [Argparse Basics Demo](https://github.com/macgregor/demos/blob/master/python/argparse/demo.py)
* [argparse docs](https://docs.python.org/2/library/argparse.html)
