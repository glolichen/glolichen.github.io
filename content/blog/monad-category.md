---
title: A Monad is a Monoid in the Category of Endofunctors (WIP)
author: 'Jayden Li'
date: "2026-11-19"
---

**In progress!**

I learned some category theory and abstract algebra in Math 55a at Harvard, so here's (trying to) understand the [Infamous](https://www.reddit.com/r/ProgrammerHumor/comments/1ctgnng/amonadisamonoidinthecategoryofendofunctors/) [Sentence](https://james-iry.blogspot.com/2009/05/brief-incomplete-and-mostly-wrong.html).

**<u>Definition</u> (Monad).** A *monad* is a monoid in the category of endofunctors. {{< p_qed >}}

Actually, this isn't the most precise definition. When I first saw this, I assumed "monoid" was a monoid in abstract algebra, which it isn't.[^monoid] We might also ask: ... the category of endofunctors of *what*? So, we have the more precise definition of a monad below:

[^monoid]: In abstract algebra, a *monoid* is a set \(S\) together with a binary operator \(m:S\times S\to S\) called the *law of composition* satisfying two axioms (using the shorthand for the law of composition \(ab:=m(a,b)\) for all \(a,b\in S\)):
     - Identity. There exists an element \(e\in S\) such that \(ae=ea=a\) for all \(a\in S\).
     - Associativity. For all \(a,b,c\in S\), we have \((ab)c=a(bc)\).

    (If a monoid satisfies the inverse axiom, then it is also a *group*: for all \(a\in S\), there exists an *inverse* \(b\in S\) such that \(ab=ba=e\).) As we'll see, the a *monoid object* in category theory is a generalization of a monoid defined above.

**<u>Definition</u> (Monad).** A *monad* in a category \(\newcommand{\CC}{\mathcal C} \CC\) is a *monoid object* in the *monoidal category* of endofunctors of \(\CC\). {{< p_qed >}}

Now, onto defining the terms in this definition.

## Category of Endofunctors

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

**<u>Definition</u> (Endofunctor).** An *endofunctor* is a functor from a category \(\CC\) to itself. \(\DeclareMathOperator{\End}{End}\End(\CC)\) is the collection[^setvsclass] of all endofunctors in \(\CC\): \(\End(C)=\{F:\CC \to \CC\}\). {{< p_qed >}}

Two claims: that \(\End(\CC)\) can itself be given a structure of not just a category, but of what's called a *monoidal category*, so \(\End(\CC)\) is the "monoidal category of endofunctors of \(C\)." First, we define a monoidal category.
[^setvsclass]: \(\End(C)\) is not always be a set, because it might be too big. For example, consider the category \(\CC\) of all sets. We have an embedding of all sets (i.e. objects of \(\CC\)) to an endofunctor of \(\CC\) as such: for all \(A\in\Ob(\CC)\), let \(K_A:\CC\to\CC\) be a mapping that maps all objects of \(\CC\) to the set/object \(A\), and all morphisms to \(\id_A\). \(K_A\) satisfies the requirements of a functor (preservation of composition and identity), and is thus an endofunctor. There is an associated endofunctor \(K_X\in\End(\CC)\) for each object in \(\CC\), so we have an embedding of every set in \(\End(C)\). But, a set cannot contain all sets (or have an embedding of all sets), so \(\End(C)\) is not a set. In this case, \(\End(C)\) is in fact a *proper class*. For "small" categories \(\CC\), however, \(\End(\CC)\) is actually a set.

## *Monoidal Category* of Endofunctors

**<u>Definition</u> (Product category).** Let \(\CC,\DD\) be categories. The *product category* \(\CC\times\DD\) is similar to the notion of a Cartesian product of sets.
- The objects of \(\CC\times\DD\) are pairs of objects \((A,B)\) with \(A\in\Ob(\CC)\) and \(B\in\Ob(\DD)\), i.e. \(\Ob(\CC\times\DD)=\Ob(\CC)\times\Ob(\DD)\), where the \(\times\) on the right hand side is the Cartesian product of sets.
- A morphism from \((A,B)\in \Ob(\CC\times\DD)\) to \((A',B')\in \Ob(\CC\times\DD)\) is a pair, consisting of a morphism in \(\CC\) from \(A\) to \(A'\) and a morphism in \(\DD\) from \(B\) to \(B'\). Therefore, the hom-set in the product category is: \(\Hom_{\CC\times\DD}((A,B),(A',B')) = \Hom_{\CC}(A,A') \times \Hom_{\DD}(B,B')\); again, the right hand side uses the Cartesian product.
- The composition of two morphisms \((f,g)\) and \((f',g')\) is given by pairwise composition of morphisms in categories \(\CC\) and \(\DD\): \((f,g)\circ(f',g')=(f\circ_\CC f', g\circ_\DD g')\).
- The identity morphism associated with the object \((A,B)\in\Ob(\CC\times\DD)\) is the pair \(\id_A,\id_B\), which are the identity morphisms in the respective categories \(\CC\) and \(\DD\).

