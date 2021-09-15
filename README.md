# Django REST framework 3.12.4 (version without OrderedDict's)

Full documentation for the project is available at https://www.django-rest-framework.org/.


# Requirements

* Python (3.6, 3.7, 3.8, 3.9)
* Django (3.0, 3.1, 3.2)


dict is now assumed to be ordered in all currently supported versions of Python.


Preserve order and remove duplicates via fromkeys() (e.g. line 334 of openapi.py)
```python
%timeit list(dict.fromkeys(field.choices))
```
553 ns ± 1.97 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)

```python
 %timeit list(OrderedDict.fromkeys(field.choices))
```
680 ns ± 5.24 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
Conclusion dict is 19% faster

Create a dict from a list
(e.g line 221 of pagination.py)
```python
%timeit Response(OrderedDict([('count', self.page.paginator.count), ('next', self.get_next_link()), ('previous', self.get_previous_link()), ('results', data)]))
```
53.2 µs ± 204 ns per loop (mean ± std. dev. of 7 runs, 10000 loops each)


```python
%timeit Response({'count': self.page.paginator.count, 'next': self.get_next_link(), 'previous': self.get_previous_link(), 'results': data,})
```
52.1 µs ± 473 ns per loop (mean ± std. dev. of 7 runs, 10000 loops each)
(e.g line 660 in renderers.py)

```python
%timeit -r20 OrderedDict(sorted(response.items()))
```
900 ns ± 9.79 ns per loop (mean ± std. dev. of 20 runs, 1000000 loops each)

```python
%timeit -r20 dict(sorted(response.items()))
```
684 ns ± 3.59 ns per loop (mean ± std. dev. of 20 runs, 1000000 loops each)
Conclusion dict faster, 2% in the first example, 25% in the second. I suspect in practice the benefit is closer to the first; however, it is consistently faster.

Adding key,values to a dict (e.g. line 62 or model_meta.py)
# OrderedDict()
```python
%timeit for field in [field for field in opts.fields if field.serialize and not field.remote_field]: fields[field.name] = field
  ...:     
 ```
517 ns ± 4.83 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)

# dict
```python
%timeit for field in [field for field in opts.fields if field.serialize and not field.remote_field]: fields[field.name] = field
   ...:     
   ```
500 ns ± 4.1 ns per loop (mean ± std. dev. of 7 runs, 1000000 loops each)
Conclusion dict is marginally (3%) faster
