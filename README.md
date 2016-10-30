# TreeFindTreeFind.py is a library function for building and using a fast lookup tree.TreeFind converts a list of words into a fast lookup treeand provides a function for using that tree to lookup words.Words are added by an ingest function which allows dynamic updatesmeaning words can be added to the tree while it is operating.In threaded operation (other languages) this operation will need mutexes.# Consider this word file:```hellohiholaholdholeworkworld```# TreeFind.py build this structure:```tree = { u'h': {u'e': {u'l': {u'l': {u'o': {'$': u'hello'}}}},        u'i': {'$': u'hi'},        u'o': {u'l': {u'a': {'$': u'hola'},                      u'd': {'$': u'hold'},                      u'e': {'$': u'hole'}}}}, u'w': {u'o': {u'r': {u'k': {'$': u'work'},                      u'l': {u'd': {'$': u'world'}}}}}}```# This Python 2.7 code finds words quickly:```def find(word):    temp = self    for i, c in enumerate(list(word)):        temp = temp.get(c, None)        if not temp:            break    return (temp and {} == temp.get('$', None)) or False```