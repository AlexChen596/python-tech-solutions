# What is the difference between @staticmethod and @classmethod in Python?

> Stack Overflow Question [#136097](https://stackoverflow.com/q/136097) | 4791 票 | 最佳答案 4021 票

> ⚠️ **时效性说明**：此问题创建于多年前，但Python核心语法变化不大，方案仍有效。部分API可能在最新版本中有更现代的写法。

---

<p>Maybe a bit of example code will help: Notice the difference in the call signatures of <code>foo</code>, <code>class_foo</code> and <code>static_foo</code>:</p>
<pre><code>class A(object):
    def foo(self, x):
        print(f&quot;executing foo({self}, {x})&quot;)

    @classmethod
    def class_foo(cls, x):
        print(f&quot;executing class_foo({cls}, {x})&quot;)

    @staticmethod
    def static_foo(x):
        print(f&quot;executing static_foo({x})&quot;)

a = A()
</code></pre>
<p>Below is the usual way an object instance calls a method. The object instance, <code>a</code>, is implicitly passed as the first argument.</p>
<pre><code>a.foo(1)
# executing foo(&lt;__main__.A object at 0xb7dbef0c&gt;, 1)
</code></pre>
<hr />
<p><strong>With classmethods</strong>, the class of the object instance is implicitly passed as the first argument instead of <code>self</code>.</p>
<pre><code>a.class_foo(1)
# executing class_foo(&lt;class '__main__.A'&gt;, 1)
</code></pre>
<p>You can also call <code>class_foo</code> using the class. In fact, if you define something to be
a classmethod, it is probably because you intend to call it from the class rather than from a class instance. <code>A.foo(1)</code> would have raised a TypeError, but <code>A.class_foo(1)</code> works just fine:</p>
<pre><code>A.class_foo(1)
# executing class_foo(&lt;class '__main__.A'&gt;, 1)
</code></pre>
<p>One use people have found for class methods is to create <a href="https://stackoverflow.com/a/1950927/190597">inheritable alternative constructors</a>.</p>
<hr />
<p><strong>With staticmethods</strong>, neither <code>self</code> (the object instance) nor  <code>cls</code> (the class) is implicitly passed as the first argument. They behave like plain functions except that you can call them from an instance or the class:</p>
<pre><code>a.static_foo(1)
# executing static_foo(1)

A.static_foo('hi')
# executing static_foo(hi)
</code></pre>
<p>Staticmethods are used to group functions which have some logical connection with a class to the class.</p>
<hr />
<p><code>foo</code> is just a function, but when you call <code>a.foo</code> you don't just get the function,
you get a &quot;partially applied&quot; version of the function with the object instance <code>a</code> bound as the first argument to the function. <code>foo</code> expects 2 arguments, while <code>a.foo</code> only expects 1 argument.</p>
<p><code>a</code> is bound to <code>foo</code>. That is what is meant by the term &quot;bound&quot; below:</p>
<pre><code>print(a.foo)
# &lt;bound method A.foo of &lt;__main__.A object at 0xb7d52f0c&gt;&gt;
</code></pre>
<p>With <code>a.class_foo</code>, <code>a</code> is not bound to <code>class_foo</code>, rather the class <code>A</code> is bound to <code>class_foo</code>.</p>
<pre><code>print(a.class_foo)
# &lt;bound method type.class_foo of &lt;class '__main__.A'&gt;&gt;
</code></pre>
<p>Here, with a staticmethod, even though it is a method, <code>a.static_foo</code> just returns
a good 'ole function with no arguments bound. <code>static_foo</code> expects 1 argument, and
<code>a.static_foo</code> expects 1 argument too.</p>
<pre><code>print(a.static_foo)
# &lt;function static_foo at 0xb7d479cc&gt;
</code></pre>
<p>And of course the same thing happens when you call <code>static_foo</code> with the class <code>A</code> instead.</p>
<pre><code>print(A.static_foo)
# &lt;function static_foo at 0xb7d479cc&gt;
</code></pre>


---

**相关链接**：
- [原问题](https://stackoverflow.com/q/136097)
- [最佳答案](https://stackoverflow.com/a/136097)

---

*内容整理自 Stack Overflow，采用 CC BY-SA 协议。*
*由 [AlexChen596](https://github.com/AlexChen596) 整理*
