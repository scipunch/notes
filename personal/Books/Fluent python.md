## Links to checkout
- [ ] https://github.com/gvanrossum/patma
- [ ] https://norvig.com/lispy.html
- [ ] https://pythontutor.com/
- [ ] https://github.com/fluentpython/example-code-2e/blob/master/02-array-seq/listcomp_speed.py
- [ ] https://docs.python.org/3/library/dis.html
- [ ] https://www.fluentpython.com/extra/ordered-sequences-with-bisect/
- [ ] https://www.fluentpython.com/extra/parsing-binary-struct/
- [ ] https://nedbatchelder.com/blog/202006/pickles_nine_flaws.html
- [ ] https://www.fluentpython.com/extra/internals-of-sets-and-dicts/
- [ ] https://peps.python.org/pep-0412/

## Notes

| Page | Note                                                                                                                                                                                                                                                                                                                                                                                             |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 15   | Since Python 3.7, the dict type is officially “ordered,” but that only<br>means that the key insertion order is preserved. You cannot<br>rearrange the keys in a dict however you like.                                                                                                                                                                                                          |
| 30   | In general, using _ as a dummy variable is just a convention. It’s<br>just a strange but valid variable name. However, in a match/case<br>statement, _ is a wildcard that matches any value but is not bound<br>to a value. See “Pattern Matching with Sequences” on page 38. And<br>in the Python console, the result of the preceding command is<br>assigned to _ — unless the result is None. |
| 32   | But if one of<br>those references points to a mutable object, and that object is changed, then the value<br>of the tuple changes                                                                                                                                                                                                                                                                 |
| 42   | `case [str(name), _, _, (float(lat), float(lon))]:` - runtime type check in match statement                                                                                                                                                                                                                                                                                                      |
| 46   | ![[Pasted image 20240912170903.png]]                                                                                                                                                                                                                                                                                                                                                             |
| 55   | ![[Pasted image 20240912175657.png]]                                                                                                                                                                                                                                                                                                                                                             |
| 84   | An object is hashable if it has a hash code which never changes during its lifetime (it<br>needs a __hash__() method), and can be compared to other objects (it needs an<br>__eq__() method). Hashable objects which compare equal must have the same hash<br>code.                                                                                                                              |
| 106  | Python runs a specialized BUILD_SET bytecode                                                                                                                                                                                                                                                                                                                                                     |
