# What does the "yield" keyword do in Python?

> Stack Overflow Question [#231767](https://stackoverflow.com/q/231767) | 18307 票

---

<p>To understand what <a href="https://docs.python.org/3/reference/simple_stmts.html#yield" rel="noreferrer"><code>yield</code></a> does, you must understand what <em><a href="https://docs.python.org/3/glossary.html#term-generator" rel="noreferrer">generators</a></em> are. And before you can understand generators, you must understand <em><a href="https://docs.python.org/3/glossary.html#term-iterable" rel="noreferrer">iterables</a></em>.</p>
<h2>Iterables</h2>
<p>When you create a list, you can read its items one by one. Reading its items one by one is called iteration:</p>
<pre><code>>>> mylist = [1, 2, 3]
>>> for i in mylist:
...    print(i)
1
2
3
</code></pre>
<p><code>mylist</code> is an <em>iterable</em>. When you use a list comprehension, you create a list, and so an iterable:</p>
<pre><code>>>> mylist = [x*x for x in range(3)]
>>> for i in mylist:
...    print(i)
0
1
4
</code></pre>
<p>Everything you can use "<code>for... in...</code>" on is an iterable; <code>lists</code>, <code>strings</code>, files...</p>
<p>These iterables are handy because you can read them as much as you wish, but you store all the values in memory and this is not always what you want when you have a lot of values.</p>
<h2>Generators</h2>
<p>Generators are <em><a href="https://docs.python.org/3/glossary.html#term-iterator" rel="noreferrer">iterators</a></em>, a kind of iterable <strong>you can only iterate over once</strong>. Generators do not store all the values in memory, <strong>they generate the values on the fly</strong>:</p>
<pre><code>>>> mygenerator = (x*x for x in range(3))
>>> for i in mygenerator:
...    print(i)
0
1
4
</code></pre>
<p>It is just the same except you used <code>()</code> instead of <code>[]</code>. BUT, you <strong>cannot</strong> perform <code>for i in mygenerator</code> a second time since generators can only be used once: they calculate 0, then forget about it and calculate 1, and end after calculating 4, one by one.</p>
<h2>Yield</h2>
<p><code>yield</code> is a keyword that is used like <code>return</code>, except the function will return a generator.</p>
<pre><code>>>> def create_generator():
...    mylist = range(3)
...    for i in mylist:
...        yield i*i
...
>>> mygenerator = create_generator() # create a generator
>>> print(mygenerator) # mygenerator is an object!
<generator object create_generator at 0xb7555c34>
>>> for i in mygenerator:
...     print(i)
0
1
4
</code></pre>
<p>Here it's a useless example, but it's handy when you know your function will return a huge set of values that you will only need to read once.</p>
<p>To master <code>yield</code>, you must understand that <strong>when you call the function, the code you have written in the function body does not run.</strong> The function only returns the generator object, this is a bit tricky.</p>
<p>Then, your code will continue from where it left off each time <code>for</code> uses the generator.</p>
<p>Now the hard part:</p>
<p>The first time the <code>for</code> calls the generator object created from your function, it will run the code in your function from the beginning until it hits <code>yield</code>, then it'll return the first value of the loop. Then, each subsequent call will run another iteration of the loop you have written in the function and return the next value. This will continue until the generator is considered empty, which happens when the function runs without hitting <code>yield</code>. That can be because the loop has come to an end, or because you no longer satisfy an <code>"if/else"</code>.</p>
<hr />
<h2>Your code explained</h2>
<p><em>Generator:</em></p>
<pre><code># Here you create the method of the node object that will return the generator
def _get_child_candidates(self, distance, min_dist, max_dist):

    # Here is the code that will be called each time you use the generator object:

    # If there is still a child of the node object on its left
    # AND if the distance is ok, return the next child
    if self._leftchild and distance - max_dist < self._median:
        yield self._leftchild

    # If there is still a child of the node object on its right
    # AND if the distance is ok, return the next child
    if self._rightchild and distance + max_dist >= self._median:
        yield self._rightchild

    # If the function arrives here, the generator will be considered empty
    # There are no more than two values: the left and the right children
</code></pre>
<p><em>Caller:</em></p>
<pre><code># Create an empty list and a list with the current object reference
result, candidates = list(), [self]

# Loop on candidates (they contain only one element at the beginning)
while candidates:

    # Get the last candidate and remove it from the list
    node = candidates.pop()

    # Get the distance between obj and the candidate
    distance = node._get_dist(obj)

    # If the distance is ok, then you can fill in the result
    if distance <= max_dist and distance >= min_dist:
        result.extend(node._values)

    # Add the children of the candidate to the candidate's list
    # so the loop will keep running until it has looked
    # at all the children of the children of the children, etc. of the candidate
    candidates.extend(node._get_child_candidates(distance, min_dist, max_dist))

return result
</code></pre>
<p>This code contains several smart parts:</p>
<ul>
<li><p>The loop iterates on a list, but the list expands while the loop is being iterated. It's a concise way to go through all these nested data even if it's a bit dangerous since you can end up with an infinite loop. In this case, <code>candidates.extend(node._get_child_candidates(distance, min_dist, max_dist))</code> exhausts all the values of the generator, but <code>while</code> keeps creating new generator objects which will produce different values from the previous ones since it's not applied on the same node.</p>
</li>
<li><p>The <code>extend()</code> method is a list object method that expects an iterable and adds its values to the list.</p>
</li>
</ul>
<p>Usually, we pass a list to it:</p>
<pre><code>>>> a = [1, 2]
>>> b = [3, 4]
>>> a.extend(b)
>>> print(a)
[1, 2, 3, 4]
</code></pre>
<p>But in your code, it gets a generator, which is good because:</p>
<ol>
<li>You don't need to read the values twice.</li>
<li>You may have a lot of children and you don't want them all stored in memory.</li>
</ol>
<p>And it works because Python does not care if the argument of a method is a list or not. Python expects iterables so it will work with strings, lists, tuples, and generators! This is called duck typing and is one of the reasons why Python is so cool. But this is another story, for another question...</p>
<p>You can stop here, or read a little bit to see an advanced use of a generator:</p>
<h2>Controlling a generator exhaustion</h2>
<pre><code>>>> class Bank(): # Let's create a bank, building ATMs
...    crisis = False
...    def create_atm(self):
...        while not self.crisis:
...            yield "$100"
>>> hsbc = Bank() # When everything's ok the ATM gives you as much as you want
>>> corner_street_atm = hsbc.create_atm()
>>> print(corner_street_atm.next())
$100
>>> print(corner_street_atm.next())
$100
>>> print([corner_street_atm.next() for cash in range(5)])
['$100', '$100', '$100', '$100', '$100']
>>> hsbc.crisis = True # Crisis is coming, no more money!
>>> print(corner_street_atm.next())
<type 'exceptions.StopIteration'>
>>> wall_street_atm = hsbc.create_atm() # It's even true for new ATMs
>>> print(wall_street_atm.next())
<type 'exceptions.StopIteration'>
>>> hsbc.crisis = False # The trouble is, even post-crisis the ATM remains empty
>>> print(corner_street_atm.next())
<type 'exceptions.StopIteration'>
>>> brand_new_atm = hsbc.create_atm() # Build a new one to get back in business
>>> for cash in brand_new_atm:
...    print cash
$100
$100
$100
$100
$100
$100
$100
$100
$100
...
</code></pre>
<p><strong>Note:</strong> For Python 3, use<code>print(corner_street_atm.__next__())</code> or <code>print(next(corner_street_atm))</code></p>
<p>It can be useful for various things like controlling access to a resource.</p>
<h2>Itertools, your best friend</h2>
<p>The <code>itertools</code> module contains special functions to manipulate iterables. Ever wish to duplicate a generator?
Chain two generators? Group values in a nested list with a one-liner? <code>Map / Zip</code> without creating another list?</p>
<p>Then just <code>import itertools</code>.</p>
<p>An example? Let's see the possible orders of arrival for a four-horse race:</p>
<pre><code>>>> horses = [1, 2, 3, 4]
>>> races = itertools.permutations(horses)
>>> print(races)
<itertools.permutations object at 0xb754f1dc>
>>> print(list(itertools.permutations(horses)))
[(1, 2, 3, 4),
 (1, 2, 4, 3),
 (1, 3, 2, 4),
 (1, 3, 4, 2),
 (1, 4, 2, 3),
 (1, 4, 3, 2),
 (2, 1, 3, 4),
 (2, 1, 4, 3),
 (2, 3, 1, 4),
 (2, 3, 4, 1),
 (2, 4, 1, 3),
 (2, 4, 3, 1),
 (3, 1, 2, 4),
 (3, 1, 4, 2),
 (3, 2, 1, 4),
 (3, 2, 4, 1),
 (3, 4, 1, 2),
 (3, 4, 2, 1),
 (4, 1, 2, 3),
 (4, 1, 3, 2),
 (4, 2, 1, 3),
 (4, 2, 3, 1),
 (4, 3, 1, 2),
 (4, 3, 2, 1)]
</code></pre>
<h2>Understanding the inner mechanisms of iteration</h2>
<p>Iteration is a process implying iterables (implementing the <code>__iter__()</code> method) and iterators (implementing the <code>__next__()</code> method).
Iterables are any objects you can get an iterator from. Iterators are objects that let you iterate on iterables.</p>
<p>There is more about it in this article about <a href="https://web.archive.org/web/20201109034340/http://effbot.org/zone/python-for-statement.htm" rel="noreferrer">how <code>for</code> loops work</a>.</p>


---

**相关链接**：
- [原问题](https://stackoverflow.com/q/231767)
- [最佳答案](https://stackoverflow.com/a/231855)

---

*内容整理自 Stack Overflow，采用 CC BY-SA 协议。*
