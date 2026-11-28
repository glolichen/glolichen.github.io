---
title: A Monad is a Monoid in the Category of Endofunctors (WIP)
author: 'Jayden Li'
date: "2026-11-28"
---

**In progress!**

I learned some category theory and abstract algebra in Math 55a at Harvard, so here's (trying to) understand the [Infamous](https://www.reddit.com/r/ProgrammerHumor/comments/1ctgnng/amonadisamonoidinthecategoryofendofunctors/) [Sentence](https://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.html).

**<u>Definition</u> (Monad).** A *monad* is a monoid in the category of endofunctors. {{< p_qed >}}

Actually, this isn't the most precise definition. I assumed "monoid" meant a monoid in abstract algebra, which it isn't.[^monoid] We might also ask: ... the category of endofunctors of *what*? So, we have the more precise definition of a monad below:

[^monoid]: In abstract algebra, a *monoid* is a set \(S\) together with a binary operator \(m:S\times S\to S\) called the *law of composition* satisfying two axioms (using the shorthand for the law of composition \(ab:=m(a,b)\) for all \(a,b\in S\)):
     - Identity. There exists an element \(e\in S\) such that \(ae=ea=a\) for all \(a\in S\).
     - Associativity. For all \(a,b,c\in S\), we have \((ab)c=a(bc)\).

    (If a monoid satisfies the inverse axiom, then it is also a *group*: for all \(a\in S\), there exists an *inverse* \(b\in S\) such that \(ab=ba=e\).) As we'll see, the a *monoid object* in category theory is a generalization of a monoid defined above.

**<u>Definition</u> (Monad).** A *monad* in a category \(\newcommand{\CC}{\mathcal C} \CC\) is a *monoid object* in the *monoidal category* of endofunctors of \(\CC\). {{< p_qed >}}

Now, onto defining the terms in this definition.

## Categories and Functors

**<u>Definition</u> (Category).** A *category* \(\CC\) consists of:
- A collection of *objects* \(\DeclareMathOperator{\Ob}{Ob}\Ob(\CC)\).
- For every two objects \(A,B\in\Ob(\CC)\), a collection of structure preserving maps called *morphisms* \(\DeclareMathOperator{\Hom}{Hom}\Hom(A,B)\). This set of morphisms is called the *hom-set*. We write \(f:A\to B\) to mean a morphism from \(A\) to \(B\), and \(f\in\Hom(A,B)\).
- A partial binary operator called *composition* between morphisms. For all objects \(A,B,C\in\Ob(\CC)\), a composition between morphisms:
\[
    \circ:\Hom(A,B)\times\Hom(B,C)\to\Hom(A,C)
\]
If \(f\in\Hom(A,B)\) and \(g\in\Hom(B,C)\), we have that the codomain of \(f\) is the domain of \(g\), so we have another morphism \(g\circ f\in\Hom(A,C)\). There does not necessarily exist a composition between any two morphism.

Composition must satisfy two axioms:
- Associativity. Let \(A,B,C,D\in\Ob(\CC)\) and let \(f\in\Hom(A,B)\), \(g\in\Hom(B,C)\) and \(h\in\Hom(C,D)\). Then: \((h\circ g)\circ f=h\circ(g\circ f)\).
- Identity. For every \(A\in\Ob(\CC)\), there exists an identity morphism \(\DeclareMathOperator{\id}{id}\id_A\in\Hom(A,A)\) such that for all objects \(B\in\Ob(\CC)\) and morphisms \(f\in\Hom(A,B)\), we have
\(f\circ\id_A=f=\id_B\circ f\). {{< ul_qed >}}

Important note: thoughout this article, we use the following notation for different "identities":
- \(\id_X:X\to X\) for the identity morphism, where \(X\) is an object.
- \(\DeclareMathOperator{\Id}{Id}\Id_\CC:\CC\to\CC\) for the identity functor, where \(\CC\) is a category.
- \(1_F:F\Rightarrow F\) for the identity natural transformation, where \(F\) is a functor.
We introduce the last two later.

<details open>
<summary>Example: Category of finite-dimensional vector spaces</summary>

Let \(\DeclareMathOperator{\Vect}{Vect} \Vect_k\) be the category of finite-dimensional vector spaces over a field \(k\). The set of morphisms between any two objects \(V,W\in\Ob(\Vect_k)\) is the set of linear map/homomorphisms: \(\DeclareMathOperator{\LL}{\mathcal L} \Hom(V,W)=\LL(V,W)\) (using notation from Axler, LADR). \(\Hom(V,W)\) is not the set of all functions \(V\to W\) because morphisms preserve structure.

Let \(U,V,W\in\Ob(\Vect_k)\). Composition between two linear maps \(f:U\to V\), \(g:V\to W\) is simply given by standard function composition, which is associative. The identity \(\id_U\) on any vector space \(U\) is simply the map \(\id_U:u\mapsto u\) for all \(u\in U\). {{< p_qed >}}
</details>

**<u>Definition</u> (Functor).** A *functor* is a map of categories that preserve structure. Let \(\newcommand{\DD}{\mathcal D}\CC,\DD\) be categories; a functor \(F:\CC\to \DD\) is a map satisfying the following:
- Each object \(A\in \Ob(\CC)\) is associated to an object \(F(A)\in \Ob(\DD)\)
- Each morphism \(f\in\Hom(X,Y), f:X\to Y\) (where \(X,Y\in\Ob(\CC)\)) is associated to a morphism \( F(f)\in\Hom(F(X),F(Y)), F(f):F(X)\to F(Y) \).

A functor \(F\) must preserve the structure of categories by preserving the identity and the composition of morphisms:
- Recall that for each object \(A\in\Ob(\CC)\), there exists an identity morphism \(\id_A\in\Hom(A,A)\) such that \(\id_A(a)=a\) for all \(a\in A\). The identity is preserved: \(F(\id_A)=\id_{F(a)}\), the identity morphism of the object \(F(A)\in\Ob(\DD)\).
- Let \(f\in\Hom(A,B)\) and \(g\in\Hom(B,C)\) be morphisms. By definition of the composition operator, \(g\circ f\in\Hom(A,C)\) and is also a functor. Then \(F(g\circ f)\) is a functor from \(F(A)\) to \(F(C)\). Likewise, \(F(f):\Hom(F(A),F(B))\) and \(F(g):\Hom(F(B),F(C))\), so their composition \(F(g)\circ F(f)\) is a functor from \(F(A)\) to \(F(C)\). Functors preserve the composition of morphisms, so we require \(F(g\circ f)=F(g)\circ F(f)\). {{< ul_qed >}}

**<u>Definition</u> (Endofunctor).** An *endofunctor* is a functor from a category \(\CC\) to itself. \(\DeclareMathOperator{\End}{End}\End(\CC)\) is the collection[^setvsclass] of all endofunctors of \(\CC\): \(\End(C)=\{F:\CC \to \CC\}\). {{< p_qed >}}

Two claims: that \(\End(\CC)\) can itself be given a structure of not just a category, but of what's called a *monoidal category*, so \(\End(\CC)\) is the "monoidal category of endofunctors of \(C\)." First, we define a monoidal category.
[^setvsclass]: \(\End(C)\) is not always be a set, because it might be too big. For example, consider the category \(\CC\) of all sets. We have an embedding of all sets (i.e. objects of \(\CC\)) to an endofunctor of \(\CC\) as such: for all \(A\in\Ob(\CC)\), let \(K_A:\CC\to\CC\) be a mapping that maps all objects of \(\CC\) to the set/object \(A\), and all morphisms to \(\id_A\). \(K_A\) satisfies the requirements of a functor (preservation of composition and identity), and is thus an endofunctor. There is an associated endofunctor \(K_X\in\End(\CC)\) for each object in \(\CC\), so we have an embedding of every set in \(\End(C)\). But, a set cannot contain all sets (or have an embedding of all sets), so \(\End(C)\) is not a set. In this case, \(\End(C)\) is in fact a *proper class*. For "small" categories \(\CC\), however, \(\End(\CC)\) is actually a set.

## Monoidal Categories

