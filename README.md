# mmh3
[![GitHub Super-Linter](https://github.com/hajimes/mmh3/workflows/Super-Linter/badge.svg?branch=master)](https://github.com/hajimes/mmh3/actions?query=workflow%3ASuper-Linter+branch%3Amaster)
[![Build](https://github.com/hajimes/mmh3/actions/workflows/build.yml/badge.svg?branch=master)](https://github.com/hajimes/mmh3/actions/workflows/build.yml?branch=master)
[![PyPi Version](https://img.shields.io/pypi/v/mmh3.svg?style=flat-square&logo=pypi&logoColor=white)](https://pypi.org/project/mmh3/)
[![Python Versions](https://img.shields.io/pypi/pyversions/mmh3.svg)](https://pypi.org/project/mmh3/)
[![License: CC0-1.0](https://img.shields.io/badge/License-CC0%201.0-lightgrey.svg)](http://creativecommons.org/publicdomain/zero/1.0/)
[![Total Downloads](https://pepy.tech/badge/mmh3)](https://pepy.tech/project/mmh3)
[![Recent Downloads](https://pepy.tech/badge/mmh3/month)](https://pepy.tech/project/mmh3)
[![Conda Version](https://img.shields.io/conda/vn/conda-forge/mmh3.svg?style=flat-square&logo=conda-forge&logoColor=white)](https://anaconda.org/conda-forge/mmh3)

mmh3 is a Python wrapper for [MurmurHash (MurmurHash3)](https://en.wikipedia.org/wiki/MurmurHash), a set of fast and robust non-cryptographic hash functions invented by Austin Appleby.

Combined with probabilistic techniques like a [Bloom filter](https://en.wikipedia.org/wiki/Bloom_filter), [MinHash](https://en.wikipedia.org/wiki/MinHash), and [feature hashing](https://en.wikipedia.org/wiki/Feature_hashing), mmh3 allows you to develop high-performance systems in fields such as data mining, machine learning, and natural language processing.

## How to use
Install:
```shell
pip install mmh3 # for macOS, use "pip3 install mmh3" and python3
```

Quickstart:
```shell
>>> import mmh3
>>> mmh3.hash("foo") # returns a 32-bit signed int
-156908512
>>> mmh3.hash("foo", 42) # uses 42 as a seed
-1322301282
>>> mmh3.hash("foo", signed=False) # returns a 32-bit unsigned int
4138058784
```

Other functions:
```shell
>>> mmh3.hash64("foo") # two 64 bit signed ints (by using the 128-bit algorithm as its backend)
(-2129773440516405919, 9128664383759220103)
>>> mmh3.hash64("foo", signed=False) #  two 64 bit unsigned ints
(16316970633193145697, 9128664383759220103)
>>> mmh3.hash128("foo", 42) # 128 bit unsigned int
215966891540331383248189432718888555506
>>> mmh3.hash128("foo", 42, signed=True) # 128 bit signed int
-124315475380607080215185174712879655950
>>> mmh3.hash_bytes("foo") # 128 bit value as bytes
'aE\xf5\x01W\x86q\xe2\x87}\xba+\xe4\x87\xaf~'
>>> import numpy as np
>>> a = np.zeros(2 ** 32, dtype=np.int8)
>>> mmh3.hash_bytes(a)
b'V\x8f}\xad\x8eNM\xa84\x07FU\x9c\xc4\xcc\x8e'
```

Beware that `hash64` returns **two** values, because it uses the 128-bit version of MurmurHash3 as its backend.

`hash_from_buffer` hashes byte-likes without memory copying. The method is suitable when you hash a large memory-view such as `numpy.ndarray`.

```shell
>>> mmh3.hash_from_buffer(numpy.random.rand(100))
-2137204694
>>> mmh3.hash_from_buffer(numpy.random.rand(100), signed=False)
3812874078
```

`hash64`, `hash128`, and `hash_bytes` have the third argument for architecture optimization. Use True for x64 and False for x86 (default: True):

```shell
>>> mmh3.hash64("foo", 42, True) 
(-840311307571801102, -6739155424061121879)
```

## Changelog
### 3.0.0 (2021-02-23)
* Python wheels are now available, thanks to the power of [cibuildwheel](https://github.com/joerick/cibuildwheel).
  * Supported platforms are `manylinux1_x86_64`, `manylinux2010_x86_64`, `manylinux2014_aarch64`, `win32`, `win_amd64`, `macosx_10_9_x86_64`, and `macosx_11_0_arm64` (Apple Silicon).
* Add support for newer macOS environments. Thanks [Matthew Honnibal](https://github.com/honnibal)!
* Drop support for Python 2.7, 3.3, 3.4, and 3.5.
* Add support for Python 3.7, 3.8, and 3.9.
* Migrate Travis CI and AppVeyor to GitHub Actions.

### 2.5.1 (2017-10-31)
* Bugfix for `hash_bytes`. Thanks [doozr](https://github.com/doozr)!

### 2.5 (2017-10-28)
* Add `hash_from_buffer`. Thanks [Dimitri Vorona](https://github.com/alendit)!
* Add a keyword argument `signed`.

### 2.4 (2017-05-27)
* Support seeds with 32-bit unsigned integers; thanks [Alexander Maznev](https://github.com/pik)!
* Support 64-bit data (under 64-bit environments)
* Fix compile errors for Python 3.6 under Windows systems.
* Add unit testing and continuous integration with Travis CI and AppVeyor.

### 2.3.2 (2017-05-26)
* Relicensed from public domain to [CC0-1.0](./LICENSE).

### 2.3.1 (2015-06-07)
* Fix compile errors for gcc >=5.

### 2.3 (2013-12-08)
* Add `hash128`, which returns a 128-bit signed integer.
* Fix a misplaced operator which could cause memory leak in a rare condition.
* Fix a malformed value to a Python/C API function which may cause runtime errors in recent Python 3.x versions.

The first two commits are from [Derek Wilson](https://github.com/underrun). Thanks!

### 2.2 (2013-03-03)
* Improve portability to support systems with old gcc (version < 4.4) such as CentOS/RHEL 5.x. (Commit from [Micha Gorelick](https://github.com/mynameisfiber). Thanks!)

### 2.1 (2013-02-25)
* Add `__version__` constant. Check if it exists when the following revision matters for your application.
* Incorporate the revision r147, which includes robustness improvement and minor tweaks.

Beware that due to this revision, **the result of 32-bit version of 2.1 is NOT the same as that of 2.0**. E.g.,:

```shell
>>> mmh3.hash("foo") # in mmh3 2.0
-292180858
>>> mmh3.hash("foo") # in mmh3 2.1
-156908512
```

The results of hash64 and hash_bytes remain unchanged. Austin Appleby, the author of Murmurhash, ensured this revision was the final modification to MurmurHash3's results and any future changes would be to improve performance only.

## License
[CC0-1.0](./LICENSE).

## Known Issues
### Getting different results from other MurmurHash3-based libraries
By default, mmh3 returns **signed** values for 32-bit and 64-bit versions and **unsigned** values for `hash128`, due to historical reasons. Please use the keyword argument `signed` to obtain a desired result.

For compatibility with Google Guava (Java), see <https://stackoverflow.com/questions/29932956/murmur3-hash-different-result-between-python-and-java-implementation>

### Unexpected results when given non 32-bit seeds
Version 2.4 changed the type of seeds from signed 32-bit int to unsigned 32-bit int. The resulting values with signed seeds still remain the same as before, as long as they are 32-bit.

```shell
>>> mmh3.hash("aaaa", -1756908916) # signed representation for 0x9747b28c
1519878282
>>> mmh3.hash("aaaa", 2538058380) # unsigned representation for 0x9747b28c
1519878282
```

Be careful so that these seeds do not exceed 32-bit. Unexpected results may happen with invalid values.

```shell
>>> mmh3.hash("foo", 2 ** 33)
-156908512
>>> mmh3.hash("foo", 2 ** 34)
-156908512
```

## Authors
MurmurHash3 was originally developed by Austin Appleby and distributed under public domain.

* <https://github.com/aappleby/smhasher>

Ported and modified for Python by Hajime Senuma.

* <http://pypi.python.org/pypi/mmh3>
* <http://github.com/hajimes/mmh3>

## See also
### Tutorials
The following textbooks and tutorials are great sources to learn how to use mmh3 (and other hash algorithms in general) for high-performance computing.

* Chapter 11: Using Less Ram in Micha Gorelick and Ian Ozsvald. 2014. *High Performance Python: Practical Performant Programming for Humans*. O'Reilly Media. [ISBN: 978-1-4493-6159-4](https://www.amazon.com/dp/1449361595).
* Duke University. [Efficient storage of data in memory](http://people.duke.edu/~ccc14/sta-663-2016/20B_Big_Data_Structures.html).
* Max Burstein. [Creating a Simple Bloom Filter](http://www.maxburstein.com/blog/creating-a-simple-bloom-filter/).
* Bugra Akyildiz. [A Gentle Introduction to Bloom Filter](https://www.kdnuggets.com/2016/08/gentle-introduction-bloom-filter.html).

### Similar libraries
* <https://github.com/wc-duck/pymmh3>: mmh3 in pure python (Fredrik Kihlander and Swapnil Gusani)
* <https://github.com/escherba/python-cityhash>: Python bindings for CityHash (Eugene Scherba)
* <https://github.com/veelion/python-farmhash>: Python bindigs for FarmHash (Veelion Chong)
* <https://github.com/escherba/python-metrohash>: Python bindings for MetroHash (Eugene Scherba)