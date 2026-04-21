# How do I check whether a file exists without exceptions?

> Stack Overflow Question [#82831](https://stackoverflow.com/q/82831) | 6793 票

---

<p>If the reason you're checking is so you can do something like <code>if file_exists: open_it()</code>, it's safer to use a <code>try</code> around the attempt to open it. Checking and then opening risks the file being deleted or moved or something between when you check and when you try to open it.</p>
<p>If you're not planning to open the file immediately, you can use <a href="https://docs.python.org/library/os.path.html#os.path.isfile" rel="noreferrer"><code>os.path.isfile</code></a> if you need to be sure it's a file.</p>
<blockquote>
<p>Return <code>True</code> if path is an existing regular file. This follows symbolic links, so both <a href="https://docs.python.org/library/os.path.html#os.path.islink" rel="noreferrer">islink()</a> and <a href="https://docs.python.org/library/os.path.html#os.path.isfile" rel="noreferrer">isfile()</a> can be true for the same path.</p>
</blockquote>
<pre><code>import os.path
os.path.isfile(fname)
</code></pre>
<h3><code>pathlib</code></h3>
<p>Starting with Python 3.4, the <a href="https://docs.python.org/3/library/pathlib.html#pathlib.Path.is_file" rel="noreferrer"><code>pathlib</code> module</a> offers an object-oriented approach (backported to <code>pathlib2</code> in Python 2.7):</p>
<pre><code>from pathlib import Path

my_file = Path("/path/to/file")
if my_file.is_file():
    # file exists
</code></pre>
<p>To check a directory, do:</p>
<pre><code>if my_file.is_dir():
    # directory exists
</code></pre>
<p>To check whether a <code>Path</code> object exists independently of whether is it a file or directory, use <code>exists()</code>:</p>
<pre><code>if my_file.exists():
    # path exists
</code></pre>
<p>You can also use <code>resolve(strict=True)</code> in a <code>try</code> block:</p>
<pre><code>try:
    my_abs_path = my_file.resolve(strict=True)
except FileNotFoundError:
    # doesn't exist
else:
    # exists
</code></pre>


---

**相关链接**：
- [原问题](https://stackoverflow.com/q/82831)
- [最佳答案](https://stackoverflow.com/a/82852)

---

*内容整理自 Stack Overflow，采用 CC BY-SA 协议。*
