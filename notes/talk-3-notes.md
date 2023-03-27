## **[Introduction to Lattice Cryptography](#)**
### Aayush Yadav, George Mason University
Monday, March 27 2023

--------------------------------------------------------------------------------

### **Abstract**

In this two-part lecture, we will attempt to address two fundamental questions — 
what are lattices, and what makes them cryptographically interesting? For the 
first part, we review the mathematical background, introduce the shortest vector 
problem and consider the LLL algorithm, all with an eye towards modern 
foundations of lattice-based cryptography.

<br/>

### **Reading List**

[1] _Notes for Seminar on Lattices In Cryptography_ (1-4), Chris Peikert (2022) 
&bull;&nbsp;&nbsp;[`url`](https://github.com/cpeikert/LatticesInCryptography)

--------------------------------------------------------------------------------

## **Notes**

### **I. Basic Definitions**

Lattices, as mathematical objects, go at least as far back as the 18th century
with particular applications in number theory. Let us begin by defining a 
lattice,

**Definition I.1.** (Lattice) &nbsp;&nbsp; _An $n$-dimensional lattice $\mathscr{L}$ is a 
discrete additive subgroup of $\mathbb{R}^n$._

So, an $n$-dimensional lattice satisfies two properties:

1. It is **discrete**, ie. for $\mathcal{B}$, an open ball of unit radius 
centered at the origin,

$$\forall x \in \mathscr{L}, \exists \varepsilon > 0 : (x + \varepsilon\mathcal{B}) \cap \mathscr{L} = \{x\}$$

In words &mdash; $x$ is the only lattice point in its $\varepsilon$-neighbourhood.

2. It is an **subgroup of $\mathbb{R}^n$ under addition**. This should be self 
explanatory; but do note that conditioned on this fact, we may reverse the 
quantifiers above so that

$$\exists\varepsilon > 0, \forall x \in \mathscr{L} : (x + \varepsilon\mathcal{B}) \cap \mathscr{L} = \{x\}$$

this follows since if $\varepsilon_1 > \varepsilon_2 > 0$ are two such values 
for $x_1, x_2 \in \mathscr{L}$ respectively, then by the additive group property,
$\varepsilon_1$ is also such a point for $x_2$.

**Examples**

1. $\{\mathbf{0}\} \in \mathbb{R}^n$ <ins>is</ins> a lattice for any positive 
integer $n$. How about $n \in \mathbb{Q^+}, \mathbb{R^+}, \mathbb{Z}, \ldots$?
2. The so called "number-line" over the integers <ins>is</ins> a $1$-dimensional 
lattice.
3. Any lattice $\mathscr{L}$ scaled by some $c \in \mathbb{R}$ such that 
$c\mathscr{L} = \{ c\mathbf{x} : x \in \mathscr{L} \}$ <ins>is</ins> a lattice.
4. The subgroup of even integers $2\mathbb{Z}$ <ins>is</ins> a lattice whereas
the set of odd integers $2\mathbb{Z} + 1$ <ins>is not</ins>. Which property does
it violate?
5. The subgroup $\mathbb{Q} \subset \mathbb{R}$ <ins>is not</ins> a lattice as 
it is dense in the reals. In particular, one can find a rational number 
arbitrarily close to zero, so $\mathbb{Q}$ is not discrete.

The following definitions will also be helpful&mdash;

**Definition I.2.** (Rank) &nbsp;&nbsp; _The rank $k$, of a lattice $\mathscr{L}$ is the dimension of its linear span._

As an example, The subset $\mathbb{Z} \times \{0\} = \{(z, 0) : z \in \mathbb{Z}\} \subset \mathbb{R}^2$
is $2$ dimensional lattice of rank $1$. In most cryptographic applications we 
typically deal with full rank lattices.

**Definition I.3.** (Lattice basis) &nbsp;&nbsp; _A basis $\mathbf{B} = \{\mathbf{b}_i\}_{i=1}^n$ of lattice $\mathscr{L}$ is a set of linearly independent points of $\mathbb{R}^n$ such that_

$$\mathrm{span}_\mathbb{Z}(\mathbf{B}) = \left\{\sum_{i=1}^n z_i\mathbf{b}_i :z_i \in \mathbb{Z}\right\} = \mathscr{L}_\mathbf{B} = \mathscr{L} \quad .$$

Equivalently, for $\mathbf{B} = \begin{pmatrix} \vert & & \vert \\ \mathbf{b}_1 & \cdots & \mathbf{b}_n \\ \vert & & \vert\end{pmatrix} \in \mathbb{R}^{n \times n}$, $\mathscr{L} = \mathscr{L}_\mathbf{B} = \mathbf\{{B}\cdot \mathbf{z} : \mathbf{z} \in \mathbb{Z}^n\}$. Note here that for a 
given lattice, while it always has a basis, it is not unique. Indeed, as we will
see, this is essentially what enables cryptography on lattices.

Lastly, we have the following proposition

**Definition I.4.** (Determinant of a lattice) _The determinant of a lattice $\mathscr{L}$, also known as the **volume** is given by $\det{(\mathscr{L})} = \vert \det{(\mathbf{B})} \vert$._

A proof is easily obtained given the notion of a fundamental region of a lattice,
although we omit both details here for brevity.

### **II. A notion of distance**

As is common in linear algebra, one can associate a notion of _length_ 
with any lattice element under some norm. Since each element then has a length 
associated with it, there must be a minimal such element. Such an element is not
unique.

**Definition II.1.** (Minimum distance) &nbsp;&nbsp; _The minimum distance of a
lattice $\mathscr{L}$ is the length of its shortest element_

$$\lambda_1(\mathscr{L}) \triangleq \min\limits_{\mathbf{x} \in \mathscr{L}} \vert\vert \mathbf{x} \vert\vert = \min\limits_{\substack{\mathbf{x_i}, \mathbf{x_j} \in \mathscr{L} \\ i \ne j}} \vert\vert \mathbf{x}_i - \mathbf{x}_j \vert\vert$$

We are now ready for our first result that bounds the minimum distance of a 
lattice from above by the $n^\text{th}$-root of its determinant. The bound 
follows as a corollary of the following theorem due to Minkowski,

**Theorem II.2.** _Given an $n$-dimensional lattice $\mathscr{L}$ (of full rank), any convex, centrally symmetric body $\mathcal{S} \subset \mathbb{R}^n$ of volume $> 2^n \cdot \det{(\mathscr{L})}$ contains a non-zero lattice point._

**Corollary II.3.** _For any $n$-dimensional lattice $\mathscr{L}$,_
$$\lambda_1(\mathscr{L}) \leqslant \sqrt{n} \cdot \det{(\mathscr{L})}^{1/n} \qquad .$$

**_Proof sketch._** The proof relies on the fact that the bound above scales properly with the 
lattice which allows us to take $\det{(\mathscr{L})} = 1$ without any loss of
generality. Then, one can show that the closed ball of radious $\sqrt{n}$ has
volume $> 2^n \cdot \det{(\mathscr{L})} = 2^n$ and therefore contains a non-zero
lattice point.

It is known that in the general case we cannot imporive this bound beyond small 
constant factors.

### **III. Shortest Vector Problem**

Given this notion of distance, we can define the computational problem known as
the **Shortest Vector Problem** (SVP)&mdash;

**Definition III.1.** (Shortest Vector Problem) The SVP restricted to integer 
lattices, mostly without loss of generality, is one of the following three 
variants:

1. Decision: given a lattice basis $\mathbf{B}$ and a real number $\delta > 0$, 
distinguish between instances where 
$\lambda_1(\mathscr{L}_\mathbf{B}) > \delta$ and where 
$\lambda_1(\mathscr{L}_\mathbf{B}) \leqslant \delta$.
2. Calculation: given a lattice basis $\mathbf{B}$, find 
$\lambda_1(\mathscr{L}_\mathbf{B})$.
3. Search: given a lattice basis $\mathbf{B}$, find a non-zero vector 
$\mathbf{x} \in \mathscr{L}_\mathbf{B}$ such that 
$\vert\vert \mathbf{x} \vert\vert = \lambda_1(\mathscr{L}_\mathbf{B})$.

It is easily observed that 

$$\mathsf{D\text{-}SVP} \leqslant \mathsf{C\text{-}SVP} \leqslant \mathsf{S\text{-}SVP} \qquad .$$

Perhaps less trivial is the fact that $\mathsf{C\text{-}SVP} \leqslant \mathsf{D\text{-}SVP}$, 
since given a decision oracle for $\mathsf{SVP}$, one can find $\lambda_1(\mathscr{L}_\mathbf{B})$
in polynomial time via a binary search on $d$. This runs in polynomial time 
due to **Corollary II.3**. In fact, it is known that $\mathsf{D\text{-}SVP} = \mathsf{C\text{-}SVP} = \mathsf{S\text{-}SVP}$.

In cryptography, we will often be more interested in approximate forms of these 
problems.

**Definition III.2.** (Approximate SVP) The $\gamma$-approximate SVP, for 
$\gamma = \gamma(n) \geqslant 1$, is one of the following three variants:

1. Decision ($\mathsf{GapSVP_\gamma}$): given a lattice basis $\mathbf{B}$ and a real number $\delta > 0$, 
distinguish between instances where 
$\lambda_1(\mathscr{L}_\mathbf{B}) > \gamma \cdot \delta$ and where 
$\lambda_1(\mathscr{L}_\mathbf{B}) \leqslant \delta$.
2. Estimation ($\mathsf{EstSVP_\gamma}$): given a lattice basis $\mathbf{B}$, find 
$\lambda_1(\mathscr{L}_\mathbf{B})$ upto a factor of $\gamma$.
3. Search ($\mathsf{SVP_\gamma}$): given a lattice basis $\mathbf{B}$, find a non-zero vector 
$\mathbf{x} \in \mathscr{L}_\mathbf{B}$ such that 
$\vert\vert \mathbf{x} \vert\vert \leqslant \gamma \cdot \lambda_1(\mathscr{L}_\mathbf{B})$.

As before, we have that,

$$\mathsf{GapSVP}_\gamma = \mathsf{EstSVP}_\gamma \leqslant \mathsf{SVP}_\gamma$$

where the equality holds due to a similar Cook reduction as in the exact version. 
For $1 < \gamma < \Omega(2^n)$ it is now known if $\mathsf{SVP}_\gamma \leqslant \mathsf{GapSVP}_\gamma$.
For $\gamma > \Omega(2^n)$, we have a polynomial time algorithm for both 
$\mathsf{GapSVP}_\gamma$ as well as $\mathsf{SVP}_\gamma$ due to Lenstra, 
Lenstra and Lovász [[LLL82]](https://link.springer.com/article/10.1007/BF01457454).

### **IV. Lenstra-Lenstra-Lovász (LLL) Algorithm**

Let us first recall the matrix factorization technique central to the LLL 
algorithm, called the **Gram-Shcmidt Orthogaonalization**. For linearly 
independent vectors $\mathbf{b}_1, \ldots, \mathbf{b}_n \in \mathbb{R}^n$, the
Gram-Schmidt process is a constructive process that yields linearly independent, 
mutually orthogonal vectors $\mathbf{\tilde{b}}_1, \ldots, \mathbf{\tilde{b}}_n \in \mathbb{R}^n$
as follows:

$$
\begin{aligned}
\mathbf{\tilde{b}}_1 \triangleq \mathbf{b}_1 \\
\mathbf{\tilde{b}}_2 \triangleq \mathbf{b}_2 - \underbrace{\frac{\langle \mathbf{b}_2, \mathbf{\tilde{b}}_1\rangle}{\vert\vert \mathbf{\tilde{b}}_1 \vert\vert^2}}_{\mu_{1, 2}} \cdot \mathbf{\tilde{b}}_1\\
\vdots\\
\mathbf{\tilde{b}}_j \triangleq \mathbf{b}_j - \sum_{i < j}\underbrace{\frac{\langle \mathbf{b}_j, \mathbf{\tilde{b}}_i\rangle}{\vert\vert \mathbf{\tilde{b}}_i \vert\vert^2}}_{\mu_{i, j}} \cdot \mathbf{\tilde{b}}_i \qquad .
\end{aligned}
$$

In words, define $\mathbf{\tilde{b}}_1 = \mathbf{b}_1$ and then set each 
subsequent $\mathbf{\tilde{b}}_j$ to be the component of $\mathbf{b}_j$
orthogonal to the $\mathrm{span}\{\mathbf{b}_i\}_{i=1}^j\text{ }(= \mathrm{span}\{\mathbf{\tilde{b}}_i\}_{i=1}^j)$. Equivalently, in matrix notation, we have

$$\mathbf{B} = \begin{pmatrix} \vert & & \vert \\ \mathbf{b}_1 & \cdots & \mathbf{b}_n \\ \vert & & \vert\end{pmatrix} = \underbrace{\begin{pmatrix} \vert & & \vert \\ \mathbf{\tilde{b}}_1 & \cdots & \mathbf{\tilde{b}}_n \\ \vert & & \vert\end{pmatrix}}_{\mathbf{\tilde{B}}} \cdot \underbrace{\begin{pmatrix} 1 & \mu_{1, 2} & \cdots &  \mu_{1, n} \\ 0 & 1 & \cdots & \mu_{2, n} \\ 0 & 0 & \cdots & \mu_{n, n} \end{pmatrix}}_{\mathbf{U}} \qquad .$$

Since $\mathbf{U}$ is unimodular, we have as a special case that $\mathscr{L}_\mathbf{B} = \mathscr{L}_\mathbf{\tilde{B}}$. Furthermore,

$$\mathbf{\tilde{B}} = \underbrace{\begin{pmatrix} \vert & & \vert \\ \frac{\mathbf{\tilde{b}}_1}{\vert\vert \mathbf{\tilde{b}}_1 \vert\vert} & \cdots & \frac{\mathbf{\tilde{b}}_n}{\vert\vert \mathbf{\tilde{b}}_n \vert\vert} \\ \vert & & \vert\end{pmatrix}}_{\mathbf{Q}} \cdot \underbrace{\begin{pmatrix} \vert\vert \mathbf{\tilde{b}}_1 \vert\vert & & & \\ & \vert\vert \mathbf{\tilde{b}}_2 \vert\vert & & \\ & & \ddots & \\ & & & \vert\vert \mathbf{\tilde{b}}_n \vert\vert\end{pmatrix}}_{\Delta}$$

This is the $\mathbf{QR}$-factorization of $\mathbf{B}$ where $\mathbf{Q}$ is an
orthogonal matrix and $\mathbf{R} = \Delta \mathbf{U}$ is an upper-triangular 
matrix with $\vert\vert \mathbf{\tilde{b}}_i \vert\vert$ on the diagonals. Since
$\mathbf{Q}$ acts as rigid rotations of $\mathbb{R}^n$, essential properties 
such as inner products, Euclidean norms and volumes are preserved. So we can 
instead focus on $\mathbf{R}$. In particular,

**Lemma IV.1.** For any lattice $\mathscr{L}_\mathbf{B}$,

$$\det(\mathscr{L}_\mathbf{B}) = \prod_{i=1}^n \vert\vert \mathbf{\tilde{b}}_i \vert\vert \qquad .$$

Using this, we have the following key lemma

**Lemma IV.2.** For any lattice $\mathscr{L}_\mathbf{B}$,

$$\lambda_1(\mathscr{L}_\mathbf{B}) \geqslant \min_i \vert\vert \mathbf{\tilde{b}}_i \vert\vert \qquad .$$

<img src="figure-1.png" width="400" height="300" style="display: block; margin-left: auto; margin-right: auto;"/>

This lemma has an intuitive appeal &ndash; consider the partition of the lattice 
points $\mathbf{x} = \sum_{i=1}^2 z_i \mathbf{b}_i $ for each $z_i \in \mathbb{Z}$ 
according to the integer coefficient $z_2$ of $\mathbf{b}_2$. Then, clearly, any
such $\mathbf{x}$ lies in the affine subspace 
$z_2\mathbf{b}_2 + \mathrm{span}(\mathbf{b}_1)$. If $z_2 = 0$, then we are done 
since $\lambda_1(\mathscr{L}_\mathbf{B}) = \lambda_1(\mathscr{L}_{\{\mathbf{b}_1\}}) = \vert\vert \mathbf{b}_1 \vert\vert = \vert\vert \mathbf{\tilde{b}}_1 \vert\vert$. Otherwise, for every 
point we have $\vert\vert \mathbf{x} \vert\vert \geqslant \vert z_2 \vert \cdot \vert\vert \mathbf{\tilde{b}}_2 \vert\vert \geqslant \vert\vert \mathbf{\tilde{b}}_2 \vert\vert$, whence
$\lambda_1(\mathscr{L}_\mathbf{B}) \geqslant \min \left\{\vert\vert \mathbf{\tilde{b}}_1 \vert\vert, \vert\vert \mathbf{\tilde{b}}_2 \vert\vert \right\}$.

Let us now begin to understand the structure of the LLL algorithm. As we 
mentioned, it gives a polynomial time algorithm for $\mathsf{SVP}_\gamma$, with
$\gamma = 2^{(n-1)/2}$. This is non-trivial as it depends only on the dimension
of the lattice alone. In particular, for small $n$, this algorithm can give 
practical efficiency. We begin with a definition

**Definition IV.3** (LLL-reduced basis) _A lattice basis $\mathbf{B}$ is 
LLL-reduced if:_

1. _It is **size reduced**, ie., for all $i < j$, we have 
$\vert \mu_{i, j} \vert \leqslant \frac{1}{2}$; and_
2. _It meets the **Lovász-condition**, which says that for all $i < n$, we have
$\frac{3}{4}\vert\vert \mathbf{\tilde{b}}_i\vert\vert^2 \leqslant \vert\vert \mu_{i, i+1}\mathbf{\tilde{b}}_i + \mathbf{\tilde{b}}_{i+1}\vert\vert^2$._

These conditions ensure that the lengths of the Gram-Schmidt vectors do not 
decrease (vis-à-vis size reduction), but not too quickly (Lovász-condition). The
second condition is necessary for **Lemma IV.4** as we will later discuss. First,
we give the algorithm_

```
The LLL Algorithm

1:  fn LLL(B := int[n,n]):          // returns an LLL-reduced basis of the lattice
2:      B = size_reduce(B)
3:      for i = 1 to n - 1:
4:          if <Lovász-condition is violated>:
5:              swap(B[:, i], B[:, i + 1])
6:              goto line 2
7:      return B
8:
9:  fn size_reduce(B := int[n,n]):  // returns a size-reduced basis of the lattice, preserving B_
10:     B_, U = gram_schmidt(B)
11:     for j = 2 to n:
12:         for i = j - 1 down to 1:
13:             B[:, j] = B[:, j] - round(U[i,j]) * B[:,i]
```

As can be observed, this algorithm iteratively updates the basis matrix 
$\mathbf{B}$ so it is size-reduced, and maintains the Lovász-condition 
throughout. The `size_reduce` is of further interest, but an analysis is omitted
in the interest of time. The main lemma that power the LLL algorithm is the
following

**Lemma IV.4** _In an LLL-reduced basis $\mathbf{B} \in \mathbb{Z}^{n\times n}$,_

$$\vert\vert \mathbf{\tilde{b}}_{i+1}\vert\vert^2 \geqslant \frac{1}{2}\vert\vert \mathbf{\tilde{b}}_i\vert\vert^2, \quad \forall i < n \text{ }.$$

**Corollary IV.5** _In an LLL-reduced basis $\mathbf{B}$ we have_

$$\vert\vert \mathbf{b}_1\vert\vert \leqslant 2^{(n-1)/2} \cdot \lambda(\mathscr{L}_\mathbf{B}) \quad .$$

**_Proof._** By the previous lemma, $\vert\vert \mathbf{\tilde{b}}_{i+1}\vert\vert \geqslant \frac{1}{\sqrt{2}}\vert\vert \mathbf{\tilde{b}}_i\vert\vert$ for all $i < n$,

$$\leadsto \vert\vert \mathbf{b}_{1}\vert\vert = \vert\vert \mathbf{\tilde{b}}_{1} \vert\vert \leqslant 2^{(i-1)/2} \cdot \vert\vert \mathbf{\tilde{b}}_{i} \vert\vert \leqslant 2^{(n-1)/2} \cdot \vert\vert \mathbf{\tilde{b}}_{i} \vert\vert, \qquad \forall i < n\text{ }.$$

Thus,

$$\vert\vert \mathbf{b}_{1}\vert\vert \leqslant 2^{(n-1)/2} \cdot \min_{i<n} \vert\vert \mathbf{\tilde{b}}_{i} \vert\vert \leqslant 2^{(n-1)/2} \cdot \lambda_1(\mathscr{L}_\mathbf{B}) \quad .$$

Here, the last ineuqlity is due to **Lemma IV.2**.&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
&nbsp;&nbsp;&nbsp;&nbsp;$\square$

Of course, it is also required that given a suitable (integral) lattice basis 
$\mathbf{B} \in \mathbb{Z}^{n \times n}$, the LLL algorithm must return the
LLL-reduced basis matrix in time polynomial in $n$ and the bit length of 
$\mathbf{B}$. We complete the discussion by simply stating that this is indeed 
the case.

**Coppersmith's Method.** An application of LLL is the algorithm due to 
Coppersmith [[Cop96]](https://link.springer.com/chapter/10.1007/3-540-68339-9_14) 
for finding _small_ roots of a polynomial modulo a given number $N$. When $N$ is 
prime, there are efficient algorithms that can give all roots of a given 
polynomial modulo $N$. For a degree-$d$ polynomial, there are at most $d$ such 
roots. When $N$ is a product of $k$ distinct primes, then by the 
**Remainder Theorem**, any square modulo $N$ has $2^k$ distinct roots! In 
general, no efficient algorithm can return all roots.

Given a monic integer polynomial of degree-$d$ $f(x) \in \mathbb{Z}[X]$, 
Coppersmith's method outputs all integers $\alpha$ such that 
$\vert \alpha \vert \leqslant N^{1/d}$ and $f(\alpha) \equiv 0 \pmod{N}$ for 
any integer $N$.

Note that Coppersmith's method does not assume that the factorization of $N$ is 
known, but it does only output certain _small_ roots. This is related, also, to
the hardness of factoring since &ndash; knowing two _non-trivial_ square roots 
$\alpha_1 \ne \pm \alpha_2$ one can factor $N$ as 
$\gcd(\alpha_1 - \alpha_2, N)$ &mdash; this essentially follows 
because a non-trivial root is a zero divisor so $N$ and $\alpha_1 - \alpha_2$ 
share a common divisor. However, a square cannot have more than one small root 
of magnitude $\sqrt{N}$ so factoring remains hard.

Many attacks using Coppersmith's method have been shown against existing 
cryptosystems. Notably, an attack against RSA-encryption under small exponents 
(and short padding) efficiently recovers an encrypted message given just two 
encryptions of under different paddings. There are also lattice-based attacks 
for factoring integers of the form $N = p^rq$ for large $r$ [[BDH99]](https://link.springer.com/chapter/10.1007/3-540-49649-1_3) as well as attacks against discrete log based signing 
algorithms for biased cryptographic-nonces [[HS01](https://link.springer.com/article/10.1023/a:1011214926272), [NS02](https://link.springer.com/article/10.1023/A:1025436905711), [NS03](https://link.springer.com/article/10.1023/A:1025436905711)].
