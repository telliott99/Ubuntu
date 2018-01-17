The editors universally available on Unix are vi, pico and emacs.  [Here](https://earthsci.stanford.edu/computing/unix/editing/editorchoices.php) is a bit of description.  emacs is a complicated beast.

Note:  we use these editors rather than TextEditor mostly because you are warned to do so when editing ``conf`` files.  I don't know if it really matters.  Invoke ``vi`` like this:

```
sudo vi sshd_config
```

#### vi

[Notes](https://earthsci.stanford.edu/computing/unix/editing/vi.php).

Basic vi commands [here](https://www.cs.colostate.edu/helpdocs/vi.html).

You start out in command mode.  In this mode, you can navigate.  Sometimes the arrow keys work, sometimes not.  If not, these keys move the cursor (as given):

- j (down)
- k (up)
- l (right)
- h (left)
- 0 (beginning of current line)
- $ (end of current line)

- i    insert text before cursor
- a    append text after cursor
- x    delete single character under cursor
- dd   delete entire line

``<esc>`` gets you out of edit mode.

Commit your edit with ``:wq``.  Use ``cat`` to check your edit.  To discard edit do ``:q``.

#### pico

An advantage of pico is the possible commands are so few they can be listed on the edit screen so you don't have to remember anything.