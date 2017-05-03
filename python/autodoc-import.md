# sphinx-autodoc ImportError: No module named foo

```
> make html
sphinx-build -b html -d _build/doctrees   . _build/html
Running Sphinx v1.5.5
loading pickled environment... done
building [mo]: targets for 0 po files that are out of date
building [html]: targets for 0 source files that are out of date
updating environment: 0 added, 2 changed, 0 removed
reading sources... [100%] accounting.tests
/home/macgregor/Repos/personal/accounting/docs/accounting.rst:17: WARNING: autodoc: failed to import module u'accounting.accounting'; the following exception was raised:
Traceback (most recent call last):
  File "/usr/lib64/python2.7/site-packages/sphinx/ext/autodoc.py", line 551, in import_object
    __import__(self.modname)
ImportError: No module named accounting
```

## Solution
Add the module you are documenting to sys.path in the sphinx conf.py file for
your project. Autodoc actually has to import your python code, so it needs to be
able to find it.

```bash
> tree -L 2
./
├── accounting.py
├── docs/
│   ├── accounting.rst
│   ├── accounting.tests.rst
│   ├── _build/
│   ├── conf.py
│   ├── index.rst
│   ├── Makefile
│   ├── modules.rst
│   ├── _static/
│   └── _templates/
├── __init__.py
├── mapping.py
├── models.py
├── README.md
└── tests/
```

So my project has the documentation root directory in a subdirectory called `docs/`
I tried adding `sys.path.insert(0, os.path.abspath('..'))` to `docs/conf.py` but
that didnt quit get it. Going up two levels did the trick.

Final solution, add the following to `conf.py` (exact path will vary based on your
configuration):
```python
import os
import sys
sys.path.insert(0, os.path.abspath('../..'))
```

## Additional Resources
* [Stack overflow - Sphinx is not able to import anything](http://stackoverflow.com/questions/23531825/sphinx-is-not-able-to-import-anything)
* [another stack overflow](http://stackoverflow.com/questions/10324393/sphinx-build-fail-autodoc-cant-import-find-module)
* [Sphinx build configuration file](http://www.sphinx-doc.org/en/stable/config.html)