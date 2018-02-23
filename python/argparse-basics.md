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

Most of the example are pulled from [Argparse Basics Demo](https://github.com/macgregor/demos/blob/master/python/argparse/demo.py).
You can check out that project to run examples for all the topics in this post.

{% method %}
## Getting started

Save this in a file (say `demo.py`) and run it...

Type casting/validation, help usage, input checking. That's a lot of value from
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
```
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

Once you initialize your `argparse.ArgumentParser` you can make calls to
[argparse.ArgumentParser.add_argument](https://docs.python.org/2/library/argparse.html#argparse.ArgumentParser.add_argument)
to add positional arguments, optional arguments and subparsers to build your cli.

### Positional Arguments
This is what we used in the example above.
```python
parser.add_argument('one')
```
This creates a positional argument that will be stored in `one` in the parsed Namespace.
If not enough positional arguments are specified the program will halt. You can
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
Since optional arguments are not required you may need to check for their existence
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
wouldn't make since to provide a default.

#### Flags
A common pattern is to have boolean flags you optionally pass to the cli tool, this
is simple enough with argparse using the `action` field:
```python
#python demo.py --dry-run
parser.add_argument('--dry-run', action='store_true', default=False)
```
This will create `args.dry_run` with a default value of `False` if it isn't provided
and set `args.dry_run` to `True` if it is

{% method %}
### Lists
Both positional and optional arguments can take any number of arguments so that
the variable in the parsed arguments is a `list(obj)`

{% sample lang="python" %}
```python
import argparse

parser = argparse.ArgumentParser(description='python demo for argpase - lists usage')
parser.add_argument('list', help='this is required list of arguments', nargs='+')
parser.add_argument('--optional-list-fixed', help='this is an optional list of 3 arguments', nargs=3)
parser.add_argument('--optional-list-int', help='this is an optional list of ints', nargs='+', type=int)

args = parser.parse_args()

print 'parsed_args.list = \'%s\'' % "\',\'".join(args.list)
print 'parsed_args.optional_list_fixed = \'%s\'' % "\',\'".join(args.optional_list_fixed)
print 'parsed_args.optional_list_int = %s' % ','.join([str(i) for i in args.optional_list_int])
```

{% sample lang="bash" %}
```
18:19:02 > macgregor > .../demos/python/argparse > master > python demo.py --help
usage: demo.py [-h]
                [--optional-list-fixed OPTIONAL_LIST_FIXED OPTIONAL_LIST_FIXED OPTIONAL_LIST_FIXED]
                [--optional-list-int OPTIONAL_LIST_INT [OPTIONAL_LIST_INT ...]]
                list [list ...]

python demo for argpase - lists usage

positional arguments:
  list                  this is required list of arguments

optional arguments:
  -h, --help            show this help message and exit
  --optional-list-fixed OPTIONAL_LIST_FIXED OPTIONAL_LIST_FIXED OPTIONAL_LIST_FIXED
                        this is an optional list of 3 arguments
  --optional-list-int OPTIONAL_LIST_INT [OPTIONAL_LIST_INT ...]
                        this is an optional list of ints

18:20:54 > macgregor > .../demos/python/argparse > master > python demo.py a b c d --optional-list-fixed one two three --optional-list-int 1 2 3 4 5 6
parsed_args.list = 'a','b','c','d'
parsed_args.optional_list_fixed = 'one','two','three'
parsed_args.optional_list_int = 1,2,3,4,5,6
```
{% endmethod %}

{% method %}
## Subparsers
Subparsers can be used to parse commands differently based on positional arguments
that came before. For example git commands use different verbs and nouns to perform
complex operations. `git add demo.py`, `git commit -m 'adding demo.py'`,
`git checkout -b new_branch`, etc. You can think of each of these subcommands as
their own processor attached to the root git parser.

One thing that bugs me is that when I add an optional argument to the root parser
so that all subcommands get it, like:
```python
git_parser.add_argument('--global-option', help='all subcommands can use this argument')
```
I have to specify the parameter before the subcommand. So `python git.py --global-option foo add file.txt`
works but `python git.py add file.txt --global-option foo` gives an error. Ideally
I would be able to specify this option either before or after, the next section shows
one way to do this.

{% sample lang="python" %}
```python
import argparse

def git_add(args):
    print 'adding file to git...'
    print 'parsed_args.filename = \'%s\'' % args.filename
    print 'parsed_args.global_option = \'%s\'' % args.global_option

def git_commit(args):
    print 'committing to git...'
    print 'parsed_args.message = \'%s\'' % args.message
    print 'parsed_args.global_option = \'%s\'' % args.global_option

git_parser = argparse.ArgumentParser(prog='git', description='python demo for argpase - subparsers usage')
git_parser.add_argument('--global-option', help='all subcommands can use this argument')

#configure the root parser object to process subcommands
git_subparsers = git_parser.add_subparsers()

#create a subparser to handle arguments when 'git add' is called
#the title of the subparser with the argument used as the command when using the cli
git_file_add_parser = git_subparsers.add_parser('add', description='Add file contents to the index')
git_file_add_parser.add_argument('filename', help='filename to add to index')

#this is a trick to have a function called by the parser which is matched
#so if git add ... is called the git_add() method will be called with the parsed args
#and it git commit ... is called the git_commit method will be called
git_file_add_parser.set_defaults(func=git_add)

#create a subparser to handle arguments when 'git commit' is called
git_commit_parser = git_subparsers.add_parser('commit', description='Record changes to the repository')
git_commit_parser.add_argument('-m', '--message', help='Use the given <msg> as the commit message')

#another example of this can be found at https://docs.python.org/2.7/library/argparse.html#argparse.ArgumentParser.add_subparsers
git_commit_parser.set_defaults(func=git_commit)

args = git_parser.parse_args()

#call the function matched by subparsers
args.func(args)
```

{% sample lang="bash" %}
```
18:54:59 > macgregor > .../demos/python/argparse > master > python git.py --help
usage: git [-h] [--global-option GLOBAL_OPTION] {add,commit} ...

python demo for argpase - subparsers usage

positional arguments:
  {add,commit}

optional arguments:
  -h, --help            show this help message and exit
  --global-option GLOBAL_OPTION
                        all subcommands can use this argument

18:55:40 > macgregor > .../demos/python/argparse > master > python git.py add --help
usage: git add [-h] filename

Add file contents to the index

positional arguments:
  filename    filename to add to index

optional arguments:
  -h, --help  show this help message and exit

18:55:47 > macgregor > .../demos/python/argparse > master > python git.py commit --help
usage: git commit [-h] [-m MESSAGE]

Record changes to the repository

optional arguments:
  -h, --help            show this help message and exit
  -m MESSAGE, --message MESSAGE
                        Use the given <msg> as the commit message

18:59:12 > macgregor > .../demos/python/argparse > master > python git.py --global-option foo add hello.txt
adding file to git...
parsed_args.filename = 'hello.txt'
parsed_args.global_option = 'foo'

18:59:55 > macgregor > .../demos/python/argparse > master > python git.py --global-option foo commit -m 'my commit message'
committing to git...
parsed_args.message = 'my commit message'
parsed_args.global_option = 'foo'
```

{% endmethod %}

{% method %}
## Inheriting arguments
If you are using subparsers and want to inherit arguments (say, a debug flag or global config file or something)
You can create a parent parser which is used to inherit arguments from. We can modify the previous example a bit
to add a debug flag which both the `git add` and `git commit` commands inherit. Its a little
clunky but it works well.

{% sample lang="python" %}
```python
import argparse

git_parser = argparse.ArgumentParser(prog='git', description='python demo for argpase - subparsers usage')

#configure the root parser object to process subcommands
git_subparsers = git_parser.add_subparsers()

#create a parent parser to inherit arguments from
parent = argparse.ArgumentParser(add_help=False)
parent.add_argument('-d', '--debug', action='store_true', default=False, help='enable debug logging')

#create a subparser to handle arguments when 'git add' is called
#the title of the subparser with the argument used as the command when using the cli
#pass in the parent parser with the debug flag using the 'parents' keyword, note it takes
#a list
git_file_add_parser = git_subparsers.add_parser('add', description='Add file contents to the index', parents=[parent])
git_file_add_parser.add_argument('filename', help='filename to add to index')

...
```

{% sample lang="bash" %}
```
18:55:40 > macgregor > .../demos/python/argparse > master > python git.py add --help
usage: git add [-h] [-d] filename

Add file contents to the index

positional arguments:
  filename     filename to add to index

optional arguments:
  -h, --help   show this help message and exit
  -d, --debug  enable debug logging

18:59:12 > macgregor > .../demos/python/argparse > master > python git.py add hello.txt --debug
adding file to git...
parsed_args.filename = 'hello.txt'
parsed_args.debug = 'True'
```
{% endmethod %}

## Conclusion
All of this should be more than enough to make a sophisticated cli. Check out my
argparse demo below for some more runnable examples.

### Additional Resources
* [Argparse Basics Demo](https://github.com/macgregor/demos/blob/master/python/argparse/demo.py)
* [argparse docs](https://docs.python.org/2/library/argparse.html)
* [Another argparse tutorial](https://pymotw.com/2/argparse/)
* [Yet another argparse tutorial...](https://mkaz.tech/code/python-argparse-cookbook/)
