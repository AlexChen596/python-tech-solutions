# Does Python have a ternary conditional operator?

> Stack Overflow Question [#394809](https://stackoverflow.com/q/394809) | 9355 票

---

<p>Yes, it was <a href="https://mail.python.org/pipermail/python-dev/2005-September/056846.html" rel="noreferrer" title="[Python-Dev] Conditional Expression Resolution">added</a> in version 2.5. The expression syntax is:</p>
<pre class="lang-py prettyprint-override"><code>a if condition else b
</code></pre>
<p>First <code>condition</code> is evaluated, then exactly one of either <code>a</code> or <code>b</code> is evaluated and returned based on the <a href="https://en.wikipedia.org/wiki/Boolean_data_type" rel="noreferrer" title="Boolean data type">Boolean</a> value of <code>condition</code>. If <code>condition</code> evaluates to <code>True</code>, then <code>a</code> is evaluated and returned but <code>b</code> is ignored, or else when <code>b</code> is evaluated and returned but <code>a</code> is ignored.</p>
<p>This allows short-circuiting because when <code>condition</code> is true only <code>a</code> is evaluated and <code>b</code> is not evaluated at all, but when <code>condition</code> is false only <code>b</code> is evaluated and <code>a</code> is not evaluated at all.</p>
<p>For example:</p>
<pre class="lang-py prettyprint-override"><code>>>> 'true' if True else 'false'
'true'
>>> 'true' if False else 'false'
'false'
</code></pre>
<p>Note that conditionals are an <em>expression</em>, not a <em>statement</em>. This means you can't use <strong>statements</strong> such as <code>pass</code>, or assignments with <code>=</code> (or "augmented" assignments like <code>+=</code>), within a conditional <strong>expression</strong>:</p>
<pre class="lang-py prettyprint-override"><code>>>> pass if False else pass
  File "<stdin>", line 1
    pass if False else pass
         ^
SyntaxError: invalid syntax

>>> # Python parses this as `x = (1 if False else y) = 2`
>>> # The `(1 if False else x)` part is actually valid, but
>>> # it can't be on the left-hand side of `=`.
>>> x = 1 if False else y = 2
  File "<stdin>", line 1
SyntaxError: cannot assign to conditional expression

>>> # If we parenthesize it instead...
>>> (x = 1) if False else (y = 2)
  File "<stdin>", line 1
    (x = 1) if False else (y = 2)
       ^
SyntaxError: invalid syntax
</code></pre>
<p>(In 3.8 and above, the <code>:=</code> "walrus" operator allows simple assignment of values <em>as an expression</em>, which is then compatible with this syntax. But please don't write code like that; it will quickly become very difficult to understand.)</p>
<p>Similarly, because it is an expression, the <code>else</code> part is <em>mandatory</em>:</p>
<pre><code># Invalid syntax: we didn't specify what the value should be if the 
# condition isn't met. It doesn't matter if we can verify that
# ahead of time.
a if True
</code></pre>
<p>You can, however, use conditional expressions to assign a variable like so:</p>
<pre class="lang-py prettyprint-override"><code>x = a if True else b
</code></pre>
<p>Or for example to return a value:</p>
<pre><code># Of course we should just use the standard library `max`;
# this is just for demonstration purposes.
def my_max(a, b):
    return a if a > b else b
</code></pre>
<p>Think of the conditional expression as switching between two values. We can use it when we are in a 'one value or another' situation, where we will <em>do the same thing</em> with the result, regardless of whether the condition is met. We use the expression to compute the value, and then do something with it. If you need to <em>do something different</em> depending on the condition, then use a normal <code>if</code> <strong>statement</strong> instead.</p>
<hr />
<p>Keep in mind that it's frowned upon by some Pythonistas for several reasons:</p>
<ul>
<li>The order of the arguments is different from those of the classic <code>condition ? a : b</code> ternary operator from many other languages (such as <a href="https://en.wikipedia.org/wiki/C_%28programming_language%29" rel="noreferrer">C</a>, <a href="https://en.wikipedia.org/wiki/C%2B%2B" rel="noreferrer">C++</a>, <a href="https://en.wikipedia.org/wiki/Go_%28programming_language%29" rel="noreferrer">Go</a>, <a href="https://en.wikipedia.org/wiki/Perl" rel="noreferrer">Perl</a>, <a href="https://en.wikipedia.org/wiki/Ruby_%28programming_language%29" rel="noreferrer">Ruby</a>, <a href="https://en.wikipedia.org/wiki/Java_%28programming_language%29" rel="noreferrer">Java</a>, <a href="https://en.wikipedia.org/wiki/JavaScript" rel="noreferrer">JavaScript</a>, etc.), which may lead to bugs when people unfamiliar with Python's "surprising" behaviour use it (they may reverse the argument order).</li>
<li>Some find it "unwieldy", since it goes contrary to the normal flow of thought (thinking of the condition first and then the effects).</li>
<li>Stylistic reasons. (Although the 'inline <code>if</code>' can be <em>really</em> useful, and make your script more concise, it really does complicate your code)</li>
</ul>
<p>If you're having trouble remembering the order, then remember that when read aloud, you (almost) say what you mean. For example, <code>x = 4 if b > 8 else 9</code> is read aloud as <code>x will be 4 if b is greater than 8 otherwise 9</code>.</p>
<p>Official documentation:</p>
<ul>
<li><a href="https://docs.python.org/3/reference/expressions.html#conditional-expressions" rel="noreferrer" title="Conditional expressions">Conditional expressions</a></li>
<li><a href="https://docs.python.org/3/faq/programming.html#is-there-an-equivalent-of-c-s-ternary-operator" rel="noreferrer" title="Is there an equivalent of C’s ”?:” ternary operator?">Is there an equivalent of C’s ”?:” ternary operator?</a></li>
</ul>


---

**相关链接**：
- [原问题](https://stackoverflow.com/q/394809)
- [最佳答案](https://stackoverflow.com/a/394814)

---

*内容整理自 Stack Overflow，采用 CC BY-SA 协议。*
