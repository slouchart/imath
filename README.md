# pyimath
A pure Python library for finite field arithmetic and polynomial manipulation

## Introduction
```pyimath``` allows number manipulation from the following sets:
- Gaussian integers
- Prime fields
- Finite fields of dimension 2 and higher

You can also define polynomials whose coefficients are drawn from these sets.

The following operations are supported for numbers and polynomials:
- addition and subtraction
- multiplication
- multiplication by a member of the base set (external multiplication as in a vector space)
- exponentiation by a positive integer

Rings (Gaussian integers, Polynomials) support:
- euclidean division a.k.a `divmod` (// and %)
- GCD

Fields support:
- true division
- Frobenius reciprocal
- floor division (//) and modulo (%) are supported but default respectively to true division(/) and zero

## Installation
Download the sources, unzip them, change your current directory and execute:

    python setup.py install
    
You may wish to run the test suite before:

    python setup.py test

Registration with PyPI is pending, once it is done, you can install ``pyimath`` using:

    pip install pyimath
    
## Usage

### Prime field creation

```python
from pyimath.primefield import PrimeField
f2 = PrimeField(2)
```

The integer passed as a parameter must be prime and represents the characteristic of the field.

### Elements of a prime field

In a prime field of characteristic p, the elements are mapped onto the integers in the range [-(p-1)/2.. (p-1)/2]
For instance, the prime field F7 contains -3, -2, -1, 0, 1, 2 and 3

You can instantiate any element in two ways:

```python
from pyimath.primefield import PrimeField
f7 = PrimeField(7)
e = f7(-3)
g = f7.element(2)
```
    
At this point, all operations are supported using the common operators `+ - * / **`

### Defining a polynomial over a prime field

```python
from pyimath.polynomial import Polynomial
from pyimath.primefield import PrimeField

p0 = Polynomial((-1, 0, 1, 1), PrimeField(3))

# or, alternatively:
p1 = PrimeField(3).polynomial(-1, 0, 1, 1)
```
    
### Symbolic polynomials

```python
from pyimath.primefield import PrimeField
from pyimath.polyparse import symbolic_polynomial
p0 = symbolic_polynomial('X^3 + X^2 - 1', PrimeField(3))

# or, alternatively:
p1 = PrimeField(3).parse_poly('X^3 + X^2 - 1')
```

### Creation of a finite field
Finite fields are vector spaces built upon a prime field. For instance, F4 the finite field with 4 elements can be seen as a space vector of dimension 2 over F2, the prime field of characteristic 2.
However, the creation of a finite field involves polynomials. Given an irreducible polynomial P of degree q over a prime field of characteristic p, the quotient set Fp[X]/\<P> of the ring of polynomials over Fp divided by the ideal generated by P is a field of characteristic p with p^q elements
This finite field is a vector space of dimension q over the field Fp

```python
from pyimath.finitefield import FiniteField, PrimeField

f2 = PrimeField(2)
f4 = FiniteField(2, 2, f2.parse_poly('X^2 + X + 1'))
```

### Elements of a finite field
Elements are defined as coefficients in a vector basis. If w is a root of the irreducible polynomial used in the definition (see above), {1, w, ..., w^(q-1)} is such a vector basis
Therefore, elements of such a field are isomorphic to polynomials over the base prime field up to degree q-1

```python
from pyimath.finitefield import finite_field
f4 = finite_field(4)
e = f4(0, 1)  # the element w
a = f4(1, 0)  # the element 1
```

In the rest of this document, we use the term 'finite field' to cover both prime fields and finite fields 
    
### Polynomial Factorization
A polynomial over a finite field can be factorized into irreducible factors using the Cantor-Zassenhaus algorithm

```python
from pyimath.finitefield import finite_field
from pyimath.polyparse import symbolic_polynomial
from pyimath.factorize import factorize
f3 = finite_field(3)
p = symbolic_polynomial('-X^4 + X - 1', f3)
factors, constant_term = factorize(p).cantor_zassenhaus()
for factor in factors:
    print(factor)
```

## Instantiation of finite fields for busy/lazy people
If finding irreducible polynomials over prime fields to create finite fields bothers you, you may get any finite field up to order 27 with the factory function `finite_field`:

```python
from pyimath.finitefield import finite_field
f3 = finite_field(3)
f25 = finite_field(25)
...
```

## A final note

This module is intended for educational purposes, mainly mine. If you need to deal with finite fields and polynomial factorization as a real applied mathematician, you may want to use FLINT and its Python binding.
- FLINT (Fast Library for Number Theory) home page: http://flintlib.org/
- Python-FLINT repository: https://github.com/python-flint/python-flint
