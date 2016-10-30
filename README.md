# TreeFindTreeFind.py is a library function for building and using a fast lookup tree.It is a demonstration of a technique for rapid tests for presence ofa candidate word in a precompiled set.TreeFind converts a list of words into a fast lookup treeand provides a function for using that tree to lookup words.Words are added by an ingest function which allows dynamic updates,so that words can be added to the tree while it is in use.In threaded operation (other languages) this operation will need mutexes.# Consider this word file:```hellohiholaholdholeworkworld```# TreeFind.py build this structure:```tree = {    u'h': {u'e': {u'l': {u'l': {u'o': {'$': u'hello'}}}},           u'i': {'$': u'hi'},           u'o': {u'l': {u'a': {'$': u'hola'},                         u'd': {'$': u'hold'},                         u'e': {'$': u'hole'}}}},    u'w': {u'o': {u'r': {u'k': {'$': u'work'},                         u'l': {u'd': {'$': u'world'}}}}}}```# This Python 2.7 code finds words quickly:```def find(word):    temp = tree    for i, c in list(word):        temp = temp.get(c, None)        if not temp:            break    return (temp and {} == temp.get('$', None)) or False```# FutureIt should be obvious that this is inefficient in Python.This code, rewritten in C or C++ should be wicked fast.Maybe java could compete.Javascript JIT compiler may also do a good job.But the structure can be turned into indexing rather than hashingfor performance close to one or only a few Intel instructions per letter.Also, using unicode codepoints rather than letters is likely preferred.