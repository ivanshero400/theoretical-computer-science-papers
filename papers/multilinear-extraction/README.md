# Extracting the Multilinear Part of a Monotone Arithmetic Circuit

**Working paper · 23 November 2026 · Draft for review**

[العربية — النص الكامل](README.ar.md)

## Abstract

A short arithmetic circuit may represent a polynomial with exponentially many monomials. Suppose that only $k$ distinct output monomials contain a repeated variable. For a monotone circuit with $s$ gates, we give a circuit of size $O(s(k+1)^3)$ that retains exactly the squarefree monomials and their coefficients. The construction partitions these monomials into at most $k+1$ classes and isolates each class by weighted degree. A separate application of weighted-degree extraction bounds the overlap between two small monotone circuits whose difference is the determinant. The novelty of these statements remains open to review.

## ملخص

قد تمثّل دارة حسابية صغيرة كثيرة حدود يصعب حتى سرد جميع حدودها. إذا تكرّر متغير في كل واحد من $k$ حدود مختلفة في الناتج، فيمكن حذف هذه الحدود مع الاحتفاظ بمعاملات سائر الحدود. يحدّد البرهان حجم الدارة الجديدة بدلالة حجم الدارة الأصلية وعدد الحدود المحذوفة. فعندما يكون هذا العدد ثابتًا، يظل حجم الدارة الجديدة خطيًا في حجم الأصل.

## 1. Setting and statement

A *monotone arithmetic circuit* is a DAG with input gates $x_1,\dots,x_n$, constant gates $c\in\mathbb{Z}_{\ge 0}$ (written in binary), and fan-in-2 gates $+$ and $\times$. Its size $s$ is the number of gates. It computes a polynomial $A\in\mathbb{Z}_{\ge0}[x_1,\dots,x_n]$.

For $A=\sum_\alpha c_\alpha x^\alpha$, let $\mathrm{ML}(A)=\sum_{\alpha\in\{0,1\}^n} c_\alpha x^\alpha$ be its multilinear part. A monomial $x^\alpha$ with $c_\alpha>0$ and $\max_i\alpha_i\ge2$ is *bad*; let $k(A)$ be the number of bad monomials.

**Theorem 1 (removal).** Suppose $A$ is computed by a monotone arithmetic circuit of size $s$ and has $k$ bad monomials. Then $\mathrm{ML}(A)$ is computed by a monotone arithmetic circuit of size $O\big(s\,(k+1)^3\big)$. The circuit retains the coefficient of each squarefree monomial. Its literal constants come from the input circuit together with zero.

**Consequences of the size measure.**
- Coefficients are carried as gates rather than integers. For example, $c_{40}=2^{2^{40}}$ produced by repeated squaring is never written out.
- The dependence on $k$ is essential, in the following sense. If $k$ is exponential, ML can be exponentially harder: take $\prod_i\sum_j x_{ij}y_j$, whose multilinear part in $y$ encodes the permanent.

## 2. Proof of Theorem 1

The proof uses the exceptional exponent vectors as auxiliary information. The theorem bounds circuit size. A running-time bound for discovering these vectors is a separate question.

**Step 1: an adaptive partition tree**. Each node $v$ carries disjoint coordinate sets $Z_v$ ("exponent 0") and $P_v$ ("exponent exactly 1"). A bad vector $b$ is *retained* at $v$ if $b_i=0$ for every $i\in Z_v$ and $b_i=1$ for every $i\in P_v$.
- The root has $Z=P=\emptyset$.
- If $v$ retains some $b$, pick a coordinate $i\notin Z_v\cup P_v$ with $b_i\ge2$; one exists because $b$ is bad and matches the node. Create two children: one adds $i$ to $Z$, the other adds $i$ to $P$.
- Otherwise $v$ is a leaf.

Two facts follow:
1. $b$ is retained by neither child, since $b_i\ge2$ is neither 0 nor 1. Every other retained vector goes to at most one child. Charge each internal node to the vector $b$ it eliminates. Each vector is charged at most once, so there are at most $k$ internal nodes and at most $k+1$ leaves. Every root-to-leaf path has $|P|\le k$.
2. Every multilinear exponent vector $\alpha$ follows exactly one root-to-leaf path: at a node that branches on $i$, go to the $Z$ child if $\alpha_i=0$ and to the $P$ child if $\alpha_i=1$. So the leaves partition the multilinear monomials, and
$$\mathrm{ML}(A)=\sum_{\text{leaves } \ell}\ \mathrm{ML}(A)_{\ell},$$
where $\mathrm{ML}(A)_\ell$ collects the multilinear monomials whose exponent is 0 on $Z_\ell$ and 1 on $P_\ell$.



**Step 2: weighted-degree extraction at a leaf**. Fix a leaf $\ell$ with sets $Z,P$. Set $x_i=0$ for $i\in Z$ to obtain $A_Z$; this costs nothing extra. Give each $i\in P$ a positive integer weight $w_i$, give every other variable weight 0, and set $D=\sum_{i\in P}w_i$. We want:

(★) a monomial $x^\alpha$ of $A_Z$ has weighted degree $\sum_{i\in P}w_i\alpha_i=D$ **iff** it lies in $\mathrm{ML}(A)_\ell$.

