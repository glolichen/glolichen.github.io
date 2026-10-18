---
title: A Monad is a Monoid in the Category of Endofunctors
author: 'Jayden Li'
date: "2027-10-15"
---

<!-- <style> -->
<!-- p:has(+ ul) { -->
<!--   margin-bottom: 0; -->
<!-- } -->
<!-- p + ul { -->
<!--   margin-top: 0; -->
<!-- } -->
<!-- </style> -->

I learned some category theory and abstract algebra in Math 55a at Harvard so I thought I'd take a shot at trying to understand and explain this infamous sentence, starting with the definition of each word.

**<u>Definition</u> (Monad).** A *monad* is a monoid in the category of endofunctors. :D {{< p_qed >}}

**<u>Definition</u> (Monoid).** A *monoid* is a set \(S\) together with a binary operator \(m:S\times S\to S\) called the *law of composition* satisfying two axioms (using the shorthand for the law of composition \(ab:=m(a,b)\) for all \(a,b\in S\)):
 - Identity. There exists an element \(s\in S\) such that \(ae=ea=a\) for all \(a\in S\).
 - Associativity. For all \(a,b,c\in S\), we have \((ab)c=a(bc)\). {{< ul_qed >}}

(If a monoid satisfies the inverse axiom, then it is also a *group*: for all \(a\in S\), there exists an *inverse* \(b\in S\) such that \(ab=ba=e\).)

**<u>Definition</u> (Category).** A *category* \(\newcommand{\CC}{\mathcal C}\CC\) consists of:
- A collection of *objects* \(\DeclareMathOperator{\Ob}{Ob}\Ob(\CC)\).
- For every two objects \(A,B\in\Ob(\CC)\), a collection of structure preserving maps called *morphisms* \(\DeclareMathOperator{\Hom}{Hom}\Hom(A,B)\). The set of morphisms is called the *hom-set*.
- A partial binary operator called *composition* between morphisms. For all objects \(A,B,C\in\Ob(\CC)\), a composition between morphisms:
\[
    \circ:\Hom(A,B)\times\Hom(B,C)\to\Hom(A,C)
\]
If \(f\in\Hom(A,B)\) and \(g\in\Hom(B,C)\), we have that the codomain of \(f\) is the domain of \(g\), so we have another morphism \(g\circ f\in\Hom(A,C)\). There does not necessarily exist a composition between any two morphism.

Composition must satisfy two axioms:
- Associativity. Let \(A,B,C,D\in\Ob(\CC)\) and let \(f\in\Hom(A,B)\), \(g\in\Hom(B,C)\) and \(h\in\Hom(C,D)\). Then: \((h\circ g)\circ f=h\circ(g\circ f)\).
- Identity. For every \(A\in\Ob(\CC)\), there exists an identity morphism \(\DeclareMathOperator{\id}{id}\id_A\in\Hom(A,A)\) such that for all objects \(B\in\Ob(\CC)\) and morphisms \(f\in\Hom(A,B)\), we have
\(f\circ\id_A=f=\id_B\circ f\). {{< ul_qed >}}

**<u>Example</u> (Category of finite-dimensional vector spaces).** Let \(\DeclareMathOperator{\Vect}{Vect} \Vect_k\) be the category of finite-dimensional vector spaces over a field \(k\). The set of morphisms between any two objects \(V,W\in\Ob(\Vect_k)\) is the set of linear map/homomorphisms: \(\DeclareMathOperator{\LL}{\mathcal L} \Hom(V,W)=\LL(V,W)\) (using notation from Axler, LADR). \(\Hom(V,W)\) is not the set of all functions \(V\to W\) because morphisms preserve structure.

Let \(U,V,W\in\Ob(\Vect_k)\). Composition between two linear maps \(f:U\to V\), \(g:V\to W\) is simply given by standard function composition, which is associative. The identity \(\id_U\) on any vector space \(U\) is simply the map \(\id_U:u\mapsto u\) for all \(u\in U\). {{< p_qed >}}

**<u>Definition</u> (Functor).**

**<u>Definition</u> (Endofunctor).** An *endofunctor* is a functor from a category \(\CC\) to the same category \(\CC\).

**<u>Example</u> (Polynomial functor).**