In short: the objects of \(\CC\times\DD\) is the Cartesian product of the objects of both categories, and morphisms are defined naturally as pairs.{{< p_qed >}}

**<u>Definition</u> (Monoidal category).** A *monoidal category* is a category \(\CC\) together with the following to be defined:
- A bifunctor \(\otimes:\CC\times\CC\to\CC\) (a functor whose domain is a product category) called the *tensor product*.
- An *identity object* \(I\in\Ob(\CC)\).
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
\[\begin{CD}
(X\otimes I)\otimes Y @>{\alpha_{X,I,Y}}>> X\otimes(I\otimes Y) \\
@V{R_X\otimes\mathrm{id}_Y}VV @VV{\mathrm{id}_X\otimes L_Y}V \\
X\otimes Y @= X\otimes Y
\end{CD}\]
Similiarly, this says that you can eliminate the identity object \(I\) directly with the unitor, or you can reparenthesize \((X\otimes I)\otimes Y\) with the associator and then eliminate the identity. Both must give the same outcome.{{< p_qed >}}

Note that some of the properties above are about "natural isomorphisms." They are not equal! For example, the category of sets, with the bifunctor being the Cartesian product, is a monoidal category.[^trust] If \(X,Y,Z\) are sets, \((X\times Y)\times Z\) contain elements of the form \(((x,y), z)\), but \(X\times(Y\times Z)\) contains \((x,(y,z))\). They are not equal. But, there is a very natural and obvious isomorphism \((X\times Y)\times Z\xrightarrow{\sim} X\times (Y\times Z)\): the associator would be \(\alpha_{X,Y,Z}: ((x,y),z) \mapsto (x,(y,z)) \). But, since we are interested in structure, being isomorphic means they have the same structure and the two collections are "the same" up to renaming the elements, so they are good as equal.
[^trust]: trust me bro

Ok, this is quite a complicated definition. It may be helpful to look at some examples below.

<details open>
<summary>Example: Tensor product of vector spaces makes \(\Vect_k\) a monoidal category</summary>

Recall \(\Vect_k\), the category of finite-dimensional vector spaces over a field \(k\), and the ordinary tensor product of vector spaces. I claim that the tensor product on vector spaces gives \(\Vect_k\) the structure of a monoidal category. There are a number of equivalent definitions of the tensor product \(V\otimes W\) of two vector spaces \(V,W\in\Vect_k\), but we focus on these two:
1. Let \(v_1,\ldots,v_m\) be a basis for \(V\) and let \(w_1,\ldots,w_n\) be a basis for \(W\). The tensor product \(V\otimes W\) is the vector space with basis \(v_i\otimes w_j\) for all \(1\leq i\leq m,1\leq j\leq n\). If \(v=\sum_{i=1}a_iv_i\in V\) and \(w=\sum_{j=1}b_jw_j\in W\) (linear combination of basis vectors), then we define a pure tensor \(v\otimes w\) as:
\[ v\otimes w=\left(\sum_{i=1}^{m}a_iv_i\right)\otimes\left(\sum_{j=1}^{n}b_jw_j\right)=\sum_{i=1}^{m}\sum_{j=1}^{n} a_i b_j (v_i\otimes w_j) \]
Not all elements of \(V\otimes W\) are pure tensors, but all are linear combinations of pure tensors, by definition of span. The rank of an element of \(V\otimes W\) is the smallest \(\ell\) such that it can be expressed as the sum of \(\ell\) pure tensors.

