# nbdev_all_demo
> Demoing the issue at https://github.com/fastai/nbdev/issues/328


## Summary of the issue

I've created two nearly identical notebooks: `m1` and `m1_copy`, which both import the math module as `math1` and `math2` respectively, and 'exports' them by default (via \_all_).

The only catch is:
- First I ran nbdev_build_lib for both, which generated `m1.py` and `m1_copy.py` respectively
- In `m1_copy.py`, I added an extra print statement and then ran `nbdev_upload_lib`

This added the print statement in `m1_copy.ipynb`, but it also incorrectly replaced the `_all_ = ['math2']` line with `#nbdev_comment _all_ = ['math2']`. This is likely happening because when `nbdev_upload_lib` parses `m1_copy.py`, it doesn't treat `#nbdev_comment` comments specially.

So, on running `nbdev_build_lib` again (say, I changed some random notebook), the `__all__` variable in `m1_copy.py` becomes `[]`, as there is no (uncommented) `_all_ = ['math2']` statement in the notebook.

So now, when I try to use `math2` after importing `m1_copy`, it gives an error

```python
from nbdev_all_demo.m1_copy import *
```

    m1_copy loaded, pi= 3.141592653589793
    This is added directly in m1_copy.py
    

```python
math2.pi
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-4-e78faf57ed6d> in <module>
    ----> 1 math2.pi
    

    NameError: name 'math2' is not defined