- *Good monomials.* These have $\alpha_i\in\{0,1\}$. With all weights positive, the weighted degree equals $D$ iff $\alpha_i=1$ for every $i\in P$, which is exactly membership in $\mathrm{ML}(A)_\ell$.
- *Bad monomials surviving in $A_Z$.* These have $b_i=0$ on $Z$. Because $\ell$ is a leaf, $b$ is not retained, so $b|_P\neq\mathbf 1$. Condition (★) then requires $\sum_{i\in P}w_i(b_i-1)\neq0$, a constraint given by a nonzero integer vector.

Order $P$ arbitrarily and choose the weights in that order. Group the constraints by the last coordinate of $P$ at which their coefficient is nonzero, and let $r_i$ be the size of group $i$. When $w_i$ is chosen, every earlier weight is fixed, so each constraint in group $i$ forbids at most one value of $w_i$. Some $w_i\in\{1,\dots,r_i+1\}$ is therefore allowed. Since $\sum_i r_i\le k$, we get $D\le|P|+k\le 2k$.

Next, homogenize with respect to the weights. Each gate $g$ is replaced by gates for its weighted-degree components $g^{(0)},\dots,g^{(D)}$:
- $(g+h)^{(d)}=g^{(d)}+h^{(d)}$;
- $(gh)^{(d)}=\sum_{a+b=d}g^{(a)}h^{(b)}$;
- an input $x_i$ goes to component $w_i$ if $i\in P$ and to component 0 otherwise; constants go to component 0.

This uses only $+$ and $\times$, so it is monotone, and it has $O(s(D+1)^2)$ gates. Its output component $A_Z^{(D)}$ equals $\mathrm{ML}(A)_\ell$ by (★).

**Step 3: assembly.** Sum the at most $k+1$ leaf circuits. The total size is $(k+1)\cdot O(s(2k+1)^2)=O(s(k+1)^3)$. For $k=0$ the original circuit already works. ∎


## 3. Support overlap in a monotone representation of the determinant

Let $\det_n=A-B$, where $A$ and $B$ have monotone circuits with at most $s$ gates each. Write $S=\operatorname{supp}(A)\cap\operatorname{supp}(B)$. Every monomial outside $S$ appearing in $A+B$ is a permutation monomial. Every permutation monomial occurs in $A+B$, though its coefficient may exceed one.

**Proposition 2.** If $s$ is polynomial in $n$, then $|S|\ge 2^{\Omega(n)}$.

**Profile extraction.** Associate to a monomial its vector of $n$ row degrees and $n$ column degrees. The all-ones profile characterizes permutation monomials. Let $r$ count the distinct other profiles in $A+B$, so $r\le |S|$. Apply the weight-selection argument of Section 2 to the differences between these profiles and the all-ones vector, now on $2n$ coordinates. It gives positive row and column weights of total $D\le 2n+r$. Give $x_{ij}$ the sum of the weights of row $i$ and column $j$. The component of degree $D$ has precisely the support of the permanent, with positive coefficients. Its monotone circuit has $O((s+1)(2n+r+1)^2)$ gates.

**Why arbitrary positive coefficients suffice.** Consider a fan-in-two monotone circuit with this support. Discard zero and unused gates. For each permutation monomial, expand one computation witnessing its production into a tree, selecting one child at each addition and both at each multiplication. For $n\ge 3$, the tree contains a gate contributing a monomial of degree between $n/3$ and $2n/3$: descend through the child of greater degree until the degree first falls to at most $2n/3$.

Fix such a circuit gate and one witness through it. The factors outside that occurrence give a fixed context monomial. Substituting any monomial from the gate into this context must give a permutation monomial, by monotonicity. Thus every monomial at the gate covers the same $d$ rows and $d$ columns, namely those omitted by the context. The number of permutations witnessed through this gate is at most $d!(n-d)!$, since the remaining rows must be matched to the remaining columns. Covering all $n!$ permutations requires at least $\min_{n/3\le d\le 2n/3}\binom nd=2^{\Omega(n)}$ gates, with the minimum over integers. Combining the two bounds proves the proposition and, for polynomial $s$, the stronger bound $r\ge 2^{\Omega(n)}$.

The restriction on $s$ matters. Explicitly summing the even and odd permutation monomials in separate circuits gives empty overlap at the cost of large circuits.

## 4. Verification and open questions

Finite checks compared the extracted polynomial with direct expansion, including shared subcircuits and coefficients greater than one. For example, $(1+x)^2(1+y)$ must yield $1+2x+y+2xy$. The factor of two on $xy$ tests coefficient preservation independently of the support check. Such tests supplement the proof; the asymptotic gate bound follows from the leaf count and the degree budget.

Two questions remain for this note: can the cubic dependence on $k+1$ be reduced, and where does this parameterization first appear in the literature? The size theorem uses a list of exceptional exponents. A uniform complexity analysis must also account for obtaining that list and representing its entries.

## References and context

- M. Jerrum and M. Snir (1982). *Some exact complexity results for straight-line computations over semirings*. The permanent lower bound is the background for the support-counting argument in Section 3. [Article](https://doi.org/10.1145/322326.322341)
- R. Sengupta (1997). *Cancellation is exponentially powerful for computing the determinant*. This paper studies determinant computation in the absence of cancellation. Section 3 measures the number of distinct shared output monomials in a difference representation. [Article](https://doi.org/10.1016/S0020-0190(97)00069-0)
- S. Jukna. *Notes on Boolean Read-k and Multilinear Circuits*. Related work on multilinearity in Boolean and arithmetic models. [Preprint](https://arxiv.org/abs/2207.08701)