Notice that this definition of a pure tensor makes the tensor product *bilinear*. That is, if \(v,v'\in V\), \(w,w'\in W\) and \(\lambda\in k\) is a scalar (in the underlying field \(k\)), then:
\begin{equation*}
    \left\\{\begin{aligned}
        (v+v')\otimes w
        &=v\otimes w+v'\otimes w \\\\
        v\otimes(w+w')
        &=v\otimes w+v\otimes w' \\\\
        (\lambda v)\otimes w
        &=\lambda(v\otimes w) \\\\
        v\otimes (\lambda w)
        &=\lambda(v\otimes w)
    \end{aligned}\right.
    \iff
    \left\\{\begin{aligned}
        (v+v')\otimes w
        -v\otimes w-v'\otimes w &= 0\\\\
        v\otimes(w+w')
        -v\otimes w-v\otimes w' &= 0\\\\
        (\lambda v)\otimes w
        -\lambda(v\otimes w) &= 0\\\\
        v\otimes (\lambda w)
        -\lambda(v\otimes w) &= 0
    \end{aligned}\right.
\end{equation*}
Notice that the right hand side of \(\iff\) above give the *relations* that the vector space \(V\otimes W\) must satisfy. This gives rise to our second definition.

2. Let \(U\) be a vector spaced spanned by the set \(\{ v\otimes w \mid v\in V,w\in W \}\), i.e. \(U\) is the vector space consisting of *all* linear combination of all symbols \(v\otimes w\). Then, let \(U_0\) be the vector space spanned by the relations the tensor product satisfies (i.e. \((v+v')\otimes w-v\otimes w-v'\otimes w\) and other expressions above), for all \(v\in V\) and \(w\in W\). Then, by the properties of a quotient vector space, \(U/U_0\) will satisfy all the relations we want, so we define \(V\otimes W=U/U_0\).
\[
    U_0=\operatorname{span} \left\{\left.\begin{gathered}
        (v+v')\otimes w
        -v\otimes w-v'\otimes w \\
        v\otimes(w+w')
        -v\otimes w-v\otimes w' \\
        (\lambda v)\otimes w
        -\lambda(v\otimes w) \\
        v\otimes (\lambda w)
        -\lambda(v\otimes w)
    \end{gathered}
    \right| v\in V, w\in W, \lambda\in k\right\}
\]

We are going to see that the category of vector spaces \(\Vect_k\) can be given the structure of a monoidal category with the tensor product as the bifunctor. A bifunctor must associate to each pair of objects \(V,W\in\Ob(\Vect_k)\) (the objects of the category \(\Vect_k\) are vector spaces) another object/vector space. The tensor product does so my mapping the pair \((V,W)\) with \(V,W\in\Ob(\Vect_k)\) to the vector space \(V\otimes W\). 

But a bifunctor must also map pairs of morphisms (in \(\Vect_k\), this would be pairs of linear maps) to another morphism/linear map. Specifically, let \(V,V',W,W'\in\Ob(\Vect_k)\) be vector spaces, and let \(T\in\Hom(V,V')=\LL(V,V')\) and \(S\in\Hom(W,W')=\LL(V,V')\) be morphisms/linear maps. In order for \(\otimes\) to be a bifunctor, we want \(T\otimes S\) to be a morphism from \(V\otimes W\) to \(V'\otimes W'\). There is a natural way to define this: define the tensor product of linear maps as \(T\otimes S:v\otimes w\mapsto T(v)\otimes S(w)\in V'\otimes W'\).

There are a few concerns with this definition. First, a pure tensor might have multiple names, and the map is only well-defined if all such names map to the same output. For example, \(v\otimes 2w=2v\otimes w\), and we want \(T(v)\otimes S(2w)=T(2v)\otimes S(w)\).

How do we ensure this is the case? Notice that \(T\otimes S:V\otimes W \to V'\otimes W'\). Instead, we define a new linear map \(\phi:U\to V'\otimes W'\) (recalling that \(V\otimes W=U/U_0\)), and like \(T\otimes S\) this new map is given by \(\phi(v\otimes w)=T(v)\otimes S(w)\). Recall that \(U\) is the space with basis all objects \(v\otimes w\) for all \(v\) and \(w\), the definition of \(\phi\) previously is really a definition of the linear map \(\phi\) on the basis vectors, the \(v\otimes w\)'s.

We want \(\phi\) to satisfy the following condition: let \(v_1\otimes w_1, v_2\otimes w_2 \in U\). If these are the same in the quotient space \(U/U_0\) (i.e. they both belong to the same coset[^coset] of \(U_0\)), then they are different names for the same pure tensor in \(V\otimes W\). Then, we should have:
\[
    \phi(v_1\otimes w_1)=\phi(v_2\otimes w_2)
    \iff
    \phi(v_1\otimes w_1)-\phi(v_2\otimes w_2)=0
\]
We defined \(\phi\) to be a linear map \(U\to V'\otimes W'\), so by linearity:
\[
    \phi(v_1\otimes w_1)=\phi(v_2\otimes w_2)
    \iff
    \phi(v_1\otimes w_1-v_2\otimes w_2)=0
\]

If \(v_1\otimes w_1=v_2\otimes w_2\) in the quotient space \(U/U_0\), then (proven in footnote) \(v_1\otimes w_1-v_2\otimes w_2\in U_0\). To prove the above statement, it suffices to show that \(\phi(u)=0\) for all \(u\in U_0\). By linearity of \(\phi\), \(\phi(u)=0\) for all \(u\) if \(\phi\) maps each basis vector/relation of \(U_0\) to \(0\). For example, for the relation \((v+v')\otimes w-v\otimes w-v'\otimes w\):
\begin{align*}
    \phi(\ldots)
    &=\phi((v+v')\otimes w)-\phi(v\otimes w)-\phi(v'\otimes w) \\\\
    &=T(v+v')\otimes S(w)- T(v)\otimes S(w)- T(v')\otimes S(w) \\\\
    &=(T(v)+T(v'))\otimes S(w)-T(v)\otimes S(w)-T(v')\otimes S(w) \\\\
    &=T(v)\otimes S(w)+T(v')\otimes S(w)-T(v)\otimes S(w)-T(v')\otimes S(w) \\\\
    &=0
\end{align*}
Note \(v,v'\in V, w,w'\in W\). Likewise for the other 3 relations spanning \(U_0\).

Ok, so we see that the definition of the linear map \(T\otimes S\) as \(v\otimes w\mapsto T(v)\otimes S(w)\) is well-defined. Note \(v\otimes w\in V\otimes W\) and \(T(v)\in V',S(w)\in W'\) so their tensor product is in \(V'\otimes W'\). Thus \(T\otimes S\) is a morphism \(V\otimes W\to V'\otimes W'\). Therefore, the tensor product \(\otimes\) also maps pairs of morphisms to morphisms.

TODO: show that the \(\otimes\) satisfy some other functor requirements, i.e. preserves composition and the identity. The unit object is just the underlying field \(k\). The associator \(\alpha\) is really simple (same as the set associator given before as am example). The unitors are given by scalar multiplication. They satisfy the pentagon/triangle axioms obviously.
</details>

[^coset]: The cosets of a subspace are parallel shifts of that subspace by a constant vector: if \(W\) is a subspace of \(V\), then for all \(v\in V\) we have a coset of \(W\): \(v+W=\{v+w \mid w\in W\}\). Two elements \(a,b\in U\) are in the same coset if and only if \(a+U_0\) and \(b+U_0\) the same coset. If \(a+U_0=b+U_0\), then for all \(r\in U_0\), there exists some \(r'\in U_0\) such that \(a+r=b+r_0 \implies a-b=r-r_0 \implies a-b\in U_0\). The converse is trivial.

    In \(U/U_0\), the cosets of \(U_0\) in the form \(a+U_0\) with \(a\in U\) is the subset of \(U\) that differ from \(a\) by an element of \(U_0\), i.e. \(a+U_0=\{a+u \mid u\in U_0\}\). Recall each of the relations used to define \(U_0\). We are saying that \(a+U_0\) is **the subset of \(V\) that equals \(a\) in \(V\otimes W=U/U_0\) by bilinearity**. For example, take \(a=(2v)\otimes (3w)\). Using the definition of the coset \(a+U_0\), and take:
\[\begin{aligned}
    u
    &=\underbrace{-1}_{\in k}\cdot \underbrace{((2v)\otimes (3w)-3((2v)\otimes w))}_{=(2v)\otimes (\lambda w)-\lambda((2v)\otimes w)}+\underbrace{(-3)}_{\in k}\cdot\underbrace{((2v)\otimes w-2(v\otimes w))}_{=(\lambda v)\otimes w-\lambda(v\otimes w)} \\
    &=\cancel{3((2v)\otimes w)}-(2v)\otimes (3w)+6(v\otimes w)-\cancel{3((2v)\otimes w)} \\
    &=6(v\otimes w)-(2v)\otimes (3w)
\end{aligned}\]
In the first line note that \(u\) is the linear combination (with weights \(-1\) and \(-3\)) of two expressions in the basis of \(U_0\) defined before (specifically, the last two). \(a+u=6(v\otimes w)\), so therefore \(6(v\otimes w)\) and \((2v)\otimes (3w)\) are in the same coset and are therefore in fact the same in \(V\otimes W=U/U_0\).

**<u>Proof</u> (\(\End(\CC)\) is a monoidal category).** The objects of \(\End(\CC)\) are endofunctors \(F:\CC\to\CC\). The morphisms are a bit more complex. What actually is a morphism? In familiar categories like the category of sets or \(\Vect_k\), morphisms are functions/linear maps between two sets/vector spaces. These would be natural ways to define a morphism in these particular categories. But morphisms of categories in general do not have to be maps. In general, the hom-set of two objects of a category is just a "thing that is provided when you define a category \(\CC\)," and the hom-sets collectively have some additional properties like identity and composition. Morphisms are just "things" in the hom-set; what they specifically are depends on the category.

For clarity, we will use \(\Hom_\DD\) to denote a hom-set in the category \(\DD\), since we will be working with \(\CC\) as well as the (supposed) category \(\End(\CC)\).

Let \(F,G\in\Ob(\End(\CC))\), so \(F,G\) are both endofunctors in \(\CC\): \(F,G:\CC\to\CC\). We define the morphisms as follows: the hom-set \(\Hom_{\End(\CC)}(F,G)\) is the set of *natural transformations* from \(F\) to \(G\). A natural transformation \(\alpha:F\Rightarrow G\)[^notation] is the assignment from every object \(X\in\Ob(\CC)\) to a morphism \(\alpha_X:F(X)\to G(X)\) in \(\CC\), such that for all objects \(X,Y\in\Ob(\CC)\) and morphisms \(\phi:X\to Y\) in \(\CC\), the following diagram commutes:
\[
\begin{CD}
F(X) @> F(\phi) >> F(Y) \\
@V \alpha_X VV @VV \alpha_Y V \\
G(X) @>> G(\phi) > G(Y)
\end{CD}
\]
Note: \(F(X),F(Y)\in\Ob(\CC)\) and \(\phi\in\Hom_{\CC}(X,Y)\), and \(F:\CC\to\CC\) is an endofunctor. By definition of a functor, \(F(\phi)\) is a morphism in \(\CC\) from \(F(X)\) to \(F(Y)\) (since a functor must carry a morphism into another morphism). Likewise for \(G\).
[^notation]: (Not an exponent!) I think the notational convention here is to use \(\Rightarrow\) for natural transformations between functors, like \(\alpha\), but use \(\to\) for morphisms and functors.

The diagram above commutes if and only if the following compositions are equal: \(G(\phi)\circ \alpha_X\) and \(\alpha_Y \circ F(\phi)\). Both of these are morphisms: \(\circ\) is the composition operator of morphisms defined in the category \(\CC\), which returns another morphism in \(\CC\).

Finish proof of \(\End(\CC)\).

## *Monoid Object* in the Monoidal Category of Endofunctors

TODO

## References {.unnumbered}

- Joseph D. Harris, Math 55 lecture notes.
- [https://en.wikipedia.org/wiki/Category_(mathematics)](https://en.wikipedia.org/wiki/Category_(mathematics)).
- [https://en.wikipedia.org/wiki/Functor](https://en.wikipedia.org/wiki/Functor).
- [https://en.wikipedia.org/wiki/Product_category](https://en.wikipedia.org/wiki/Product_category).
- [https://en.wikipedia.org/wiki/Monoidal_category](https://en.wikipedia.org/wiki/Monoidal_category).
- [https://en.wikipedia.org/wiki/Tensor_product](https://en.wikipedia.org/wiki/Tensor_product).
- [https://ncatlab.org/nlab/show/natural+transformation](https://ncatlab.org/nlab/show/natural+transformation).


