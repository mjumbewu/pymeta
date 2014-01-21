PyMeta
========

A Pattern-Matching Language Based on Python

Installation
------------

This is a custom fork of pymeta 0.5.0 that supports python 3. To install, execute:

```bash
pip install git+git://github.com/wbond/pymeta.git
```

Summary
-------

PyMeta is an implementation of OMeta, an object-oriented pattern-matching
language developed by Alessandro Warth
(http://www.cs.ucla.edu/~awarth/ometa/). PyMeta provides a compact syntax based
on Parsing Expression Grammars (PEGs) for common lexing, parsing and
tree-transforming activities in a way that's easy to reason about for Python
programmers.


How It Works
------------

PyMeta compiles a grammar to a Python class, with the rules as methods. The
rules specify parsing expressions, which consume input and return values if
they succeed in matching.

### Basic syntax

``foo ::= ....``  
   Define a rule named foo.

``expr1 expr2``  
   Match expr1, and then match expr2 if it succeeds, returning the value of
   expr2. Like Python's ``and``.

``expr1 | expr2``  
  Try to match expr1 --- if it fails, match expr2 instead. Like Python's
  ``or``.

``expr*``  
  Match expr zero or more times, returning a list of matches.

``expr+``  
  Match expr one or more times, returning a list of matches.

``expr?``  
  Try to match expr. Returns None if it fails to match.

``~expr``  
  Fail if the next item in the input matches expr.

``<ruleName>``  
  Call the rule ``ruleName``.

``'x'``  
  Match the literal character 'x'.

``expr:name``  
  Bind the result of expr to the local variable ``name``.

``=> pythonExpression``  
  Evaluate the given Python expression and return its result.

Comments like Python comments are supported as well, starting with #
and extending to the end of the line.

Interface
---------

The starting point for defining a new grammar is
``pymeta.grammar.OMeta.makeGrammar``, which takes a grammar definition and a
dict of variable bindings for its embedded expressions and produces a Python
class. Grammars can be subclassed as usual, and makeGrammar can be called on
these classes to override rules and provide new ones. To invoke a grammar rule,
call ``grammarObject.apply()`` with its name.

Example Usage
-------------

```python
from pymeta.grammar import OMeta
exampleGrammar = """
ones ::= '1' '1' => 1
twos ::= '2' '2' => 2
stuff ::= (<ones> | <twos>)+
"""
Example = OMeta.makeGrammar(exampleGrammar, {})
g = Example("11221111")
result, error = g.apply("stuff")
print(result)
# Outputs [1, 2, 1, 1]
```
