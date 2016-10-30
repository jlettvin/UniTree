# TreeFindTreeFind.py is a library function for building and using a fast lookup tree.It is a demonstration of a technique for rapid tests for presence ofa candidate word in a precompiled set.TreeFind converts a list of words into a fast lookup treeand provides a function for using that tree to lookup words.Words are added by an ingest function which allows dynamic updates,so that words can be added to the tree while it is in use.In threaded operation (other languages) this operation will need mutexes.# Consider this word file:```hellohiholaholdholeworkworld```# TreeFind.py builds this dictionary tree structure:```tree = {    u'h': {u'e': {u'l': {u'l': {u'o': {'$': u'hello'}}}},           u'i': {'$': u'hi'},           u'o': {u'l': {u'a': {'$': u'hola'},                         u'd': {'$': u'hold'},                         u'e': {'$': u'hole'}}}},    u'w': {u'o': {u'r': {u'k': {'$': u'work'},                         u'l': {u'd': {'$': u'world'}}}}}}```# This Python 2.7 code finds words quickly:```def find(word, tree):    temp = tree    for c in list(word):        temp = temp.get(c, None)        if not temp:            break    return (temp and {} == temp.get('$', None)) or False```# TreeFind.py usage:```TreeFind.pyUsage:    TreeFind.py [-inrst] [-w WORDS] [-v] [FILE...]    TreeFind.py (-h | --help)    TreeFind.py (--version)Options:    -i --ignorecase                 Caseless comparison    -n --noJSON                     Use ingest words without backstore    -r --renewJSON                  Generate new JSON backstore    -s --showtree                   Show args and tree using pretty print    -t --test                       Run tests    -v --verbose                    Show verbose output    -w WORDS --words=WORDS          Source [default: /usr/share/dict/words]    -h --help                       Show this Usage message    --version                       Show versionConcepts:    Searching for established words is improved by creating    a dictionary tree from which individual character can be    tested until a special token is found meaning terminal ('$').    This application reads from the standard unix word source    /usr/share/dict/words    from which it constructs the dictionary and then stores it    as a .json file which is faster to dump and load than cPickle.Unit tests include:    1. reporting non-matches from the ASCII in this python script.       NOTE: some acceptable English words are not in the words dictionary.    2. reporting no output when searching the words dictionary.Examples:    ./TreeFind.py -w testwords -i -n -s TreeFind.py  # local only, show tree    Given a file 'testwords' with the following contents:hellohiholaholdholeworkworld    the output would finish with:{'arg': {'FILE': ['TreeFind.py'],         'haveJSON': True,         'help': False,         'ignorecase': True,         'noJSON': True,         'renewJSON': False,         'showtree': True,         'test': False,         'verbose': False,         'version': False,         'words': 'testwords'}, u'h': {u'e': {u'l': {u'l': {u'o': {'$': u'hello'}}}},        u'i': {'$': u'hi'},        u'o': {u'l': {u'a': {'$': u'hola'},                      u'd': {'$': u'hold'},                      u'e': {'$': u'hole'}}}}, u'w': {u'o': {u'r': {u'k': {'$': u'work'}, u'l': {u'd': {'$': u'world'}}}}}}Author  : Jonathan D. Lettvin (jlettvin@gmail.com)Date    : 20161029 Legal   : Copyright(c) Jonathan D. Lettvin, All Rights ReservedLicense : GPL 3.0```# FutureIt should be obvious that this is inefficient in Python.This code, rewritten in C or C++ should be wicked fast.Maybe java could compete.Javascript JIT compiler may also do a good job.But the structure can be turned into indexing rather than hashingfor performance close to one or only a few Intel instructions per letter.Also, using unicode codepoints rather than letters is likely preferred.