**<u>Definition</u> (Product category).** Let \(\CC,\DD\) be categories. The *product category* \(\CC\times\DD\) is similar to the notion of a Cartesian product of sets.
- The objects of \(\CC\times\DD\) are pairs of objects \((A,B)\) with \(A\in\Ob(\CC)\) and \(B\in\Ob(\DD)\), i.e. \(\Ob(\CC\times\DD)=\Ob(\CC)\times\Ob(\DD)\), where the \(\times\) on the right hand side is the Cartesian product of sets.
- A morphism from \((A,B)\in \Ob(\CC\times\DD)\) to \((A',B')\in \Ob(\CC\times\DD)\) is a pair, consisting of a morphism in \(\CC\) from \(A\) to \(A'\) and a morphism in \(\DD\) from \(B\) to \(B'\). Therefore, the hom-set in the product category is: \(\Hom_{\CC\times\DD}((A,B),(A',B')) = \Hom_{\CC}(A,A') \times \Hom_{\DD}(B,B')\); again, the right hand side uses the Cartesian product.
- The composition of two morphisms \((f,g)\) and \((f',g')\) is given by pairwise composition of morphisms in categories \(\CC\) and \(\DD\): \((f,g)\circ(f',g')=(f\circ_\CC f', g\circ_\DD g')\).
- The identity morphism associated with the object \((A,B)\in\Ob(\CC\times\DD)\) is the pair \(\id_A,\id_B\), which are the identity morphisms in the respective categories \(\CC\) and \(\DD\).

In short: the objects of \(\CC\times\DD\) is the Cartesian product of the objects of both categories, and morphisms are defined naturally as pairs. {{< p_qed >}}

**<u>Definition</u> (Bifunctor).** A *bifunctor* is a functor whose domain is a product category \(\CC\times\CC\), where \(\CC\) is a category. A bifunctor \(\Phi:\CC\times\CC\to\CC\) sends pairs of objects \(A,B\in\Ob(\CC)\) (alternatively, an object in the product category \(\CC\times\CC\)) to an object \(\Phi(A,B)\in\Ob(\CC)\), and pairs of morphisms \(f:A\to A', g:B\to B'\) to a morphism:
\[
    \Phi(f,g):\Phi(A,B)\to\Phi(A',B').
\]
Let \(A,A',\overline A,B,B',\overline B\in\Ob(\CC)\) be objects, and let the following be morphisms:
\begin{alignat*}{2}
    f&:A\to A' &\qquad
    g&:B\to B'  \\\\
    f'&:A'\to\overline A &\qquad
    g'&:B'\to\overline B
\end{alignat*}
\(\Phi\) satisfies the following laws:
- Let \(\id_A:A\to A\) and \(\id_B:B\to B\) be the identity morphisms. Then \(\Phi(\id_A,\id_B)=\id_{\Phi(A,B)}\).
- \(\Phi\left(f\circ f', g\circ g'\right)=\Phi(f,g)\circ\Phi(f',g')\). {{< ul_qed >}}

This definition is motivated by the fact that we want a functor from a product category \(\CC\times\CC\). Objects, morphisms and identity morphisms in a product category are all pairs, and composition is defined as componentwise composition in \(\CC\). The requirements above are equivalent to the identity and composition axioms of a regular functor applied on pairs of objects and morphisms in \(\CC\times\CC\).

**<u>Definition</u> (Monoidal category).** A *monoidal category* is a category \(\CC\) together with the following to be defined:
- A bifunctor \(\otimes:\CC\times\CC\to\CC\) called the *tensor product*. 
- A *unit object* \(I\in\Ob(\CC)\).
- "Associativity": let \(X,Y,Z\in\Ob(\CC)\). By definition of the tensor product, \((X\otimes Y)\otimes Z\) and \(X\otimes(Y\otimes Z)\) are both objects in \(\CC\). The requirement here is that they are isomorphic. More specifically, to define a monoidal category, we must define a natural isomorphism (invertible morphism in \(\CC\)) between these for all \(X,Y,Z\). This isomorphism is called the *associator*: \(\alpha_{X,Y,Z}\) is an isomorphism such that \(\alpha_{X,Y,Z}:(X\otimes Y)\otimes Z \xrightarrow{\sim} X\otimes(Y\otimes Z)\). 
- For each object \(X\in\Ob(\CC)\), we must define a *left unitor* \(L_X\) and a *right unitor* \(R_X\), which are natural isomorphisms: \(L_X:I\otimes X\xrightarrow{\sim} X\) and \(R_X:X\otimes I\xrightarrow{\sim} X\).

And also, the associator and unitor natural maps need to make the following diagrams commute below. For all \(W,X,Y,Z\in\Ob(\CC)\):
\[\begin{CD}
((W\otimes X)\otimes Y)\otimes Z @>{\alpha_{W\otimes X,Y,Z}}>> (W\otimes X)\otimes(Y\otimes Z) \\
@V{\alpha_{W,X,Y}\otimes\mathrm{id}_Z}VV @VV{\alpha_{W,X,Y\otimes Z}}V \\
(W\otimes(X\otimes Y))\otimes Z @. W\otimes(X\otimes(Y\otimes Z)) \\
@V{\alpha_{W,X\otimes Y,Z}}VV @| \\
W\otimes((X\otimes Y)\otimes Z) @>>{\mathrm{id}_W\otimes\alpha_{X,Y,Z}}> W\otimes(X\otimes(Y\otimes Z))
\end{CD}\]
This is called the *pentagon axiom*, and it says that the two ways of reparenthesizing \(((W\otimes X)\otimes Y)\otimes Y\) result in the same object in \(W\otimes(X\otimes (Y\otimes Z))\). The other constraint is called the *triangle axiom*:
\[
\begin{CD}
(X\otimes I)\otimes Y @>{\alpha_{X,I,Y}}>> X\otimes(I\otimes Y) \\
@V{R_X\otimes\mathrm{id}_Y}VV @VV{\mathrm{id}_X\otimes L_Y}V \\
X\otimes Y @= X\otimes Y
\end{CD}
\]
Similiarly, this says that you can eliminate the unit object \(I\) directly with the unitor, or you can reparenthesize \((X\otimes I)\otimes Y\) with the associator and then eliminate the identity. Both must give the same outcome.{{< p_qed >}}

Note that some of the properties above are about "natural isomorphisms." They are not equal! For example, the category of sets, with the bifunctor being the Cartesian product, is a monoidal category.[^trust] If \(X,Y,Z\) are sets, \((X\times Y)\times Z\) contain elements of the form \(((x,y), z)\), but \(X\times(Y\times Z)\) contains \((x,(y,z))\). They are not equal. But, there is a natural (and very obvious) isomorphism \((X\times Y)\times Z\xrightarrow{\sim} X\times (Y\times Z)\): the associator would be \(\alpha_{X,Y,Z}: ((x,y),z) \mapsto (x,(y,z)) \). But, since we are interested in structure, being isomorphic means they have the same structure and the two collections are "the same" up to renaming the elements, so they are good as equal.
[^trust]: trust me bro

Ok, this is quite a complicated definition. It may be helpful to look at some examples below.

