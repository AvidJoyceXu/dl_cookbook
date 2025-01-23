# Common attributes in NumPy APIs
## `axis` and `keepdims`
- https://numpy.org/devdocs/reference/generated/numpy.mean.html#numpy.mean
```Python
numpy.mean(a, axis=None, dtype=None, out=None, keepdims=<no value>, *, where=<no value>)
```
> axis: None or int or tuple of ints, optional

Axis or axes along which the means are computed. The default is to compute the mean of the flattened array.

If this is a tuple of ints, a mean is performed over multiple axes, instead of a single axis or all the axes as before.

> keepdims: bool, optional

If this is set to True, the axes which are **reduced** are left in the result as dimensions with size one. With this option, the result will broadcast correctly against the input array.