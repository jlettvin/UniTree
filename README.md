# Uni Overview## FILES:```local                   Directory with a common resource file from unicode.org.gitignore              Intermediate files to be ignored when running/testingLICENSE                 Gnu V3.0README.md               This fileUniArray.py             Class ndarray emulation to avoid requirementsUniDict.py              Class for maintaining dict elements as class membersUniDigit.py             Class for converting digits between languagesUniDoc.py               Function for accessing function __doc__ stringUniTree.py              Class for treeing, pruning, and canonicalizing words__init__.py             Root of librarytest_UniArray.py        Unit testtest_UniDict.py         Unit testtest_UniDigit.py        Unit testtest_UniDoc.py          Unit testtest_UniTree.py         Unit test```### UniArray (numpy ndarray emulator)* UniArray.py:  * init(shape, value=0):    * shape (just like ndarray)    * value (initialize (default: 0))Generate and provide get/set accessors for a simple multidimensional array.See test_UniArray.py to see permitted operations.### UniDict (enhanced dictionary)* UniDict.py:  * init(**kw)  initializes  * call(**kw)  updatesIngest arbitraty dictionaries (i.e. docopt return value) andstrip out non-alpha characters for keys thenmake those keys available as object member names.See test_UniDict.py to see permitted operations.### UniDigit (digit converter: language-independent and canonicalizing)* UniDigit.py:  * call(codepoint)  returns the associated int digit or -1 if not NdAn efficient multi-language number conversion algorithmUniDigit.py is a library class for building and using a fast lookup tree.It is a demonstration of canonicalizing digits from all supported languages.See test_UniDigit.py to see permitted operations.### UniDoc (functions and methods can verbosely report their __doc__ string)* UniDoc.py:  * UniDoc()  returns __doc__ of caller### UniTree (tree builder: fast canonicalizing word tree splice/prune/search)* UniTree.py:  * init(wordlist)              Insert canonical words into tree  * tree[word]                  Returns True if word is in tree  * tree.word(word, variant)    Inserts variant and canonicalize to word  * tree(word, 'delete')        Deletes word from tree  * tree.delete(word)           Deletes word from treeAn efficient multi-word canonicalizing search algorithmUniTree.py is a library class for building and using a fast lookup tree.It is a demonstration of a technique for rapid tests for presence ofa candidate word in a precompiled set.UniTree converts a list of words into a fast lookup tree on Unicode codepointsand provides a function for using that tree to lookup words.Words are added by an functor which allows dynamic updates,so that words can be added to the tree while it is in use.Words can be deleted and variants on words may lead to a canonical word.In threaded operation (other languages) this operation will need mutexes.See test_UniTree.py to see permitted operations.# Details## UniTree: Consider the words:```python[hello, hi, hola, hold, hole, work, world]```### UniTree.py builds this dictionary tree structure (except with codepoints):```pythontree = {    u'h': {u'e': {u'l': {u'l': {u'o': {'$': set([u'hello'])}}}},           u'i': {'$': set([u'hi'])},           u'o': {u'l': {u'a': {'$': set([u'hola'])},                         u'd': {'$': set([u'hold'])},                         u'e': {'$': set([u'hole'])}}}},    u'w': {u'o': {u'r': {u'k': {'$': set([u'work'])},                         u'l': {u'd': {'$': set([u'world'])}}}}}}```### This simplified Python 2.7 code identifies treed words quickly:```pythondef find(word, tree):    temp = tree    for c in list(word):        temp = temp.get(c, None)        if not temp:            break    return (temp and {} == temp.get('$', None)) or False```### PerformanceIt should be obvious that this is not highly efficient written in Python.This code, rewritten in C or C++ should be wicked fast.Maybe java could compete.Javascript JIT compiler may also do a good job.But the structure can be turned into indexing rather than hashingfor performance close to one or only a few Intel instructions per letter.Also, using unicode codepoints rather than letters is likely preferred.## UniDigit: encapsulate latest Unicode Nd digit translations### UniDigit.py builds this table for ASCII only```pythontable = [[0, 0, 0, 0, 0, 0, 0, 0], [1, 1, 1, 1, 1, 1, 1, 1], [2, 2, 2, 2, 2, 2, 2, 2], [3, 3, 3, 3, 3, 3, 3, 3], [4, 4, 4, 4, 4, 4, 4, 4], [5, 5, 5, 5, 5, 5, 5, 5], [6, 6, 6, 6, 6, 6, 6, 6], [7, 7, 7, 7, 7, 7, 7, 7], [8, 8, 8, 8, 8, 8, 8, 8], [9, 9, 9, 9, 9, 9, 9, 9], [11, -1, -1, -1, -1, -1, -1, -1], [12, -1, -1, -1, -1, -1, -1, -1], [13, -1, -1, -1, -1, -1, -1, -1], [14, -1, -1, -1, -1, -1, -1, -1], [15, -1, -1, -1, -1, -1, -1, -1], [-1, -1, -1, -1, -1, -1, 16, 17], [0, 1, 2, 3, 4, 5, 6, 7], [8, 9, -1, -1, -1, -1, -1, -1]]```### UniDigit.py uses this code to access the tableGiven a codepoint, this code traverses the table and producesa valid digit between 0:9 if the codepoint is validor -1 if the codepoint is invalid.The tree is much larger when populated with digits from all languages.For the particular value of bits of 3, this produces a consistent7 dereferences with no branches to convert a codepoint to a digitregardless of how many language digits are employed.```pythondef toDigit(c):  # c is a codepoint ordinal    codepointbits = 21    base = 10    bits = 3    enum = 1 << bits    mask = enum - 1    need = codepointbits / bits    need += int(codepointbits % bits)    cuts = [(c>>(shft*self.bits)) & self.mask for shft in range(self.need)]    cuts.reverse()    for segment in cuts:        this = self[this][segment]        if this < base:            return this    return digit```# FutureParsing-------The functor is targeted for modification such thateach keyword is associated with a trigger functionwhich is installed in the tree structure at word terminals.For walking the parse tree after it is built,execution of those functions is an alternative to trigger execution.