<!-- <details open> -->
<!-- <summary>Example: Tensor product of vector spaces makes \(\Vect_k\) a monoidal category</summary> -->
<!---->
<!-- Recall \(\Vect_k\), the category of finite-dimensional vector spaces over a field \(k\), and the ordinary tensor product of vector spaces. I claim that the tensor product on vector spaces gives \(\Vect_k\) the structure of a monoidal category.  -->
<!---->
<!-- #### Define tensor product of objects/vector spaces -->
<!---->
<!-- There are a number of equivalent definitions of the tensor product \(V\otimes W\) of two vector spaces \(V,W\in\Vect_k\), but we focus on these two: -->
<!-- 1. Let \(v_1,\ldots,v_m\) be a basis for \(V\) and let \(w_1,\ldots,w_n\) be a basis for \(W\). The tensor product \(V\otimes W\) is the vector space with basis \(v_i\otimes w_j\) for all \(1\leq i\leq m,1\leq j\leq n\). If \(v=\sum_{i=1}a_iv_i\in V\) and \(w=\sum_{j=1}b_jw_j\in W\) (linear combination of basis vectors), then we define a pure tensor \(v\otimes w\) as: -->
<!-- \[ v\otimes w=\left(\sum_{i=1}^{m}a_iv_i\right)\otimes\left(\sum_{j=1}^{n}b_jw_j\right)=\sum_{i=1}^{m}\sum_{j=1}^{n} a_i b_j (v_i\otimes w_j) \] -->
<!-- Not all elements of \(V\otimes W\) are pure tensors, but all are linear combinations of pure tensors, by definition of span. The rank of an element of \(V\otimes W\) is the smallest \(\ell\) such that it can be expressed as the sum of \(\ell\) pure tensors. -->
<!---->
<!-- Notice that this definition of a pure tensor makes the tensor product *bilinear*. That is, if \(v,v'\in V\), \(w,w'\in W\) and \(\lambda\in k\) is a scalar (in the underlying field \(k\)), then: -->
<!-- \begin{align*} -->
<!--     &\left\\{\begin{aligned} -->
<!--         (v+v')\otimes w -->
<!--         &=v\otimes w+v'\otimes w \\\\ -->
<!--         v\otimes(w+w') -->
<!--         &=v\otimes w+v\otimes w' \\\\ -->
<!--         (\lambda v)\otimes w -->
<!--         &=\lambda(v\otimes w) \\\\ -->
<!--         v\otimes (\lambda w) -->
<!--         &=\lambda(v\otimes w) -->
<!--     \end{aligned}\right. \\\\ -->
<!--     \iff -->
<!--     {}&\left\\{\begin{aligned} -->
<!--         (v+v')\otimes w -->
<!--         -v\otimes w-v'\otimes w &= 0\\\\ -->
<!--         v\otimes(w+w') -->
<!--         -v\otimes w-v\otimes w' &= 0\\\\ -->
<!--         (\lambda v)\otimes w -->
<!--         -\lambda(v\otimes w) &= 0\\\\ -->
<!--         v\otimes (\lambda w) -->
<!--         -\lambda(v\otimes w) &= 0 -->
<!--     \end{aligned}\right. -->
<!-- \end{align*} -->
<!-- Notice that the right hand side of \(\iff\) above give the *relations* that the vector space \(V\otimes W\) must satisfy. This gives rise to our second definition. -->
<!---->
<!-- 2. Let \(U\) be a vector spaced spanned by the set \(\{ v\otimes w \mid v\in V,w\in W \}\), i.e. \(U\) is the vector space consisting of *all* linear combination of all symbols \(v\otimes w\). Then, let \(U_0\) be the vector space spanned by the relations the tensor product satisfies (i.e. \((v+v')\otimes w-v\otimes w-v'\otimes w\) and other expressions above), for all \(v\in V\) and \(w\in W\). Then, by the properties of a quotient vector space, \(U/U_0\) will satisfy all the relations we want, so we define \(V\otimes W=U/U_0\). -->
<!-- \[ -->
<!--     U_0=\operatorname{span} \left\{\left.\begin{gathered} -->
<!--         (v+v')\otimes w -->
<!--         -v\otimes w-v'\otimes w \\ -->
<!--         v\otimes(w+w') -->
<!--         -v\otimes w-v\otimes w' \\ -->
<!--         (\lambda v)\otimes w -->
<!--         -\lambda(v\otimes w) \\ -->
<!--         v\otimes (\lambda w) -->
<!--         -\lambda(v\otimes w) -->
<!--     \end{gathered} -->
<!--     \right| \begin{gathered} -->
<!--         v\in V \\ w\in W \\ \lambda\in k -->
<!--     \end{gathered}\right\} -->
<!-- \] -->
<!---->
<!-- We are going to see that the category of vector spaces \(\Vect_k\) can be given the structure of a monoidal category with the tensor product as the bifunctor. A bifunctor must associate to each pair of objects \(V,W\in\Ob(\Vect_k)\) (the objects of the category \(\Vect_k\) are vector spaces) another object/vector space. The tensor product does so my mapping the pair \((V,W)\) with \(V,W\in\Ob(\Vect_k)\) to the vector space \(V\otimes W\).  -->
<!---->
<!-- #### Define tensor product of morphisms/linear maps -->
<!---->
<!-- But a bifunctor must also map pairs of morphisms (in \(\Vect_k\), this would be pairs of linear maps) to another morphism/linear map. Specifically, let \(V,V',W,W'\in\Ob(\Vect_k)\) be vector spaces, and let \(T\in\Hom(V,V')=\LL(V,V')\) and \(S\in\Hom(W,W')=\LL(V,V')\) be morphisms/linear maps. In order for \(\otimes\) to be a bifunctor, we want \(T\otimes S\) to be a morphism from \(V\otimes W\) to \(V'\otimes W'\). There is a natural way to define this: define the tensor product of linear maps as \(T\otimes S:v\otimes w\mapsto T(v)\otimes S(w)\in V'\otimes W'\). -->
<!---->
<!-- There are a few concerns with this definition. First, a pure tensor might have multiple names, and the map is only well-defined if all such names map to the same output. For example, \(v\otimes 2w=2v\otimes w\), and we want \(T(v)\otimes S(2w)=T(2v)\otimes S(w)\). -->
<!---->
<!-- How do we ensure this is the case? Notice that \(T\otimes S:V\otimes W \to V'\otimes W'\). Instead, we define a new linear map \(\phi:U\to V'\otimes W'\) (recalling that \(V\otimes W=U/U_0\)), and like \(T\otimes S\) this new map is given by \(\phi(v\otimes w)=T(v)\otimes S(w)\). Recall that \(U\) is the space with basis all objects \(v\otimes w\) for all \(v\) and \(w\), the definition of \(\phi\) previously is really a definition of the linear map \(\phi\) on the basis vectors, the \(v\otimes w\)'s. -->
<!---->
<!-- We want \(\phi\) to satisfy the following condition: let \(v_1\otimes w_1, v_2\otimes w_2 \in U\). If these are the same in the quotient space \(U/U_0\) (i.e. they both belong to the same coset[^coset] of \(U_0\)), then they are different names for the same pure tensor in \(V\otimes W\). Then, we should have: -->
<!-- \[ -->
<!--     \phi(v_1\otimes w_1)=\phi(v_2\otimes w_2) -->
<!--     \iff -->
<!--     \phi(v_1\otimes w_1)-\phi(v_2\otimes w_2)=0 -->
<!-- \] -->
<!-- We defined \(\phi\) to be a linear map \(U\to V'\otimes W'\), so by linearity: -->
<!-- \[ -->
<!--     \phi(v_1\otimes w_1)=\phi(v_2\otimes w_2) -->
<!--     \iff -->
<!--     \phi(v_1\otimes w_1-v_2\otimes w_2)=0 -->
<!-- \] -->
<!---->
<!-- If \(v_1\otimes w_1=v_2\otimes w_2\) in the quotient space \(U/U_0\), then (proven in footnote) \(v_1\otimes w_1-v_2\otimes w_2\in U_0\). To prove the above statement, it suffices to show that \(\phi(u)=0\) for all \(u\in U_0\). By linearity of \(\phi\), \(\phi(u)=0\) for all \(u\) if \(\phi\) maps each basis vector/relation of \(U_0\) to \(0\). For example, for the relation \((v+v')\otimes w-v\otimes w-v'\otimes w\): -->
<!-- \begin{align*} -->
<!--     \phi(\ldots) -->
<!--     &=\phi((v+v')\otimes w)-\phi(v\otimes w)-\phi(v'\otimes w) \\\\ -->
<!--     &=T(v+v')\otimes S(w)- T(v)\otimes S(w)- T(v')\otimes S(w) \\\\ -->
<!--     &=(T(v)+T(v'))\otimes S(w)-T(v)\otimes S(w)-T(v')\otimes S(w) \\\\ -->
<!--     &=T(v)\otimes S(w)+T(v')\otimes S(w)-T(v)\otimes S(w)-T(v')\otimes S(w) \\\\ -->
<!--     &=0 -->
<!-- \end{align*} -->
<!-- Note \(v,v'\in V, w,w'\in W\). Likewise for the other 3 relations spanning \(U_0\). -->
<!---->
<!-- Ok, so we see that the definition of the linear map \(T\otimes S\) as \(v\otimes w\mapsto T(v)\otimes S(w)\) is well-defined. Note \(v\otimes w\in V\otimes W\) and \(T(v)\in V',S(w)\in W'\) so their tensor product is in \(V'\otimes W'\). Thus \(T\otimes S\) is a morphism \(V\otimes W\to V'\otimes W'\). Therefore, the tensor product \(\otimes\) also maps pairs of morphisms to morphisms. -->
<!---->
<!-- To summarize so far, the tensor product of vector spaces \(\otimes\) sends a pair of vector spaces \(V,W\in\Ob(\Vect_k)\) to another vector space \(V\otimes W\in\Ob(\Vect_k)\). And it maps pairs of morphisms/linear map \(T:V\to V'\) and \(S:W\to W'\) to a morphism/linear map \(T\otimes S : V\otimes W \to V'\otimes W'\). The tensor product on morphisms is given by: -->
<!-- \[ -->
<!--     (T\otimes S)(v\otimes w)=T(v)\otimes S(w) -->
<!-- \] -->
<!-- Now, we check that the tensor product \(\otimes\) satisfies the other requirements of a (bi)functor. Let \(V,W\in\Ob(\Vect_k)\). -->
<!---->
<!-- #### Check bifunctoriality -->
<!---->
<!-- Let \(\id_V:V\to V\) and \(\id_W:W\to W\) be the identity maps on \(V\) and \(W\), so that \(\id_V(v)=v\) and \(\id_W(w)=w\). Their tensor product is: -->
<!-- \[ -->
<!--     (\id_V\otimes\id_W)(v\otimes w) -->
<!--     =\id_V(v)\otimes\id_W(w) -->
<!--     =v\otimes w -->
<!--     =\id_{V\otimes W}(v\otimes w) -->
<!-- \] -->
<!-- If this holds for all pure tensors \(v\otimes w\), and all elements of \(V\otimes W\) are sums of pure tensors, we have \(id_{V\otimes W}(x)=(\id_V\otimes\id_W)(x)\) for all \(x\in V\otimes W\), so \(id_{V\otimes W}=\id_V\otimes\id_W\) and the tensor product preserves identities. -->
<!---->
<!-- A bifunctor must also preserve function composition. Let \(V,V',\overline V,W,W',\overline W\in\Ob(\Vect_k)\) be vector spaces. Let: -->
<!-- \begin{alignat*}{2} -->
<!--     T&:V\to V' &\qquad -->
<!--     S&:W\to W'  \\\\ -->
<!--     T'&:V'\to\overline V &\qquad -->
<!--     S'&:W'\to\overline W  -->
<!-- \end{alignat*} -->
<!-- be morphisms/linear maps. Composition by regular function composition gives: -->
<!-- \[ -->
<!--     T'\circ T:V\to\overline V -->
<!--     \qquad -->
<!--     S'\circ S:W\to\overline W -->
<!-- \] -->
<!-- The tensor product of \(T'\circ T\) and \(S'\circ S\) is a morphism \((T'\circ T) \otimes (S'\circ S):V\otimes W \to \overline V \otimes \overline W\). -->
<!---->
<!-- The tensor product on morphisms on \(T,S,T',S'\) gives: -->
<!-- \begin{align*} -->
<!--     T\otimes S&:V\otimes W \to V'\otimes W' \\\\ -->
<!--     T'\otimes S'&:V'\otimes W' \to \overline V\otimes \overline W -->
<!-- \end{align*} -->
<!-- These can be composed. The tensor product \(\otimes\) preserves function composition if: \((T'\otimes S')\circ(T\otimes S) = (T'\circ T) \otimes (S'\circ S)\). Note that their domains are both \(V\otimes W\) and their codomains are \(\overline V \otimes\overline W\). Check that their outputs are equal for all pure tensors \(v\otimes w\in V\otimes W\): -->
<!-- \begin{align*} -->
<!--     \left((T'\otimes S')\circ(T\otimes S)\right)(v\otimes w) -->
<!--     &=(T'\otimes S')((T\otimes S)(v\otimes w)) \\\\ -->
<!--     &=(T'\otimes S')(T(v)\otimes S(w)) \\\\ -->
<!--     &=T'(T(v))\otimes S'(S(w)) \\\\ \\\\ -->
<!--     \left((T'\circ T) \otimes (S'\circ S)\right)(v\otimes w) -->
<!--     &=(T'\circ T)(v)\otimes (S'\circ S)(w) \\\\ -->
<!--     &=T'(T(v))\otimes S'(S(w)) -->
<!-- \end{align*} -->
<!-- (Follows from the definition \((T\otimes S)(v\otimes w)=T(v)\otimes S(w)\).) Since this holds for all pure tensors it holds for all elements of \(V\otimes W\). Thus \((T'\otimes S')\circ(T\otimes S) = (T'\circ T) \otimes (S'\circ S)\) and the tensor product of vector spaces preserves composition. -->
<!---->
<!-- #### Define unit object -->
<!---->
<!-- The unit object is the underlying field \(k\in\Ob(\Vect_k)\) (the underlying field can always be viewed as a 1-dimensional vector space). \(k\otimes V\) and \(V\otimes k\) are both naturally isomorphic to \(V\). To see this, let \(v_1,v_2,\ldots,v_n\) be a basis for the vector space \(V\). Any nonzero element \(a\in k\) is a basis for \(k\) (\(\operatorname{span}(a)\) is all scalar multiples of \(a\) by any scalar in \(k\), so the span is all of \(k\)). We notice that \(V\otimes k \cong k\otimes V\cong V\).[^tensorisom] Specifically we have the left and right unitors \(L_V:k\otimes V\xrightarrow{\sim} V\) and \(R_V:V\otimes k\xrightarrow{\sim} V\): -->
<!-- \begin{align*} -->
<!--     L_V: a\otimes v &\mapsto av \\\\ -->
<!--     R_V: v\otimes a &\mapsto av -->
<!-- \end{align*} -->
<!-- where \(a\in k, v\in V\) and \(av\) is by scalar multiplication. -->
<!---->
<!-- [^tensorisom]: \(V\otimes k\) has basis \(\{ v_i\otimes a \mid 1\leq i\leq n \}\) and \(k\otimes V\) has basis \(\{ a\otimes v_i \mid 1\leq i\leq n \}\), with \(a\in k, a\neq 0\) (so that \(a\) is a basis for \(k\) viewed as a vector space). Both have exactly \(n=\dim V\) basis vectors, so \(\dim V=\dim(V\otimes k)=\dim(k\otimes V)\). Since all are vector spaces over a field \(k\), and all vector spaces of the same dimension over the same field are isomorphic, the statement above is true. -->
<!---->
<!-- #### Define associator and unitors -->
<!---->
<!-- Let \(U,V,W\) be vector spaces over \(k\). We define the associator \(\alpha_{U,V,W}: (U\otimes V)\otimes W \xrightarrow{\sim} U\otimes(V\otimes W)\) naturally. Let \(u\in U,v\in V,w\in W\): -->
<!-- \[ -->
<!--     \alpha_{U,V,W}: (u\otimes v)\otimes w \mapsto u\otimes(v\otimes w) -->
<!-- \] -->
<!-- It's fairly obvious that the associator and unitors are injective: only \((0\otimes 0)\otimes 0\) maps to zero in \(u\otimes(v\otimes w)\), and likewise for the unitors if \(av=0\) then \(a=0\) or \(v=0\), so the tensor product \(a\otimes v=0\), and all kernels are trivial. Likewise they are both surjective by choosing the appropriate inputs. -->
<!---->
<!-- The associator reparenthesizes any expression as "expected," so it ought to satisfy the pentagon axiom. Likewise the unitors are such that we can swap any scalar/element of the unit object \(k\) as expected, so it satisfies the triangle axiom. Full proof: ask a toddler on the street. Therefore we see \(\Vect_k\) can be given the structure of a monoidal category. {{< p_qed >}} -->
<!-- </details> -->

[^coset]: The cosets of a subspace are parallel shifts of that subspace by a constant vector: if \(W\) is a subspace of \(V\), then for all \(v\in V\) we have a coset of \(W\): \(v+W=\{v+w \mid w\in W\}\). Two elements \(a,b\in U\) are in the same coset if and only if \(a+U_0\) and \(b+U_0\) the same coset. If \(a+U_0=b+U_0\), then for all \(r\in U_0\), there exists some \(r'\in U_0\) such that \(a+r=b+r_0 \implies a-b=r-r_0 \implies a-b\in U_0\). The converse is trivial.

    In \(U/U_0\), the cosets of \(U_0\) in the form \(a+U_0\) with \(a\in U\) is the subset of \(U\) that differ from \(a\) by an element of \(U_0\), i.e. \(a+U_0=\{a+u \mid u\in U_0\}\). Recall each of the relations used to define \(U_0\). We are saying that \(a+U_0\) is **the subset of \(V\) that equals \(a\) in \(V\otimes W=U/U_0\) by bilinearity**. For example, take \(a=(2v)\otimes (3w)\). Using the definition of the coset \(a+U_0\), and take:
\[\begin{aligned}
    u
    &=\underbrace{-1}_{\in k}\cdot \underbrace{((2v)\otimes (3w)-3((2v)\otimes w))}_{=(2v)\otimes (\lambda w)-\lambda((2v)\otimes w)}+\underbrace{(-3)}_{\in k}\cdot\underbrace{((2v)\otimes w-2(v\otimes w))}_{=(\lambda v)\otimes w-\lambda(v\otimes w)} \\
    &=\cancel{3((2v)\otimes w)}-(2v)\otimes (3w)+6(v\otimes w)-\cancel{3((2v)\otimes w)} \\
    &=6(v\otimes w)-(2v)\otimes (3w)
\end{aligned}\]
In the first line note that \(u\) is the linear combination (with weights \(-1\) and \(-3\)) of two expressions in the basis of \(U_0\) defined before (specifically, the last two). \(a+u=6(v\otimes w)\), so therefore \(6(v\otimes w)\) and \((2v)\otimes (3w)\) are in the same coset and are therefore in fact the same in \(V\otimes W=U/U_0\).

Returning to the set of endofunctors of a category, it turns out that \(\End(\CC)\) can be given the structure of a monoidal category. We introduce a definition that is useful to show this.

## Natural Transformations

**<u>Definition</u> (Natural transformation).** A *natural transformation* is a morphism between two functors. Let \(\CC,\DD\) be categories, and let \(F,G:\CC\to\DD\) be functors. A natural transformation \(\alpha:F\Rightarrow G\)[^notation] is the assignment from every object \(x\in\Ob(\CC)\) to a morphism \(\alpha_x:F(x)\to G(x)\) (\(\alpha_x\) is called the *component* of \(\alpha\) at the object \(x\)) in \(\DD\) (since \(F,G\) are functors, \(F(x),G(x)\in\Ob(\DD)\)), such that for all objects \(x,y\in\Ob(\CC)\) and morphisms \(\phi:x\to y\) in \(\CC\), the following diagram commutes:
\[
\begin{CD}
F(x) @> F(\phi) >> F(y) \\
@V \alpha_x VV @VV \alpha_y V \\
G(x) @>> G(\phi) > G(y)
\end{CD}
\]
[^notation]: (Not an exponent!) I think the notational convention here is to use \(\Rightarrow\) for natural transformations between functors, like \(\alpha\), but use \(\to\) for morphisms and functors.

Note: \(F(x),F(y),G(x),G(y)\in\Ob(\DD)\), and \(\alpha_x,\alpha_y,F(\phi),G(\phi)\) are morphisms in \(\DD\).

The diagram above commutes if and only if the following morphisms in \(\DD\) are equal: \(G(\phi)\circ \alpha_x\) and \(\alpha_y \circ F(\phi)\), and \(\circ\) is composition defined in \(\DD\). To put it simply, we require that \(\alpha\) makes the diagram commute in order for it to preserve the structure of categories \(\CC,\DD\) and that of its functors/morphisms. {{< p_qed >}}

<details open>
<summary>Example: Vector space to its double dual</summary>

Let \(V\) be a vector space over a field \(k\). Recall its dual space \(V^*\) is:
\[
    V^*=\{\text{linear maps } V\to k\}=\LL(V,k)
\]
The double dual \(V^{**}\) (also written \((V^*)^*\)) of \(V\) is the dual space of \(V^*\):
\[
    V^{**}=\{\text{linear maps } V^*\to k\}=\LL(V^*,k)
\]
\(V\) is naturally isomorphic to \(V^{**}\). Why?

We can do so by the evaluation map. We associate any \(v\in V\) to a map \(V^*\to k\) as such: for any \(\ell:V\to k, \ell\in V^*\), we map it to its value evaluated at \(v\) by \(\ell\mapsto \ell(v)\), which gives a map from \(V^*\) to \(k\). Intuitively, this map is natural because we do not need to "choose" anything. It is also injective and surjective. Thus \(V\) is naturally isomorphic to \(V^{**}\).

#### Define identity and double dual functors

In category theoretical terms: \(V,V^{**}\in\Ob(\Vect_k)\) (since the linear maps form a vector space). We have the *identity* functor \(\Id_{\Vect_k}:\Vect_k\to\Vect_k\) that maps each vector space \(V\) to itself, and maps each morphism/linear map \(T:V\to W\) to itself.

The double dual is also a functor \(\Phi\). It maps objects/vector space \(V\) to \(V^{**}\), obviously. If \(V,W\in\Ob(\Vect_k)\), then morphisms are linear maps \(T:V\to W\). \(\Phi\) should map \(T\) to a linear map in the double dual spaces \(V^{**}\to W^{**}\). We can do so by the *transpose* of linear maps.[^transpose] The first transpose of \(T\) is \(^tT:W^*\to V^*\), and the second transpose is \(^t(^t T):V^{**}\to W^{**}\). 

Specifically, if \(V,W\in\Ob(\Vect_k)\) and \(T:V\to W\) is a linear map, then the double dual functor \(\Phi\) does the following:
\[
    \Phi(V)=V^{**}
    \qquad
    \Phi(T)={} ^t(^t T)
\]
\(\Phi(T)={}^t(^tT)\) is not literally the same thing as \(T\), of course, as they are linear maps between different vector spaces. But, we have a natural association between them.

This satisfies the functor laws. Briefly, if \(\id_V\) is the identity morphism/linear map in \(V\), then \(\Phi(\id_V)=^t(^t \id_V)=\id_{V^{**}}\) by definition of the transpose. Likewise, for composition:
\[
    \Phi(S\circ T)
    ={}^t(^t(S\circ T))
    ={}^t(^tT\circ {}^tS)
    ={}^tS \circ {} ^tT
\]
So \(\Phi\) preserves both composition and the identity, so \(\Phi\) is indeed a functor.
[^transpose]: Let \(V,W\) be vector spaces over a field \(k\). For a linear map \(T:V\to W\), we can naturally define an induced map \(W^*\to V^*\) called the *transpose* \(^tT\). Let \(\ell:W\to k\) (so \(\ell\in W^*\)). We can map it to an element in \(V^*\) by composition: \(\ell\circ T:V\to k\). The map from \(\ell\in W^*\) to \(\ell\circ T\in V^*\) is the transpose \(^t T\). See the following diagram:
\[
\begin{CD}
V @> T >> W \\
@V {}\left(^tT\right)(\ell){}={}\ell\circ T VV @VV \ell V \\
k @= k
\end{CD}
\]
If \(A\) is the matrix representation of \(T:V\to W\) with respect to the basis \(v_1,\ldots,v_m\) for \(V\) and \(w_1,\ldots,w_n\) for \(W\), then the matrix representation of the transpose \(^tT:W^*\to V^*\) is the matrix transpose \(A^T\) with respect to the [dual basis](https://en.wikipedia.org/wiki/Dual_basis) \(w_1^*,\ldots,w_n^*\) for \(W^*\) and \(v_1^*,\ldots,v_m^*\) for \(V^*\). Because of, uhh, [reasons](https://math.stackexchange.com/a/1138700).

#### Define natural transformation \(\Id_{\Vect_k}\Rightarrow\Phi\)

We can define the natural transformation \(\alpha\) between functors \(\Id_{\Vect_k}\) and \(\Phi\) as such: for each object/vector space \(V\in\Ob(\Vect_k)\), define the component \(\alpha_V:\Id_{\Vect_k}(V)\to\Phi(V)\) (i.e. \(V\to V^{**}\)) as follows, for \(v\in V\):
\[
    \alpha_V(v)=(\underbrace{\ell}_{V^*} \mapsto \underbrace{\ell(v)}_{k}) \in V^{**}
\]
i.e. the evaluation map with \(\ell:V\to k\) as *any arbitrary* element of \(V^*\). We do not need to make a choice for \(\ell\). We need to check: \(\alpha_V\) is indeed a morphism/linear map in \(V^{**}\), and it makes the diagram in the definition commute.

By definition, each component \(\alpha_V\) must be a morphism in \(V^{**}\), i.e. a linear map. Indeed \(\alpha_V\in V^{**}\), so it remains to check whether it is linear. Let \(v,v'\in V\):
\begin{align*}
    \alpha_V(v+v')
    &=(\ell\mapsto \ell(v+v')) 
    =(\ell\mapsto (\ell(v)+\ell(v'))) \\\\
    &=(\ell\mapsto\ell(v))+(\ell\mapsto\ell(v'))
    =\alpha_V(v)+\alpha_V(v')
\end{align*}
Since \(\ell\in V^*\) is linear. The sum of elements in the dual space is defined as the pointwise sum, and scalar multiples are defined as pointwise scalar multiples. Let \(\lambda\in k\) be a scalar value:
\begin{align*}
    \alpha_V(\lambda v)
    &=(\ell\mapsto \ell(\lambda v)) 
    =(\ell\mapsto (\lambda\ell(v))) \\\\
    &=\lambda(\ell\mapsto\ell(v))
    =\lambda\alpha_V(v)
\end{align*}
So \(\alpha_V\) is indeed linear, so it is a morphism in \(V^{**}\).

#### Check natural transformation axiom

Since we claim to have a natural transformation \(\Id_{\Vect_k}\Rightarrow\Phi\) we want the following diagram to commute (since \(\Phi(V)=V^{**}\) and likewise for \(W\)):
\[
\begin{CD}
V @> T >> W \\
@V \alpha_{V} VV @VV \alpha_W V \\
V^{**} @>> \Phi(T) > W^{**}
\end{CD}
\]
where \(V,W\in\Ob(\Vect_k)\) are objects/vector spaces, \(T:V\to W\) is a morphism/linear map, and \(\Phi\) is the double dual functor defined previously. Recall \(\Phi(T)={}^t(^tT):V^{**}\to W^{**}\), the double transpose of \(T\).

We check that \(\Phi(T)\circ \alpha_V=\alpha_W\circ T\). Clearly, both are maps \(V\to W^{**}\). Let \(v\in V\).
\[
    (\alpha_W\circ T)(v)
    =\alpha_W(T(v))
    =(\ell\mapsto \ell(T(v)))
\]
Note that unlike before, \(\ell\in W^*\) (instead of \(V^*\)). The above is indeed defined because \(T(v)\in W\), so \(\ell(T(v))\in k\).

Note \((\Phi(T)\circ\alpha_V)(v)=\left(^t(^t T)\right)(\alpha_V(v))\). By definition, \(^t(^t T):V^{**}\to W^{**}\) and is given by \(\left(^t(^t T)\right)(\mu)=\mu\circ{}^t T\) with \(\mu\in V^{**}\) (this follows by taking the transpose of \(^tT\)). Since \(\alpha_V(v)\in V^{**}\):[^intertext]
\[
    (\Phi(T)\circ\alpha_V)(v)
    =\left(^t(^t T)\right)(\alpha_V(v))
    ={} \alpha_V(v)\circ{}^tT
\]
Check types: \(\Phi(T)\circ\alpha_V:V\to W^{**}\), so we expect the value above (which is the a map \(V\to W^{**}\) evaluated at \(v\in V\)) to be an element of \(W^{**}\), i.e. a map \(W^*\to k\). The input would be an input to \(^tT:W^*\to V^*\), which is \(W^*\) as expected. The output is the output of \(\alpha_V(v):V\to k\), which is \(k\). OK.
[^intertext]: I apologize for the lack of alignment of "\(=\)" in these calculations; it does bother me somewhat too. Apparently Hugo \(\LaTeX\) does not support `\intertext`, and I am too lazy to screw around with CSS to emulate it.

We know the composition above is an element of \(W^{**}\), or a map \(W^*\to k\). Interpret this as "given an element \(\ell\in W^*\), we first use \(^tT\) to map it to an element of \(V^*\), then evaluate that at \(v\in V\) by \(\alpha_V\)." To do this, we can write:
\[
    (\Phi(T)\circ\alpha_V)(v)
    =(\ell \mapsto \left(\left(^tT\right)(\ell)\right)(v) )
\]
Again, by definition of the transpose \(^tT:W^*\to V^*\), we have \(\left(^tT\right)(\ell)=\ell\circ T\in V^*\).
\[
    (\Phi(T)\circ\alpha_V)(v)
    =(\ell \mapsto (\ell\circ T)(v) )
    =(\ell \mapsto \ell(T(v)) )
\]
This is exactly \((\alpha_W\circ T)(v)\) from before, so we conclude that the diagram does indeed commute by our definitions of \(\alpha\) as the evaluation map and \(\Phi\) as the double transpose. Thus, we have demonstrated above a natural transformation \(\alpha:\Id_{\Vect_k} \Rightarrow \Phi\).

Last thing: notice that \(\Id_{\Vect_k}\) and \(\Phi\) are both endofunctors: \(\Id_{\Vect_k}\) obviously, but \(\Phi\) takes vector spaces \(V\) to double dual *vector spaces* \(V^{**}\), so in fact \(\Phi:\Vect_k\to\Vect_k\). As we'll see next we can give \(\End(\Vect_k)\) the structure of a category such that \(\alpha_V\) is a morphism between objects \(\Id_{\Vect_k}\) and \(\Phi\).
</details>
Now we have enough for the proof that \(\End(\CC)\) is a monoidal category.

## Monoidal Category of Endofunctors

**<u>Proof</u> (\(\End(\CC)\) is a monoidal category).** The objects of \(\End(\CC)\) are endofunctors \(F:\CC\to\CC\). The morphisms are a bit more complex. What actually is a morphism? In familiar categories like the category of sets or \(\Vect_k\), morphisms are functions/linear maps between two sets/vector spaces. These would be natural ways to define a morphism in these particular categories. But morphisms of categories in general do not have to be maps. In general, the hom-set of two objects of a category is just a "thing that is provided when you define a category \(\CC\)," and the hom-sets collectively have some additional properties like identity and composition. Morphisms are just "things" in the hom-set; what they specifically are depends on the category.

For clarity, we will use \(\Hom_X\) to denote a hom-set in the category \(X\), since we will be working with \(\CC\) as well as the (supposed) category \(\End(\CC)\).

Let \(F,G\in\Ob(\End(\CC))\), so \(F,G:\CC\to\CC\) are endofunctors of \(\CC\). We define the morphisms as follows: the hom-set \(\Hom_{\End(\CC)}(F,G)\) is the set of natural transformations of functors \(F\Rightarrow G\). 

<details open>
<summary>Example: Other objects and endofunctors of \(\End(\Vect_k)\)</summary>

Previously, we defined the identity \(\Id_{\Vect_k}\) and double dual \(\Phi\) endofunctors on \(\Vect_k\). If \(V\) is a vector space over a field \(k\) and \(V^{**}\) is its double dual, we defined a natural transformation \(\alpha:\Id_{\Vect_k}\Rightarrow\Phi\) with components \(\alpha_V(v)=(\ell \mapsto \ell(v))\) where \(\ell\in V^*\).

But this is not the only natural transformation we can choose. For example:
\[
    \alpha_{V,\lambda}(v)=(\ell \mapsto \ell(\lambda v))
\]
where \(\lambda\in k\) is a scalar. This will still make the diagram commute as part of the definition of a natural transformation. Actually, these (scalar multiples as above) are the *only* natural transformations from the identity to the double dual functor.

Since \(\Id_{\Vect_k},\Phi\) are endofunctors of \(\Vect_k\), the "scalar multiple evaluation maps" above are morphisms (elements of the hom-set) in \(\End(\Vect_k)\), but they are not the only ones. For example, we could have the direct sum and tensor product endofunctors (\(S\) and \(P\), respectively):
\begin{alignat*}{2}
    S(V)&=V\oplus V &\qquad
    S(T)&=T\oplus T  \\\\
    P(V)&=V\otimes V &\qquad
    P(T)&=T\otimes T 
\end{alignat*}
where \(T:V\to W\) is a linear map. \(S\) and \(P\) are additional objects in \(\End(\Vect_k)\). We have some other natural transformations such as \(\Id_{\Vect_k}\Rightarrow S,S\Rightarrow S,P\Rightarrow P\), among others, which are in turn morphisms in \(\End(\Vect_k)\).
</details>

Two things to check: the structure above makes \(\End(\CC)\) a category *and* a monoidal category.

### \(\End(\CC)\) is a Category

By definition of a category, we must have a composition operator satisfies the associativity and identity axioms. Let \(F,G,H\in\Ob(\End(\CC))\) be objects/endomorphisms. Recall that composition is the following partial operator:
\[
    \circ:\Hom(F,G)\times\Hom(G,H)\to\Hom(F,H)
\]
Let \(\alpha\in\Hom(F,G)\) and \(\beta\in\Hom(G,H)\) be morphisms of \(\End(\CC)\), i.e. natural transformations \(\alpha:F\Rightarrow G, \beta:G\Rightarrow H\). For all \(X,Y\in\Ob(\CC)\) and morphisms \(\phi:X\to Y\) in \(\CC\), we have the following commutative diagrams by definition of natural transformations: 
\[
\begin{CD}
F(X) @> F(\phi) >> F(Y) \\
@V \alpha_X VV @VV \alpha_Y V \\
G(X) @>> G(\phi) > G(Y)
\end{CD}
\qquad
\begin{CD}
G(X) @> G(\phi) >> G(Y) \\
@V \beta_X VV @VV \beta_Y V \\
H(X) @>> H(\phi) > H(Y)
\end{CD}
\]
Now we stack these diagrams as such, since they share the same line \(G(X) \xrightarrow{G(\phi)} G(Y)\). When we write \((\beta\circ\alpha)_X=\beta_X\circ\alpha_X\), by \(\circ\) in the right hand side we mean "composition of morphisms of the category \(\CC\)." Note that the components \(\beta_X,\alpha_X\) associate an object \(X\in\Ob(\CC)\) to a morphism in \(\CC\), so composing \(\beta_X\) and \(\alpha_Y\) uses composition defined in \(\CC\). We write \(*\) for composition in \(\CC\).
\[
\begin{CD}
F(X) @> F(\phi) >> F(Y) \\
@V \alpha_X VV @VV \alpha_Y V \\
G(X) @> G(\phi) >> G(Y) \\
@V \beta_X VV @VV \beta_Y V \\
H(X) @>> H(\phi) > H(Y)
\end{CD}
\implies
\phantom{\beta_X}
\begin{CD}
F(X) @> F(\phi) >> F(Y) \\
@V \beta_X * \alpha_X VV @VV \beta_Y * \alpha_Y V \\
H(X) @>> H(\phi) > H(Y)
\end{CD}
\]
Composition gives a natural transformation \(\beta\circ\alpha:F\Rightarrow H\), which we define as: for each object \(X\in\Ob(\CC)\), define the component of \(\beta\circ\alpha\) at \(X\) as \((\beta\circ\alpha)_X=\beta_X * \alpha_X\) (composition in \(\CC\)). Then, the diagram above becomes:
\[
\begin{CD}
F(X) @> F(\phi) >> F(Y) \\
@V (\beta\circ\alpha)_X VV @VV (\beta\circ\alpha)_Y V \\
H(X) @>> H(\phi) > H(Y)
\end{CD}
\]
Which is exactly the naturality square satisfied by a natural transformation \(\beta\circ\alpha\), so we have a valid definition for the composition of morphisms/natural transformations in \(\End(\CC)\).

To check that composition in \(\End(\CC)\) is associative, let \(F,G,H,K\in\Ob(\End(\CC))\) be endofunctors, and let \(\alpha:F\Rightarrow G, \beta:G\Rightarrow H, \gamma:H\Rightarrow K\) be natural transformations. For all objects \(X\in\Ob(\CC)\), the component at \(X\) is:
\begin{align*}
    ((\alpha\circ\beta)\circ\gamma)_X
    &=(\alpha\circ\beta)_X * \gamma_X
    =(\alpha_X * \beta_X) * \gamma_X \\\\
    (\alpha\circ(\beta\circ\gamma))_X
    &=\alpha_X * (\beta_X \circ \gamma_X)
    =\alpha_X * (\beta_X * \gamma_X)
\end{align*}
Since composition \(*\) in \(\CC\) is associative by definition, the above lines are equal, and composition \(\circ\) in \(\End(\CC)\) is also associative.

The other composition axiom to check is the existence of an identity. Specifically, for all \(F\in\Ob(\End(\CC))\), we need an identity morphism/natural transformation \(1_F:F\Rightarrow F\), such that for all \(G\in\Ob(\End(\CC))\) and natural transformations \(\alpha:F\Rightarrow G\), we have \(\alpha\circ 1_F = \alpha=1_G\circ \alpha\). This is accomplished simply by defining the components of \(1_F\) at \(X\in\Ob(\CC)\) as \((1_F)_X=\id_{F(X)}:F(X) \to F(X)\), the identity morphism \(X\) in \(\CC\) which must exist by definition of the category \(\CC\). This choice of \(1_F:F\Rightarrow F\) satisfies the identity axiom (proof omitted).

To summarize, we've defined composition of natural transformations \(\alpha,\beta\) as \((\alpha\circ\beta)_X=\alpha_X * \beta_X\), and found that it satisfies:
1. \(\alpha\circ\beta:F\Rightarrow H\) is also a natural transformation.
2. It is associative.
3. There exists an identity morphism, namely the identity natural transformation.

Thus, we conclude that \(\End(\CC)\) with objects endomorphisms and morphisms natural transformations indeed forms a category.

### \(\End(\CC)\) is a Monoidal Category

#### Define tensor product \(\otimes\)

Let \(F,G,F',G'\in\Ob(\End(\CC))\) be endofunctors of \(\CC\), and \(\alpha:F\Rightarrow F', \beta:G\Rightarrow G'\) are morphisms/natural transformations of functors. Define the tensor product/bifunctor \(\otimes\) on objects as \(F\otimes G=F\circ G\) (composition, as defined previously). By definition of a bifunctor, we want \(\alpha\otimes\beta: F\otimes G \Rightarrow F'\otimes G'\), which by definition of \(\otimes\) on objects is \(\alpha\otimes\beta:F\circ G \Rightarrow F'\circ G'\). In other words, we want the component of the natural transformation \(\alpha\otimes\beta\) at \(X\) to be a morphism \((F\circ G)(X)\to (F'\circ G')(X)\). Consider the following diagram:
\[
\begin{CD}
F(G(X)) @> F(\beta_X) >> F(G'(X)) \\
@V \alpha_{G(X)} VV @VV \alpha_{G'(X)} V \\
F'(G(X)) @>> F'(\beta_X) > F'(G'(X))
\end{CD}
\]
I claim this diagram commutes. Why? View \(\beta_X\) as a morphism: by definition of the natural transformation \(\beta\), its component \(\beta_X\) is a morphism \(\beta_X:G(X)\to G'(X)\). To see this more clearly, match each of the following symbols (in quotation marks) in the [definition of a natural transformation](#natural-transformations) to the following symbols here:
- "\(\phi\)" to \(\beta_X\).
- "\(x\)" to \(G(X)\), and therefore "\(\alpha_x\)" to \(\alpha_{G(X)}\).
- "\(y\)" to \(G'(X)\), and therefore "\(\alpha_y\)" to \(\alpha_{G'(X)}\).
- "\(F\)" to \(F\) and "\(G\)" to \(F'\).

And the diagram above easily follows from the fact that \(\alpha\) is a natural transformation. This tells us that if \(\alpha:F\Rightarrow F', \beta:G\Rightarrow G'\) we can define \(\alpha\otimes\beta:F\circ G\Rightarrow F'\circ G'\) in two equivalent ways:
\[
    (\alpha\otimes\beta)_X
    =\alpha_{G'(X)} \circ F(\beta_X)
    =F'(\beta_X) \circ \alpha_{G(X)}
\]
We choose to use the first one. Now we verify that \(\alpha\otimes\beta\) is in fact a natural transformation \(F\circ G \Rightarrow F'\circ G'\), i.e. it satisfies the naturality square, for all \(X,Y\in\Ob(\CC)\) and morphisms \(\phi:X\to Y\) in \(\CC\):
\[
\boxed{\;\begin{CD}
F(G(X)) @> F(G(\phi)) >> F(G(Y)) \\
@V (\alpha\otimes\beta)_X VV @VV (\alpha\otimes\beta)_Y V \\
F'(G'(X)) @>> F'(G'(\phi)) > F'(G'(Y))
\end{CD}}
\]
We want to show this diagram commutes. Since \(\alpha:F\Rightarrow F',\beta:G\Rightarrow G'\) are natural transformations:
\[
\begin{CD}
F(A) @> F(\psi) >> F(B) \\
@V \alpha_A VV @VV \alpha_B V \\
F'(A) @>> F'(\psi) > F'(B)
\end{CD}
\qquad
\begin{CD}
G(A) @> G(\psi) >> G(B) \\
@V \beta_A VV @VV \beta_B V \\
G'(A) @>> G'(\psi) > G'(B)
\end{CD}
\]
where \(A,B\in\Ob(\CC)\) and \(\psi:A\to B\) is a morphism in \(\CC\). Since \(G'\) is an endofunctor \(\CC\to\CC\), take \(A=G'(X), B=G'(Y)\), which are also objects in \(\CC\). Next, choose \(\psi=G'(\phi)\), which is a morphism in \(\CC\).
\[
\begin{CD}
F(G'(X)) @> F(G'(\phi)) >> F(G'(Y)) \\
@V \alpha_{G'(X)} VV @VV \alpha_{G'(Y)} V \\
F'(G'(X)) @>> F'(G'(\phi)) > F'(G'(Y))
\end{CD}
\]
Now we verify the naturality square for \(\alpha\otimes\beta\) above. By definition of \(\alpha\otimes\beta\) and the commutative diagram above:
\[\begin{align*}
    F'(G'(\phi))\circ(\alpha\otimes\beta)_X
    &=F'(G'(\phi))\circ\alpha_{G'(X)}\circ F(\beta_X) \\
    &=\alpha_{G'(Y)}\circ F(G'(\phi))\circ F(\beta_X) 
\end{align*}\]
Since \(F\) is a functor, it preserves composition of morphisms \(G'(\phi)\) and \(\beta_X\):
\[
    F'(G'(\phi))\circ(\alpha\otimes\beta)_X
    =\alpha_{G'(Y)}\circ F\left(G'(\phi)\circ \beta_X\right) 
\]
Now consider the naturality square for \(\beta:G\Rightarrow G'\) previously. Take \(\psi=\phi, A=X,B=Y\):
\[
\begin{CD}
G(X) @> G(\phi) >> G(Y) \\
@V \beta_X VV @VV \beta_Y V \\
G'(X) @>> G'(\phi) > G'(Y)
\end{CD}
\]
By this diagram, we have \(G'(\phi)\circ\beta_X=\beta_Y\circ G(\phi)\).
\[
    F'(G'(\phi))\circ(\alpha\otimes\beta)_X
    =\alpha_{G'(Y)}\circ F\left(\beta_Y\circ G(\phi)\right) 
\]
Again, since \(F\) is a functor and preserves composition:
\[
    F'(G'(\phi))\circ(\alpha\otimes\beta)_X
    =\alpha_{G'(Y)}\circ F(\beta_Y)\circ F(G(\phi))
\]
By definition of \(\alpha\otimes\beta\), we have \((\alpha\otimes\beta)_Y=\alpha(G'(Y))\circ F(\beta_Y)\)
\[
    F'(G'(\phi))\circ(\alpha\otimes\beta)_X
    =(\alpha\otimes\beta)_Y\circ F(G(\phi))
\]
Therefore, the \(\boxed{\text{boxed diagram}}\) before commutes, and \(\alpha\otimes\beta\) satisfies the naturality square. So \(\alpha\otimes\beta\) is a valid morphism in \(\End(\CC)\).

#### Check \(\otimes\) is a bifunctor

Let \(F,F',\overline F,G,G',\overline G\in\Ob(\CC)\) be endofunctors, and let:
\begin{alignat*}{2}
    \alpha&:F\Rightarrow F' &\qquad
    \beta&:G\Rightarrow G'  \\\\
    \alpha'&:F'\Rightarrow\overline F &\qquad
    \beta'&:G'\Rightarrow\overline G
\end{alignat*}
be morphisms/natural transformations. A bifunctor must satisfy the following laws:
- Let \(1_F:F\Rightarrow F\) and \(1_G:G\Rightarrow G\) be the identity morphisms/natural transformations. "Identity axiom": \(1_F\otimes1_F=1_{F\otimes G}\), which in turn is defined to be \(1_{F\otimes G}=1_{F\circ G}\).
- "Interchange axiom": \((\alpha\circ\alpha')\otimes(\beta\circ\beta')=(\alpha\otimes\beta)\circ(\alpha'\otimes\beta')\).

For the identity axiom, use the definition of the tensor product in \(\End(\CC)\). For all \(X\in\Ob(\CC)\), the component at \(X\) is:
\[
    (1_F\otimes1_G)_X
    =(1_F)_{G(X)}\circ F((1_G)_X)
\]
By definition of the identity morphism in \(\End(\CC)\) previously, \((1_F)_{G(X)}=\id_{F(G(X))}\) and \((1_G)_X=\id_{G(X)}\). \(F\) is a functor so it preserves identities, therefore \(F(\id_{G(X)})=\id_{F(G(X))}\):
\[\begin{align*}
    (1_F\otimes1_G)_X
    &=\id_{F(G(X))} \circ F(\id_{G(X)}) \\
    &=\id_{F(G(X))} \circ \id_{F(G(X))} \\
    &=\id_{F(G(X))}
\end{align*}\]
This is simply the component of the identity in \(F\circ G\) at \(X\):
\[
    (1_F\otimes1_G)_X
    =\id_{F(G(X))}
    =(1_{F\circ G})_X
\]
Therefore the identity axiom is satisfied.

As for the interchange axiom, note that \(\alpha:F\Rightarrow F'\) is a natural transformation. Use the morphism \(\beta'_X:G'(X)\to \overline{G}(X)\). Then, we have the commutative diagram:
\[
\begin{CD}
F(G'(X)) @> F(\beta'_X) >> F(\overline{G}(X)) \\
@V \alpha_{G'(X)} VV @VV \alpha_{\overline{G}(X)} V \\
F'(G'(X)) @>> F'(\beta'_X) > F'(\overline{G}(X))
\end{CD}
\]
Which means that \(\alpha_{\overline{G}(X)}\circ F(\beta'_X)=F'(\beta'_X)\circ\alpha_{G'(X)}\). Along with the definition of composition, the bifunctor \(\otimes\) and that \(F\) is a functor, we have:
\[
    ((\alpha'\circ\alpha)\otimes(\beta'\circ\beta))_X
    =((\alpha'\otimes\beta')\circ(\alpha\otimes\beta))_X.
\]
The derivation is left as an exercise to the reader.

#### Define unit object

Define the unit object to be the identity functor \(\Id_\CC\) that maps each object and morphism in \(\CC\) to itself.

#### Define associator and unitors

TODO

#### Check pentagon and triangle axioms

TODO


## Monoid Objects

TODO

## References {.unnumbered}

- Joseph D. Harris, Math 55 lecture notes.
- [https://en.wikipedia.org/wiki/Category_(mathematics)](https://en.wikipedia.org/wiki/Category_(mathematics)).
- [https://en.wikipedia.org/wiki/Functor](https://en.wikipedia.org/wiki/Functor).
- [https://en.wikipedia.org/wiki/Product_category](https://en.wikipedia.org/wiki/Product_category).
- [https://en.wikipedia.org/wiki/Monoidal_category](https://en.wikipedia.org/wiki/Monoidal_category).
- [https://en.wikipedia.org/wiki/Tensor_product](https://en.wikipedia.org/wiki/Tensor_product).
- [https://en.wikipedia.org/wiki/Transpose_of_a_linear_map](https://en.wikipedia.org/wiki/Transpose_of_a_linear_map).
- [https://ncatlab.org/nlab/show/natural+transformation](https://ncatlab.org/nlab/show/natural+transformation).


