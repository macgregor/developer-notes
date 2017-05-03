# Generating Documentation with Sphynx
Coming from the enterprise Java world, Im used to pretty standardized ways of
documenting code that is mostly handled by my IDE. Python seemed to offer more
choices which can be good, but it felt a little overwhelming as well.

## Sphinx
Sphynx is a documentation generator written for and in Python. Alot of big python
projects (Django, SciPy, Python itself). While used heavily by the Python community,
it is not restricted to just Python. There are extensions that support many other
languages including [java](http://bronto.github.io/javasphinx/),
[javascript](http://www.sphinx-doc.org/en/stable/domains.html#the-javascript-domain),
and [many more](https://bitbucket.org/birkenfeld/sphinx-contrib/).

It uses [reStructuredText](http://docutils.sourceforge.net/docs/ref/rst/restructuredtext.html)
at its core for specifying documentation but there are
[many formats](http://stackoverflow.com/questions/3898572/what-is-the-standard-python-docstring-format)
that sphinx understands

Sphinx is not nearly as automated as I hoped it would be. It is versatile though.
Various sphinx extensions can be used to inspect your source code and generate .rst
files from your code. Sphinx can then use these .rst files and some configuration
files to transform them into different formats like html or epub.

### Installing
Using pip: `pip install sphinx`  

You should be able to install it with most Linux distro package managers, look for
"sphinx" or "python-sphinx": `dnf install python-sphinx`

### Quick Start with sphinx-apidoc
This handy tool will let you initialize your project for sphinx and generate .rst
files from your source code in a single command:
```
sphinx-apidoc . --full -o docs -H 'MyProjectName' -A 'My Name' -V '0.1'
```

When you need to to regenerate documentation:
```
sphinx-apidoc . -o docs
```

This generates the .rst files from your python code and outputs them to `./docs/`
(or wherever you specified with `-o`). It also generates various index rst files
which sphinx uses to tie together your documentation when converting them for
output.

It also created a Makefile which lets you build a variety of formats, for example:
```
make html
```

And now you have a decent looking website for your documentation...assuming it worked
the first time...

### Troubleshooting Errors
* [sphinx-autodoc ImportError: No module named foo](autodoc-import.md)


### Changing sort order
By default autodoc sorts everything alphabetically. Im not a fan, I think im pretty
good at ordering my code while writing it.

> autodoc_member_order
>
>This value selects if automatically documented members are sorted alphabetical
>(value 'alphabetical'), by member type (value 'groupwise') or by source order
>(value 'bysource'). The default is alphabetical.
>
>Note that for source order, the module must be a Python module with the source code available.

add this anywhere to conf.py:
```
autodoc_member_order = 'bysource'
```

## Conclusion
Sum up the article

### Additional Resources
* [overview of different docstring formats](http://stackoverflow.com/questions/3898572/what-is-the-standard-python-docstring-format)
* [reStructuredText quick reference](http://docutils.sourceforge.net/docs/user/rst/quickref.html)
