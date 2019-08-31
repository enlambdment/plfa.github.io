---
src       : "src/plfa/Induction.lagda.md"
title     : "Induction: Proof by Induction"
layout    : page
prev      : /Naturals/
permalink : /Induction/
next      : /Relations/
---

{% raw %}<pre class="Agda"><a id="146" class="Keyword">module</a> <a id="153" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html" class="Module">plfa.Induction</a> <a id="168" class="Keyword">where</a>
</pre>{% endraw %}
> Induction makes you feel guilty for getting something out of nothing
> ... but it is one of the greatest ideas of civilization.
> -- Herbert Wilf

Now that we've defined the naturals and operations upon them, our next
step is to learn how to prove properties that they satisfy.  As hinted
by their name, properties of _inductive datatypes_ are proved by
_induction_.


## Imports

We require equality as in the previous chapter, plus the naturals
and some operations upon them.  We also import a couple of new operations,
`cong`, `sym`, and `_≡⟨_⟩_`, which are explained below:
{% raw %}<pre class="Agda"><a id="763" class="Keyword">import</a> <a id="770" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.html" class="Module">Relation.Binary.PropositionalEquality</a> <a id="808" class="Symbol">as</a> <a id="811" class="Module">Eq</a>
<a id="814" class="Keyword">open</a> <a id="819" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.html" class="Module">Eq</a> <a id="822" class="Keyword">using</a> <a id="828" class="Symbol">(</a><a id="829" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">_≡_</a><a id="832" class="Symbol">;</a> <a id="834" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a><a id="838" class="Symbol">;</a> <a id="840" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a><a id="844" class="Symbol">;</a> <a id="846" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#939" class="Function">sym</a><a id="849" class="Symbol">)</a>
<a id="851" class="Keyword">open</a> <a id="856" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2499" class="Module">Eq.≡-Reasoning</a> <a id="871" class="Keyword">using</a> <a id="877" class="Symbol">(</a><a id="878" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin_</a><a id="884" class="Symbol">;</a> <a id="886" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">_≡⟨⟩_</a><a id="891" class="Symbol">;</a> <a id="893" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">_≡⟨_⟩_</a><a id="899" class="Symbol">;</a> <a id="901" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">_∎</a><a id="903" class="Symbol">)</a>
<a id="905" class="Keyword">open</a> <a id="910" class="Keyword">import</a> <a id="917" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.html" class="Module">Data.Nat</a> <a id="926" class="Keyword">using</a> <a id="932" class="Symbol">(</a><a id="933" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="934" class="Symbol">;</a> <a id="936" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a><a id="940" class="Symbol">;</a> <a id="942" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a><a id="945" class="Symbol">;</a> <a id="947" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">_+_</a><a id="950" class="Symbol">;</a> <a id="952" href="Agda.Builtin.Nat.html#501" class="Primitive Operator">_*_</a><a id="955" class="Symbol">;</a> <a id="957" href="Agda.Builtin.Nat.html#388" class="Primitive Operator">_∸_</a><a id="960" class="Symbol">)</a>
</pre>{% endraw %}

## Properties of operators

Operators pop up all the time, and mathematicians have agreed
on names for some of the most common properties.

* _Identity_.   Operator `+` has left identity `0` if `0 + n ≡ n`, and
  right identity `0` if `n + 0 ≡ n`, for all `n`. A value that is both
  a left and right identity is just called an identity. Identity is also
  sometimes called _unit_.

* _Associativity_.   Operator `+` is associative if the location
  of parentheses does not matter: `(m + n) + p ≡ m + (n + p)`,
  for all `m`, `n`, and `p`.

* _Commutativity_.   Operator `+` is commutative if order of
  arguments does not matter: `m + n ≡ n + m`, for all `m` and `n`.

* _Distributivity_.   Operator `*` distributes over operator `+` from the
  left if `(m + n) * p ≡ (m * p) + (n * p)`, for all `m`, `n`, and `p`,
  and from the right if `m * (p + q) ≡ (m * p) + (m * q)`, for all `m`,
  `p`, and `q`.

Addition has identity `0` and multiplication has identity `1`;
addition and multiplication are both associative and commutative;
and multiplication distributes over addition.

If you ever bump into an operator at a party, you now know how
to make small talk, by asking whether it has a unit and is
associative or commutative.  If you bump into two operators, you
might ask them if one distributes over the other.

Less frivolously, if you ever bump into an operator while reading a
technical paper, this gives you a way to orient yourself, by checking
whether or not it has an identity, is associative or commutative, or
distributes over another operator.  A careful author will often call
out these properties---or their lack---for instance by pointing out
that a newly introduced operator is associative but not commutative.

#### Exercise `operators` {#operators}

Give another example of a pair of operators that have an identity
and are associative, commutative, and distribute over one another.

Give an example of an operator that has an identity and is
associative but is not commutative.

{% raw %}<pre class="Agda"><a id="2975" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}

## Associativity

One property of addition is that it is _associative_, that is, that the
location of the parentheses does not matter:

    (m + n) + p ≡ m + (n + p)

Here `m`, `n`, and `p` are variables that range over all natural numbers.

We can test the proposition by choosing specific numbers for the three
variables:
{% raw %}<pre class="Agda"><a id="3332" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#3332" class="Function">_</a> <a id="3334" class="Symbol">:</a> <a id="3336" class="Symbol">(</a><a id="3337" class="Number">3</a> <a id="3339" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3341" class="Number">4</a><a id="3342" class="Symbol">)</a> <a id="3344" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3346" class="Number">5</a> <a id="3348" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="3350" class="Number">3</a> <a id="3352" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3354" class="Symbol">(</a><a id="3355" class="Number">4</a> <a id="3357" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3359" class="Number">5</a><a id="3360" class="Symbol">)</a>
<a id="3362" class="Symbol">_</a> <a id="3364" class="Symbol">=</a>
  <a id="3368" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="3378" class="Symbol">(</a><a id="3379" class="Number">3</a> <a id="3381" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3383" class="Number">4</a><a id="3384" class="Symbol">)</a> <a id="3386" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3388" class="Number">5</a>
  <a id="3392" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="3400" class="Number">7</a> <a id="3402" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3404" class="Number">5</a>
  <a id="3408" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="3416" class="Number">12</a>
  <a id="3421" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="3429" class="Number">3</a> <a id="3431" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3433" class="Number">9</a>
  <a id="3437" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="3445" class="Number">3</a> <a id="3447" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3449" class="Symbol">(</a><a id="3450" class="Number">4</a> <a id="3452" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="3454" class="Number">5</a><a id="3455" class="Symbol">)</a>
  <a id="3459" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}Here we have displayed the computation as a chain of equations,
one term to a line.  It is often easiest to read such chains from the top down
until one reaches the simplest term (in this case, `12`), and
then from the bottom up until one reaches the same term.

The test reveals that associativity is perhaps not as obvious as first
it appears.  Why should `7 + 5` be the same as `3 + 9`?  We might want
to gather more evidence, testing the proposition by choosing other
numbers.  But---since there are an infinite number of
naturals---testing can never be complete.  Is there any way we can be
sure that associativity holds for _all_ the natural numbers?

The answer is yes! We can prove a property holds for all naturals using
_proof by induction_.


## Proof by induction

Recall the definition of natural numbers consists of a _base case_
which tells us that `zero` is a natural, and an _inductive case_
which tells us that if `m` is a natural then `suc m` is also a natural.

Proof by induction follows the structure of this definition.  To prove
a property of natural numbers by induction, we need to prove two cases.
First is the _base case_, where we show the property holds for `zero`.
Second is the _inductive case_, where we assume the property holds for
an arbitrary natural `m` (we call this the _inductive hypothesis_), and
then show that the property must also hold for `suc m`.

If we write `P m` for a property of `m`, then what we need to
demonstrate are the following two inference rules:

    ------
    P zero

    P m
    ---------
    P (suc m)

Let's unpack these rules.  The first rule is the base case, and
requires we show that property `P` holds for `zero`.  The second rule
is the inductive case, and requires we show that if we assume the
inductive hypothesis---namely that `P` holds for `m`---then it follows that
`P` also holds for `suc m`.

Why does this work?  Again, it can be explained by a creation story.
To start with, we know no properties:

    -- In the beginning, no properties are known.

Now, we apply the two rules to all the properties we know about.  The
base case tells us that `P zero` holds, so we add it to the set of
known properties.  The inductive case tells us that if `P m` holds (on
the day before today) then `P (suc m)` also holds (today).  We didn't
know about any properties before today, so the inductive case doesn't
apply:

    -- On the first day, one property is known.
    P zero

Then we repeat the process, so on the next day we know about all the
properties from the day before, plus any properties added by the rules.
The base case tells us that `P zero` holds, but we already
knew that. But now the inductive case tells us that since `P zero`
held yesterday, then `P (suc zero)` holds today:

    -- On the second day, two properties are known.
    P zero
    P (suc zero)

And we repeat the process again. Now the inductive case
tells us that since `P zero` and `P (suc zero)` both hold, then
`P (suc zero)` and `P (suc (suc zero))` also hold. We already knew about
the first of these, but the second is new:

    -- On the third day, three properties are known.
    P zero
    P (suc zero)
    P (suc (suc zero))

You've got the hang of it by now:

    -- On the fourth day, four properties are known.
    P zero
    P (suc zero)
    P (suc (suc zero))
    P (suc (suc (suc zero)))

The process continues.  On the _n_'th day there will be _n_ distinct
properties that hold.  The property of every natural number will appear
on some given day.  In particular, the property `P n` first appears on
day _n+1_.


## Our first proof: associativity

To prove associativity, we take `P m` to be the property:

    (m + n) + p ≡ m + (n + p)

Here `n` and `p` are arbitrary natural numbers, so if we can show the
equation holds for all `m` it will also hold for all `n` and `p`.
The appropriate instances of the inference rules are:

    -------------------------------
    (zero + n) + p ≡ zero + (n + p)

    (m + n) + p ≡ m + (n + p)
    ---------------------------------
    (suc m + n) + p ≡ suc m + (n + p)

If we can demonstrate both of these, then associativity of addition
follows by induction.

Here is the proposition's statement and proof:
{% raw %}<pre class="Agda"><a id="+-assoc"></a><a id="7687" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7687" class="Function">+-assoc</a> <a id="7695" class="Symbol">:</a> <a id="7697" class="Symbol">∀</a> <a id="7699" class="Symbol">(</a><a id="7700" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7700" class="Bound">m</a> <a id="7702" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7702" class="Bound">n</a> <a id="7704" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7704" class="Bound">p</a> <a id="7706" class="Symbol">:</a> <a id="7708" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="7709" class="Symbol">)</a> <a id="7711" class="Symbol">→</a> <a id="7713" class="Symbol">(</a><a id="7714" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7700" class="Bound">m</a> <a id="7716" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7718" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7702" class="Bound">n</a><a id="7719" class="Symbol">)</a> <a id="7721" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7723" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7704" class="Bound">p</a> <a id="7725" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="7727" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7700" class="Bound">m</a> <a id="7729" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7731" class="Symbol">(</a><a id="7732" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7702" class="Bound">n</a> <a id="7734" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7736" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7704" class="Bound">p</a><a id="7737" class="Symbol">)</a>
<a id="7739" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7687" class="Function">+-assoc</a> <a id="7747" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="7752" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7752" class="Bound">n</a> <a id="7754" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7754" class="Bound">p</a> <a id="7756" class="Symbol">=</a>
  <a id="7760" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="7770" class="Symbol">(</a><a id="7771" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="7776" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7778" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7752" class="Bound">n</a><a id="7779" class="Symbol">)</a> <a id="7781" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7783" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7754" class="Bound">p</a>
  <a id="7787" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="7795" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7752" class="Bound">n</a> <a id="7797" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7799" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7754" class="Bound">p</a>
  <a id="7803" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="7811" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="7816" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7818" class="Symbol">(</a><a id="7819" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7752" class="Bound">n</a> <a id="7821" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7823" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7754" class="Bound">p</a><a id="7824" class="Symbol">)</a>
  <a id="7828" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="7830" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7687" class="Function">+-assoc</a> <a id="7838" class="Symbol">(</a><a id="7839" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="7843" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7843" class="Bound">m</a><a id="7844" class="Symbol">)</a> <a id="7846" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7846" class="Bound">n</a> <a id="7848" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7848" class="Bound">p</a> <a id="7850" class="Symbol">=</a>
  <a id="7854" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="7864" class="Symbol">(</a><a id="7865" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="7869" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7843" class="Bound">m</a> <a id="7871" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7873" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7846" class="Bound">n</a><a id="7874" class="Symbol">)</a> <a id="7876" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7878" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7848" class="Bound">p</a>
  <a id="7882" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="7890" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="7894" class="Symbol">(</a><a id="7895" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7843" class="Bound">m</a> <a id="7897" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7899" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7846" class="Bound">n</a><a id="7900" class="Symbol">)</a> <a id="7902" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7904" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7848" class="Bound">p</a>
  <a id="7908" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="7916" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="7920" class="Symbol">((</a><a id="7922" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7843" class="Bound">m</a> <a id="7924" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7926" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7846" class="Bound">n</a><a id="7927" class="Symbol">)</a> <a id="7929" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7931" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7848" class="Bound">p</a><a id="7932" class="Symbol">)</a>
  <a id="7936" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="7939" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="7944" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="7948" class="Symbol">(</a><a id="7949" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7687" class="Function">+-assoc</a> <a id="7957" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7843" class="Bound">m</a> <a id="7959" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7846" class="Bound">n</a> <a id="7961" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7848" class="Bound">p</a><a id="7962" class="Symbol">)</a> <a id="7964" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="7970" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="7974" class="Symbol">(</a><a id="7975" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7843" class="Bound">m</a> <a id="7977" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7979" class="Symbol">(</a><a id="7980" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7846" class="Bound">n</a> <a id="7982" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="7984" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7848" class="Bound">p</a><a id="7985" class="Symbol">))</a>
  <a id="7990" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="7998" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="8002" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7843" class="Bound">m</a> <a id="8004" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="8006" class="Symbol">(</a><a id="8007" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7846" class="Bound">n</a> <a id="8009" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="8011" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7848" class="Bound">p</a><a id="8012" class="Symbol">)</a>
  <a id="8016" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}We have named the proof `+-assoc`.  In Agda, identifiers can consist of
any sequence of characters not including spaces or the characters `@.(){};_`.

Let's unpack this code.  The signature states that we are
defining the identifier `+-assoc` which provides evidence for the
proposition:

    ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)

The upside down A is pronounced "for all", and the proposition
asserts that for all natural numbers `m`, `n`, and `p`
the equation `(m + n) + p ≡ m + (n + p)` holds.  Evidence for the proposition
is a function that accepts three natural numbers, binds them to `m`, `n`, and `p`,
and returns evidence for the corresponding instance of the equation.

For the base case, we must show:

    (zero + n) + p ≡ zero + (n + p)

Simplifying both sides with the base case of addition yields the equation:

    n + p ≡ n + p

This holds trivially.  Reading the chain of equations in the base case of the proof,
the top and bottom of the chain match the two sides of the equation to
be shown, and reading down from the top and up from the bottom takes us to
`n + p` in the middle.  No justification other than simplification is required.

For the inductive case, we must show:

    (suc m + n) + p ≡ suc m + (n + p)

Simplifying both sides with the inductive case of addition yields the equation:

    suc ((m + n) + p) ≡ suc (m + (n + p))

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    (m + n) + p ≡ m + (n + p)

Reading the chain of equations in the inductive case of the proof, the
top and bottom of the chain match the two sides of the equation to be
shown, and reading down from the top and up from the bottom takes us
to the simplified equation above. The remaining equation, does not follow
from simplification alone, so we use an additional operator for chain
reasoning, `_≡⟨_⟩_`, where a justification for the equation appears
within angle brackets.  The justification given is:

    ⟨ cong suc (+-assoc m n p) ⟩

Here, the recursive invocation `+-assoc m n p` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.

A relation is said to be a _congruence_ for a given function if it is
preserved by applying that function.  If `e` is evidence that `x ≡ y`,
then `cong f e` is evidence that `f x ≡ f y`, for any function `f`.

Here the inductive hypothesis is not assumed, but instead proved by a
recursive invocation of the function we are defining, `+-assoc m n p`.
As with addition, this is well founded because associativity of
larger numbers is proved in terms of associativity of smaller numbers.
In this case, `assoc (suc m) n p` is proved using `assoc m n p`.
The correspondence between proof by induction and definition by
recursion is one of the most appealing aspects of Agda.

## Induction as recursion

As a concrete example of how induction corresponds to recursion, here
is the computation that occurs when instantiating `m` to `2` in the
proof of associativity.

{% raw %}<pre class="Agda"><a id="+-assoc-2"></a><a id="11040" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11040" class="Function">+-assoc-2</a> <a id="11050" class="Symbol">:</a> <a id="11052" class="Symbol">∀</a> <a id="11054" class="Symbol">(</a><a id="11055" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11055" class="Bound">n</a> <a id="11057" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11057" class="Bound">p</a> <a id="11059" class="Symbol">:</a> <a id="11061" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="11062" class="Symbol">)</a> <a id="11064" class="Symbol">→</a> <a id="11066" class="Symbol">(</a><a id="11067" class="Number">2</a> <a id="11069" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11071" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11055" class="Bound">n</a><a id="11072" class="Symbol">)</a> <a id="11074" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11076" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11057" class="Bound">p</a> <a id="11078" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="11080" class="Number">2</a> <a id="11082" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11084" class="Symbol">(</a><a id="11085" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11055" class="Bound">n</a> <a id="11087" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11089" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11057" class="Bound">p</a><a id="11090" class="Symbol">)</a>
<a id="11092" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11040" class="Function">+-assoc-2</a> <a id="11102" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11102" class="Bound">n</a> <a id="11104" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11104" class="Bound">p</a> <a id="11106" class="Symbol">=</a>
  <a id="11110" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="11120" class="Symbol">(</a><a id="11121" class="Number">2</a> <a id="11123" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11125" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11102" class="Bound">n</a><a id="11126" class="Symbol">)</a> <a id="11128" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11130" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11104" class="Bound">p</a>
  <a id="11134" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="11142" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="11146" class="Symbol">(</a><a id="11147" class="Number">1</a> <a id="11149" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11151" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11102" class="Bound">n</a><a id="11152" class="Symbol">)</a> <a id="11154" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11156" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11104" class="Bound">p</a>
  <a id="11160" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="11168" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="11172" class="Symbol">((</a><a id="11174" class="Number">1</a> <a id="11176" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11178" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11102" class="Bound">n</a><a id="11179" class="Symbol">)</a> <a id="11181" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11183" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11104" class="Bound">p</a><a id="11184" class="Symbol">)</a>
  <a id="11188" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="11191" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="11196" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="11200" class="Symbol">(</a><a id="11201" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11276" class="Function">+-assoc-1</a> <a id="11211" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11102" class="Bound">n</a> <a id="11213" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11104" class="Bound">p</a><a id="11214" class="Symbol">)</a> <a id="11216" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="11222" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="11226" class="Symbol">(</a><a id="11227" class="Number">1</a> <a id="11229" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11231" class="Symbol">(</a><a id="11232" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11102" class="Bound">n</a> <a id="11234" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11236" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11104" class="Bound">p</a><a id="11237" class="Symbol">))</a>
  <a id="11242" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="11250" class="Number">2</a> <a id="11252" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11254" class="Symbol">(</a><a id="11255" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11102" class="Bound">n</a> <a id="11257" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11259" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11104" class="Bound">p</a><a id="11260" class="Symbol">)</a>
  <a id="11264" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
  <a id="11268" class="Keyword">where</a>
  <a id="11276" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11276" class="Function">+-assoc-1</a> <a id="11286" class="Symbol">:</a> <a id="11288" class="Symbol">∀</a> <a id="11290" class="Symbol">(</a><a id="11291" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11291" class="Bound">n</a> <a id="11293" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11293" class="Bound">p</a> <a id="11295" class="Symbol">:</a> <a id="11297" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="11298" class="Symbol">)</a> <a id="11300" class="Symbol">→</a> <a id="11302" class="Symbol">(</a><a id="11303" class="Number">1</a> <a id="11305" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11307" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11291" class="Bound">n</a><a id="11308" class="Symbol">)</a> <a id="11310" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11312" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11293" class="Bound">p</a> <a id="11314" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="11316" class="Number">1</a> <a id="11318" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11320" class="Symbol">(</a><a id="11321" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11291" class="Bound">n</a> <a id="11323" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11325" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11293" class="Bound">p</a><a id="11326" class="Symbol">)</a>
  <a id="11330" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11276" class="Function">+-assoc-1</a> <a id="11340" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11340" class="Bound">n</a> <a id="11342" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11342" class="Bound">p</a> <a id="11344" class="Symbol">=</a>
    <a id="11350" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
      <a id="11362" class="Symbol">(</a><a id="11363" class="Number">1</a> <a id="11365" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11367" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11340" class="Bound">n</a><a id="11368" class="Symbol">)</a> <a id="11370" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11372" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11342" class="Bound">p</a>
    <a id="11378" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
      <a id="11388" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="11392" class="Symbol">(</a><a id="11393" class="Number">0</a> <a id="11395" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11397" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11340" class="Bound">n</a><a id="11398" class="Symbol">)</a> <a id="11400" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11402" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11342" class="Bound">p</a>
    <a id="11408" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
      <a id="11418" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="11422" class="Symbol">((</a><a id="11424" class="Number">0</a> <a id="11426" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11428" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11340" class="Bound">n</a><a id="11429" class="Symbol">)</a> <a id="11431" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11433" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11342" class="Bound">p</a><a id="11434" class="Symbol">)</a>
    <a id="11440" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="11443" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="11448" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="11452" class="Symbol">(</a><a id="11453" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11540" class="Function">+-assoc-0</a> <a id="11463" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11340" class="Bound">n</a> <a id="11465" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11342" class="Bound">p</a><a id="11466" class="Symbol">)</a> <a id="11468" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
      <a id="11476" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="11480" class="Symbol">(</a><a id="11481" class="Number">0</a> <a id="11483" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11485" class="Symbol">(</a><a id="11486" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11340" class="Bound">n</a> <a id="11488" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11490" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11342" class="Bound">p</a><a id="11491" class="Symbol">))</a>
    <a id="11498" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
      <a id="11508" class="Number">1</a> <a id="11510" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11512" class="Symbol">(</a><a id="11513" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11340" class="Bound">n</a> <a id="11515" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11517" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11342" class="Bound">p</a><a id="11518" class="Symbol">)</a>
    <a id="11524" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
    <a id="11530" class="Keyword">where</a>
    <a id="11540" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11540" class="Function">+-assoc-0</a> <a id="11550" class="Symbol">:</a> <a id="11552" class="Symbol">∀</a> <a id="11554" class="Symbol">(</a><a id="11555" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11555" class="Bound">n</a> <a id="11557" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11557" class="Bound">p</a> <a id="11559" class="Symbol">:</a> <a id="11561" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="11562" class="Symbol">)</a> <a id="11564" class="Symbol">→</a> <a id="11566" class="Symbol">(</a><a id="11567" class="Number">0</a> <a id="11569" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11571" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11555" class="Bound">n</a><a id="11572" class="Symbol">)</a> <a id="11574" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11576" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11557" class="Bound">p</a> <a id="11578" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="11580" class="Number">0</a> <a id="11582" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11584" class="Symbol">(</a><a id="11585" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11555" class="Bound">n</a> <a id="11587" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11589" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11557" class="Bound">p</a><a id="11590" class="Symbol">)</a>
    <a id="11596" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11540" class="Function">+-assoc-0</a> <a id="11606" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11606" class="Bound">n</a> <a id="11608" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11608" class="Bound">p</a> <a id="11610" class="Symbol">=</a>
      <a id="11618" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
        <a id="11632" class="Symbol">(</a><a id="11633" class="Number">0</a> <a id="11635" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11637" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11606" class="Bound">n</a><a id="11638" class="Symbol">)</a> <a id="11640" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11642" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11608" class="Bound">p</a>
      <a id="11650" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
        <a id="11662" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11606" class="Bound">n</a> <a id="11664" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11666" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11608" class="Bound">p</a>
      <a id="11674" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
        <a id="11686" class="Number">0</a> <a id="11688" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11690" class="Symbol">(</a><a id="11691" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11606" class="Bound">n</a> <a id="11693" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="11695" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#11608" class="Bound">p</a><a id="11696" class="Symbol">)</a>
      <a id="11704" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}

## Terminology and notation

The symbol `∀` appears in the statement of associativity to indicate that
it holds for all numbers `m`, `n`, and `p`.  We refer to `∀` as the _universal
quantifier_, and it is discussed further in Chapter [Quantifiers]({{ site.baseurl }}/Quantifiers/).

Evidence for a universal quantifier is a function.  The notations

    +-assoc : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)

and

    +-assoc : ∀ (m : ℕ) → ∀ (n : ℕ) → ∀ (p : ℕ) → (m + n) + p ≡ m + (n + p)

are equivalent. They differ from a function type such as `ℕ → ℕ → ℕ`
in that variables are associated with the each argument type, and the
result type may mention (or depend upon) these variables; hence they
are called _dependent functions_.


## Our second proof: commutativity

Another important property of addition is that it is _commutative_, that is,
that the order of the operands does not matter:

    m + n ≡ n + m

The proof requires that we first demonstrate two lemmas.

### The first lemma

The base case of the definition of addition states that zero
is a left-identity:

    zero + n ≡ n

Our first lemma states that zero is also a right-identity:

    m + zero ≡ m

Here is the lemma's statement and proof:
{% raw %}<pre class="Agda"><a id="+-identityʳ"></a><a id="12928" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#12928" class="Function">+-identityʳ</a> <a id="12940" class="Symbol">:</a> <a id="12942" class="Symbol">∀</a> <a id="12944" class="Symbol">(</a><a id="12945" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#12945" class="Bound">m</a> <a id="12947" class="Symbol">:</a> <a id="12949" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="12950" class="Symbol">)</a> <a id="12952" class="Symbol">→</a> <a id="12954" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#12945" class="Bound">m</a> <a id="12956" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="12958" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="12963" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="12965" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#12945" class="Bound">m</a>
<a id="12967" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#12928" class="Function">+-identityʳ</a> <a id="12979" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="12984" class="Symbol">=</a>
  <a id="12988" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="12998" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="13003" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="13005" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a>
  <a id="13012" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="13020" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a>
  <a id="13027" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="13029" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#12928" class="Function">+-identityʳ</a> <a id="13041" class="Symbol">(</a><a id="13042" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="13046" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#13046" class="Bound">m</a><a id="13047" class="Symbol">)</a> <a id="13049" class="Symbol">=</a>
  <a id="13053" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="13063" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="13067" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#13046" class="Bound">m</a> <a id="13069" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="13071" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a>
  <a id="13078" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="13086" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="13090" class="Symbol">(</a><a id="13091" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#13046" class="Bound">m</a> <a id="13093" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="13095" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a><a id="13099" class="Symbol">)</a>
  <a id="13103" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="13106" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="13111" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="13115" class="Symbol">(</a><a id="13116" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#12928" class="Function">+-identityʳ</a> <a id="13128" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#13046" class="Bound">m</a><a id="13129" class="Symbol">)</a> <a id="13131" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="13137" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="13141" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#13046" class="Bound">m</a>
  <a id="13145" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}The signature states that we are defining the identifier `+-identityʳ` which
provides evidence for the proposition:

    ∀ (m : ℕ) → m + zero ≡ m

Evidence for the proposition is a function that accepts a natural
number, binds it to `m`, and returns evidence for the corresponding
instance of the equation.  The proof is by induction on `m`.

For the base case, we must show:

    zero + zero ≡ zero

Simplifying with the base case of addition, this is straightforward.

For the inductive case, we must show:

    (suc m) + zero = suc m

Simplifying both sides with the inductive case of addition yields the equation:

    suc (m + zero) = suc m

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    m + zero ≡ m

Reading the chain of equations down from the top and up from the bottom
takes us to the simplified equation above.  The remaining
equation has the justification:

    ⟨ cong suc (+-identityʳ m) ⟩

Here, the recursive invocation `+-identityʳ m` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.  This completes the first lemma.

### The second lemma

The inductive case of the definition of addition pushes `suc` on the
first argument to the outside:

    suc m + n ≡ suc (m + n)

Our second lemma does the same for `suc` on the second argument:

    m + suc n ≡ suc (m + n)

Here is the lemma's statement and proof:
{% raw %}<pre class="Agda"><a id="+-suc"></a><a id="14585" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14585" class="Function">+-suc</a> <a id="14591" class="Symbol">:</a> <a id="14593" class="Symbol">∀</a> <a id="14595" class="Symbol">(</a><a id="14596" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14596" class="Bound">m</a> <a id="14598" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14598" class="Bound">n</a> <a id="14600" class="Symbol">:</a> <a id="14602" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="14603" class="Symbol">)</a> <a id="14605" class="Symbol">→</a> <a id="14607" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14596" class="Bound">m</a> <a id="14609" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="14611" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14615" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14598" class="Bound">n</a> <a id="14617" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="14619" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14623" class="Symbol">(</a><a id="14624" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14596" class="Bound">m</a> <a id="14626" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="14628" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14598" class="Bound">n</a><a id="14629" class="Symbol">)</a>
<a id="14631" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14585" class="Function">+-suc</a> <a id="14637" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="14642" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14642" class="Bound">n</a> <a id="14644" class="Symbol">=</a>
  <a id="14648" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="14658" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="14663" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="14665" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14669" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14642" class="Bound">n</a>
  <a id="14673" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14681" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14685" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14642" class="Bound">n</a>
  <a id="14689" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14697" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14701" class="Symbol">(</a><a id="14702" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="14707" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="14709" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14642" class="Bound">n</a><a id="14710" class="Symbol">)</a>
  <a id="14714" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="14716" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14585" class="Function">+-suc</a> <a id="14722" class="Symbol">(</a><a id="14723" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14727" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14727" class="Bound">m</a><a id="14728" class="Symbol">)</a> <a id="14730" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14730" class="Bound">n</a> <a id="14732" class="Symbol">=</a>
  <a id="14736" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="14746" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14750" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14727" class="Bound">m</a> <a id="14752" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="14754" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14758" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14730" class="Bound">n</a>
  <a id="14762" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14770" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14774" class="Symbol">(</a><a id="14775" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14727" class="Bound">m</a> <a id="14777" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="14779" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14783" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14730" class="Bound">n</a><a id="14784" class="Symbol">)</a>
  <a id="14788" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="14791" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="14796" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14800" class="Symbol">(</a><a id="14801" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14585" class="Function">+-suc</a> <a id="14807" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14727" class="Bound">m</a> <a id="14809" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14730" class="Bound">n</a><a id="14810" class="Symbol">)</a> <a id="14812" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="14818" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14822" class="Symbol">(</a><a id="14823" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14827" class="Symbol">(</a><a id="14828" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14727" class="Bound">m</a> <a id="14830" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="14832" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14730" class="Bound">n</a><a id="14833" class="Symbol">))</a>
  <a id="14838" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="14846" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14850" class="Symbol">(</a><a id="14851" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="14855" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14727" class="Bound">m</a> <a id="14857" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="14859" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14730" class="Bound">n</a><a id="14860" class="Symbol">)</a>
  <a id="14864" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}The signature states that we are defining the identifier `+-suc` which provides
evidence for the proposition:

    ∀ (m n : ℕ) → m + suc n ≡ suc (m + n)

Evidence for the proposition is a function that accepts two natural numbers,
binds them to `m` and `n`, and returns evidence for the corresponding instance
of the equation.  The proof is by induction on `m`.

For the base case, we must show:

    zero + suc n ≡ suc (zero + n)

Simplifying with the base case of addition, this is straightforward.

For the inductive case, we must show:

    suc m + suc n ≡ suc (suc m + n)

Simplifying both sides with the inductive case of addition yields the equation:

    suc (m + suc n) ≡ suc (suc (m + n))

This in turn follows by prefacing `suc` to both sides of the induction
hypothesis:

    m + suc n ≡ suc (m + n)

Reading the chain of equations down from the top and up from the bottom
takes us to the simplified equation in the middle.  The remaining
equation has the justification:

    ⟨ cong suc (+-suc m n) ⟩

Here, the recursive invocation `+-suc m n` has as its type the
induction hypothesis, and `cong suc` prefaces `suc` to each side to
yield the needed equation.  This completes the second lemma.

### The proposition

Finally, here is our proposition's statement and proof:
{% raw %}<pre class="Agda"><a id="+-comm"></a><a id="16158" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16158" class="Function">+-comm</a> <a id="16165" class="Symbol">:</a> <a id="16167" class="Symbol">∀</a> <a id="16169" class="Symbol">(</a><a id="16170" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16170" class="Bound">m</a> <a id="16172" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16172" class="Bound">n</a> <a id="16174" class="Symbol">:</a> <a id="16176" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="16177" class="Symbol">)</a> <a id="16179" class="Symbol">→</a> <a id="16181" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16170" class="Bound">m</a> <a id="16183" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="16185" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16172" class="Bound">n</a> <a id="16187" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="16189" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16172" class="Bound">n</a> <a id="16191" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="16193" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16170" class="Bound">m</a>
<a id="16195" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16158" class="Function">+-comm</a> <a id="16202" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16202" class="Bound">m</a> <a id="16204" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="16209" class="Symbol">=</a>
  <a id="16213" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="16223" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16202" class="Bound">m</a> <a id="16225" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="16227" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a>
  <a id="16234" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="16237" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#12928" class="Function">+-identityʳ</a> <a id="16249" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16202" class="Bound">m</a> <a id="16251" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="16257" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16202" class="Bound">m</a>
  <a id="16261" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="16269" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="16274" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="16276" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16202" class="Bound">m</a>
  <a id="16280" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
<a id="16282" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16158" class="Function">+-comm</a> <a id="16289" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16289" class="Bound">m</a> <a id="16291" class="Symbol">(</a><a id="16292" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="16296" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16296" class="Bound">n</a><a id="16297" class="Symbol">)</a> <a id="16299" class="Symbol">=</a>
  <a id="16303" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="16313" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16289" class="Bound">m</a> <a id="16315" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="16317" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="16321" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16296" class="Bound">n</a>
  <a id="16325" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="16328" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#14585" class="Function">+-suc</a> <a id="16334" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16289" class="Bound">m</a> <a id="16336" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16296" class="Bound">n</a> <a id="16338" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="16344" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="16348" class="Symbol">(</a><a id="16349" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16289" class="Bound">m</a> <a id="16351" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="16353" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16296" class="Bound">n</a><a id="16354" class="Symbol">)</a>
  <a id="16358" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="16361" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="16366" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="16370" class="Symbol">(</a><a id="16371" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16158" class="Function">+-comm</a> <a id="16378" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16289" class="Bound">m</a> <a id="16380" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16296" class="Bound">n</a><a id="16381" class="Symbol">)</a> <a id="16383" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="16389" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="16393" class="Symbol">(</a><a id="16394" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16296" class="Bound">n</a> <a id="16396" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="16398" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16289" class="Bound">m</a><a id="16399" class="Symbol">)</a>
  <a id="16403" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2655" class="Function Operator">≡⟨⟩</a>
    <a id="16411" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="16415" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16296" class="Bound">n</a> <a id="16417" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="16419" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#16289" class="Bound">m</a>
  <a id="16423" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}The first line states that we are defining the identifier
`+-comm` which provides evidence for the proposition:

    ∀ (m n : ℕ) → m + n ≡ n + m

Evidence for the proposition is a function that accepts two
natural numbers, binds them to `m` and `n`, and returns evidence for the
corresponding instance of the equation.  The proof is by
induction on `n`.  (Not on `m` this time!)

For the base case, we must show:

    m + zero ≡ zero + m

Simplifying both sides with the base case of addition yields the equation:

    m + zero ≡ m

The remaining equation has the justification `⟨ +-identityʳ m ⟩`,
which invokes the first lemma.

For the inductive case, we must show:

    m + suc n ≡ suc n + m

Simplifying both sides with the inductive case of addition yields the equation:

    m + suc n ≡ suc (n + m)

We show this in two steps.  First, we have:

    m + suc n ≡ suc (m + n)

which is justified by the second lemma, `⟨ +-suc m n ⟩`.  Then we
have

    suc (m + n) ≡ suc (n + m)

which is justified by congruence and the induction hypothesis,
`⟨ cong suc (+-comm m n) ⟩`.  This completes the proof.

Agda requires that identifiers are defined before they are used,
so we must present the lemmas before the main proposition, as we
have done above.  In practice, one will often attempt to prove
the main proposition first, and the equations required to do so
will suggest what lemmas to prove.


## Our first corollary: rearranging {#sections}

We can apply associativity to rearrange parentheses however we like.
Here is an example:
{% raw %}<pre class="Agda"><a id="+-rearrange"></a><a id="17969" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17969" class="Function">+-rearrange</a> <a id="17981" class="Symbol">:</a> <a id="17983" class="Symbol">∀</a> <a id="17985" class="Symbol">(</a><a id="17986" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17986" class="Bound">m</a> <a id="17988" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17988" class="Bound">n</a> <a id="17990" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17990" class="Bound">p</a> <a id="17992" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17992" class="Bound">q</a> <a id="17994" class="Symbol">:</a> <a id="17996" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="17997" class="Symbol">)</a> <a id="17999" class="Symbol">→</a> <a id="18001" class="Symbol">(</a><a id="18002" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17986" class="Bound">m</a> <a id="18004" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18006" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17988" class="Bound">n</a><a id="18007" class="Symbol">)</a> <a id="18009" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18011" class="Symbol">(</a><a id="18012" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17990" class="Bound">p</a> <a id="18014" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18016" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17992" class="Bound">q</a><a id="18017" class="Symbol">)</a> <a id="18019" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="18021" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17986" class="Bound">m</a> <a id="18023" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18025" class="Symbol">(</a><a id="18026" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17988" class="Bound">n</a> <a id="18028" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18030" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17990" class="Bound">p</a><a id="18031" class="Symbol">)</a> <a id="18033" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18035" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17992" class="Bound">q</a>
<a id="18037" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#17969" class="Function">+-rearrange</a> <a id="18049" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18049" class="Bound">m</a> <a id="18051" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18051" class="Bound">n</a> <a id="18053" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18053" class="Bound">p</a> <a id="18055" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18055" class="Bound">q</a> <a id="18057" class="Symbol">=</a>
  <a id="18061" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2597" class="Function Operator">begin</a>
    <a id="18071" class="Symbol">(</a><a id="18072" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18049" class="Bound">m</a> <a id="18074" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18076" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18051" class="Bound">n</a><a id="18077" class="Symbol">)</a> <a id="18079" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18081" class="Symbol">(</a><a id="18082" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18053" class="Bound">p</a> <a id="18084" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18086" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18055" class="Bound">q</a><a id="18087" class="Symbol">)</a>
  <a id="18091" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="18094" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7687" class="Function">+-assoc</a> <a id="18102" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18049" class="Bound">m</a> <a id="18104" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18051" class="Bound">n</a> <a id="18106" class="Symbol">(</a><a id="18107" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18053" class="Bound">p</a> <a id="18109" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18111" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18055" class="Bound">q</a><a id="18112" class="Symbol">)</a> <a id="18114" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="18120" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18049" class="Bound">m</a> <a id="18122" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18124" class="Symbol">(</a><a id="18125" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18051" class="Bound">n</a> <a id="18127" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18129" class="Symbol">(</a><a id="18130" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18053" class="Bound">p</a> <a id="18132" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18134" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18055" class="Bound">q</a><a id="18135" class="Symbol">))</a>
  <a id="18140" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="18143" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#1090" class="Function">cong</a> <a id="18148" class="Symbol">(</a><a id="18149" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18049" class="Bound">m</a> <a id="18151" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+_</a><a id="18153" class="Symbol">)</a> <a id="18155" class="Symbol">(</a><a id="18156" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#939" class="Function">sym</a> <a id="18160" class="Symbol">(</a><a id="18161" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7687" class="Function">+-assoc</a> <a id="18169" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18051" class="Bound">n</a> <a id="18171" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18053" class="Bound">p</a> <a id="18173" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18055" class="Bound">q</a><a id="18174" class="Symbol">))</a> <a id="18177" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="18183" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18049" class="Bound">m</a> <a id="18185" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18187" class="Symbol">((</a><a id="18189" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18051" class="Bound">n</a> <a id="18191" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18193" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18053" class="Bound">p</a><a id="18194" class="Symbol">)</a> <a id="18196" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18198" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18055" class="Bound">q</a><a id="18199" class="Symbol">)</a>
  <a id="18203" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">≡⟨</a> <a id="18206" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#939" class="Function">sym</a> <a id="18210" class="Symbol">(</a><a id="18211" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#7687" class="Function">+-assoc</a> <a id="18219" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18049" class="Bound">m</a> <a id="18221" class="Symbol">(</a><a id="18222" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18051" class="Bound">n</a> <a id="18224" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18226" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18053" class="Bound">p</a><a id="18227" class="Symbol">)</a> <a id="18229" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18055" class="Bound">q</a><a id="18230" class="Symbol">)</a> <a id="18232" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2714" class="Function Operator">⟩</a>
    <a id="18238" class="Symbol">(</a><a id="18239" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18049" class="Bound">m</a> <a id="18241" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18243" class="Symbol">(</a><a id="18244" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18051" class="Bound">n</a> <a id="18246" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18248" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18053" class="Bound">p</a><a id="18249" class="Symbol">))</a> <a id="18252" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="18254" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#18055" class="Bound">q</a>
  <a id="18258" href="https://agda.github.io/agda-stdlib/v1.1/Relation.Binary.PropositionalEquality.Core.html#2892" class="Function Operator">∎</a>
</pre>{% endraw %}No induction is required, we simply apply associativity twice.
A few points are worthy of note.

First, addition associates to the left, so `m + (n + p) + q`
stands for `(m + (n + p)) + q`.

Second, we use `sym` to interchange the sides of an equation.
Proposition `+-assoc n p q` shifts parentheses from right to left:

    (n + p) + q ≡ n + (p + q)

To shift them the other way, we use `sym (+-assoc n p q)`:

    n + (p + q) ≡ (n + p) + q

In general, if `e` provides evidence for `x ≡ y` then `sym e` provides
evidence for `y ≡ x`.

Third, Agda supports a variant of the _section_ notation introduced by
Richard Bird.  We write `(x +_)` for the function that applied to `y`
returns `x + y`.  Thus, applying the congruence `cong (m +_)` takes
the above equation into:

    m + (n + (p + q)) ≡ m + ((n + p) + q)

Similarly, we write `(_+ x)` for the function that applied to `y`
returns `y + x`; the same works for any infix operator.



## Creation, one last time

Returning to the proof of associativity, it may be helpful to view the inductive
proof (or, equivalently, the recursive definition) as a creation story.  This
time we are concerned with judgments asserting associativity:

     -- In the beginning, we know nothing about associativity.

Now, we apply the rules to all the judgments we know about.  The base
case tells us that `(zero + n) + p ≡ zero + (n + p)` for every natural
`n` and `p`.  The inductive case tells us that if `(m + n) + p ≡ m +
(n + p)` (on the day before today) then
`(suc m + n) + p ≡ suc m + (n + p)` (today).
We didn't know any judgments about associativity before today, so that
rule doesn't give us any new judgments:

    -- On the first day, we know about associativity of 0.
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...

Then we repeat the process, so on the next day we know about all the
judgments from the day before, plus any judgments added by the rules.
The base case tells us nothing new, but now the inductive case adds
more judgments:

    -- On the second day, we know about associativity of 0 and 1.
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...

And we repeat the process again:

    -- On the third day, we know about associativity of 0, 1, and 2.
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...
    (2 + 0) + 0 ≡ 2 + (0 + 0)   ...   (2 + 4) + 5 ≡ 2 + (4 + 5)   ...

You've got the hang of it by now:

    -- On the fourth day, we know about associativity of 0, 1, 2, and 3.
    (0 + 0) + 0 ≡ 0 + (0 + 0)   ...   (0 + 4) + 5 ≡ 0 + (4 + 5)   ...
    (1 + 0) + 0 ≡ 1 + (0 + 0)   ...   (1 + 4) + 5 ≡ 1 + (4 + 5)   ...
    (2 + 0) + 0 ≡ 2 + (0 + 0)   ...   (2 + 4) + 5 ≡ 2 + (4 + 5)   ...
    (3 + 0) + 0 ≡ 3 + (0 + 0)   ...   (3 + 4) + 5 ≡ 3 + (4 + 5)   ...

The process continues.  On the _m_'th day we will know all the
judgments where the first number is less than _m_.

There is also a completely finite approach to generating the same equations,
which is left as an exercise for the reader.

#### Exercise `finite-|-assoc` (stretch) {#finite-plus-assoc}

Write out what is known about associativity of addition on each of the
first four days using a finite story of creation, as
[earlier]({{ site.baseurl }}/Naturals/#finite-creation).

{% raw %}<pre class="Agda"><a id="21676" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}
## Associativity with rewrite

There is more than one way to skin a cat.  Here is a second proof of
associativity of addition in Agda, using `rewrite` rather than chains of
equations:
{% raw %}<pre class="Agda"><a id="+-assoc′"></a><a id="21892" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21892" class="Function">+-assoc′</a> <a id="21901" class="Symbol">:</a> <a id="21903" class="Symbol">∀</a> <a id="21905" class="Symbol">(</a><a id="21906" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21906" class="Bound">m</a> <a id="21908" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21908" class="Bound">n</a> <a id="21910" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21910" class="Bound">p</a> <a id="21912" class="Symbol">:</a> <a id="21914" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="21915" class="Symbol">)</a> <a id="21917" class="Symbol">→</a> <a id="21919" class="Symbol">(</a><a id="21920" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21906" class="Bound">m</a> <a id="21922" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21924" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21908" class="Bound">n</a><a id="21925" class="Symbol">)</a> <a id="21927" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21929" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21910" class="Bound">p</a> <a id="21931" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="21933" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21906" class="Bound">m</a> <a id="21935" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21937" class="Symbol">(</a><a id="21938" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21908" class="Bound">n</a> <a id="21940" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="21942" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21910" class="Bound">p</a><a id="21943" class="Symbol">)</a>
<a id="21945" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21892" class="Function">+-assoc′</a> <a id="21954" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a>    <a id="21962" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21962" class="Bound">n</a> <a id="21964" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21964" class="Bound">p</a>                          <a id="21991" class="Symbol">=</a>  <a id="21994" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>
<a id="21999" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21892" class="Function">+-assoc′</a> <a id="22008" class="Symbol">(</a><a id="22009" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="22013" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22013" class="Bound">m</a><a id="22014" class="Symbol">)</a> <a id="22016" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22016" class="Bound">n</a> <a id="22018" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22018" class="Bound">p</a>  <a id="22021" class="Keyword">rewrite</a> <a id="22029" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#21892" class="Function">+-assoc′</a> <a id="22038" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22013" class="Bound">m</a> <a id="22040" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22016" class="Bound">n</a> <a id="22042" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22018" class="Bound">p</a>  <a id="22045" class="Symbol">=</a>  <a id="22048" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>
</pre>{% endraw %}
For the base case, we must show:

    (zero + n) + p ≡ zero + (n + p)

Simplifying both sides with the base case of addition yields the equation:

    n + p ≡ n + p

This holds trivially. The proof that a term is equal to itself is written `refl`.

For the inductive case, we must show:

    (suc m + n) + p ≡ suc m + (n + p)

Simplifying both sides with the inductive case of addition yields the equation:

    suc ((m + n) + p) ≡ suc (m + (n + p))

After rewriting with the inductive hypothesis these two terms are equal, and the
proof is again given by `refl`.  Rewriting by a given equation is indicated by
the keyword `rewrite` followed by a proof of that equation.  Rewriting avoids
not only chains of equations but also the need to invoke `cong`.


## Commutativity with rewrite

Here is a second proof of commutativity of addition, using `rewrite` rather than
chains of equations:
{% raw %}<pre class="Agda"><a id="+-identity′"></a><a id="22951" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22951" class="Function">+-identity′</a> <a id="22963" class="Symbol">:</a> <a id="22965" class="Symbol">∀</a> <a id="22967" class="Symbol">(</a><a id="22968" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22968" class="Bound">n</a> <a id="22970" class="Symbol">:</a> <a id="22972" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="22973" class="Symbol">)</a> <a id="22975" class="Symbol">→</a> <a id="22977" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22968" class="Bound">n</a> <a id="22979" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="22981" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="22986" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="22988" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22968" class="Bound">n</a>
<a id="22990" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22951" class="Function">+-identity′</a> <a id="23002" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="23007" class="Symbol">=</a> <a id="23009" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>
<a id="23014" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22951" class="Function">+-identity′</a> <a id="23026" class="Symbol">(</a><a id="23027" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="23031" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23031" class="Bound">n</a><a id="23032" class="Symbol">)</a> <a id="23034" class="Keyword">rewrite</a> <a id="23042" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22951" class="Function">+-identity′</a> <a id="23054" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23031" class="Bound">n</a> <a id="23056" class="Symbol">=</a> <a id="23058" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>

<a id="+-suc′"></a><a id="23064" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23064" class="Function">+-suc′</a> <a id="23071" class="Symbol">:</a> <a id="23073" class="Symbol">∀</a> <a id="23075" class="Symbol">(</a><a id="23076" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23076" class="Bound">m</a> <a id="23078" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23078" class="Bound">n</a> <a id="23080" class="Symbol">:</a> <a id="23082" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="23083" class="Symbol">)</a> <a id="23085" class="Symbol">→</a> <a id="23087" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23076" class="Bound">m</a> <a id="23089" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="23091" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="23095" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23078" class="Bound">n</a> <a id="23097" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="23099" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="23103" class="Symbol">(</a><a id="23104" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23076" class="Bound">m</a> <a id="23106" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="23108" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23078" class="Bound">n</a><a id="23109" class="Symbol">)</a>
<a id="23111" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23064" class="Function">+-suc′</a> <a id="23118" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="23123" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23123" class="Bound">n</a> <a id="23125" class="Symbol">=</a> <a id="23127" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>
<a id="23132" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23064" class="Function">+-suc′</a> <a id="23139" class="Symbol">(</a><a id="23140" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="23144" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23144" class="Bound">m</a><a id="23145" class="Symbol">)</a> <a id="23147" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23147" class="Bound">n</a> <a id="23149" class="Keyword">rewrite</a> <a id="23157" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23064" class="Function">+-suc′</a> <a id="23164" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23144" class="Bound">m</a> <a id="23166" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23147" class="Bound">n</a> <a id="23168" class="Symbol">=</a> <a id="23170" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>

<a id="+-comm′"></a><a id="23176" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23176" class="Function">+-comm′</a> <a id="23184" class="Symbol">:</a> <a id="23186" class="Symbol">∀</a> <a id="23188" class="Symbol">(</a><a id="23189" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23189" class="Bound">m</a> <a id="23191" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23191" class="Bound">n</a> <a id="23193" class="Symbol">:</a> <a id="23195" href="Agda.Builtin.Nat.html#165" class="Datatype">ℕ</a><a id="23196" class="Symbol">)</a> <a id="23198" class="Symbol">→</a> <a id="23200" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23189" class="Bound">m</a> <a id="23202" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="23204" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23191" class="Bound">n</a> <a id="23206" href="Agda.Builtin.Equality.html#125" class="Datatype Operator">≡</a> <a id="23208" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23191" class="Bound">n</a> <a id="23210" href="Agda.Builtin.Nat.html#298" class="Primitive Operator">+</a> <a id="23212" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23189" class="Bound">m</a>
<a id="23214" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23176" class="Function">+-comm′</a> <a id="23222" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23222" class="Bound">m</a> <a id="23224" href="Agda.Builtin.Nat.html#183" class="InductiveConstructor">zero</a> <a id="23229" class="Keyword">rewrite</a> <a id="23237" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#22951" class="Function">+-identity′</a> <a id="23249" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23222" class="Bound">m</a> <a id="23251" class="Symbol">=</a> <a id="23253" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>
<a id="23258" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23176" class="Function">+-comm′</a> <a id="23266" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23266" class="Bound">m</a> <a id="23268" class="Symbol">(</a><a id="23269" href="Agda.Builtin.Nat.html#196" class="InductiveConstructor">suc</a> <a id="23273" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23273" class="Bound">n</a><a id="23274" class="Symbol">)</a> <a id="23276" class="Keyword">rewrite</a> <a id="23284" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23064" class="Function">+-suc′</a> <a id="23291" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23266" class="Bound">m</a> <a id="23293" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23273" class="Bound">n</a> <a id="23295" class="Symbol">|</a> <a id="23297" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23176" class="Function">+-comm′</a> <a id="23305" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23266" class="Bound">m</a> <a id="23307" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#23273" class="Bound">n</a> <a id="23309" class="Symbol">=</a> <a id="23311" href="Agda.Builtin.Equality.html#182" class="InductiveConstructor">refl</a>
</pre>{% endraw %}In the final line, rewriting with two equations is
indicated by separating the two proofs of the relevant equations by a
vertical bar; the rewrite on the left is performed before that on the
right.


## Building proofs interactively

It is instructive to see how to build the alternative proof of
associativity using the interactive features of Agda in Emacs.
Begin by typing:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ m n p = ?

The question mark indicates that you would like Agda to help with
filling in that part of the code.  If you type `C-c C-l` (control-c
followed by control-l), the question mark will be replaced:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ m n p = { }0

The empty braces are called a _hole_, and 0 is a number used for
referring to the hole.  The hole may display highlighted in green.
Emacs will also create a new window at the bottom of the screen
displaying the text:

    ?0 : ((m + n) + p) ≡ (m + (n + p))

This indicates that hole 0 is to be filled in with a proof of
the stated judgment.

We wish to prove the proposition by induction on `m`.  Move
the cursor into the hole and type `C-c C-c`.  You will be given
the prompt:

    pattern variables to case (empty for split on result):

Typing `m` will cause a split on that variable, resulting
in an update to the code:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = { }0
    +-assoc′ (suc m) n p = { }1

There are now two holes, and the window at the bottom tells you what
each is required to prove:

    ?0 : ((zero + n) + p) ≡ (zero + (n + p))
    ?1 : ((suc m + n) + p) ≡ (suc m + (n + p))

Going into hole 0 and typing `C-c C-,` will display the text:

    Goal: (n + p) ≡ (n + p)
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ

This indicates that after simplification the goal for hole 0 is as
stated, and that variables `p` and `n` of the stated types are
available to use in the proof.  The proof of the given goal is
trivial, and going into the goal and typing `C-c C-r` will fill it in.
Typing `C-c C-l` renumbers the remaining hole to 0:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p = { }0

Going into the new hole 0 and typing `C-c C-,` will display the text:

    Goal: suc ((m + n) + p) ≡ suc (m + (n + p))
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ
    m : ℕ

Again, this gives the simplified goal and the available variables.
In this case, we need to rewrite by the induction
hypothesis, so let's edit the text accordingly:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p rewrite +-assoc′ m n p = { }0

Going into the remaining hole and typing `C-c C-,` will display the text:

    Goal: suc (m + (n + p)) ≡ suc (m + (n + p))
    ————————————————————————————————————————————————————————————
    p : ℕ
    n : ℕ
    m : ℕ

The proof of the given goal is trivial, and going into the goal and
typing `C-c C-r` will fill it in, completing the proof:

    +-assoc′ : ∀ (m n p : ℕ) → (m + n) + p ≡ m + (n + p)
    +-assoc′ zero n p = refl
    +-assoc′ (suc m) n p rewrite +-assoc′ m n p = refl


#### Exercise `+-swap` (recommended) {#plus-swap}

Show

    m + (n + p) ≡ n + (m + p)

for all naturals `m`, `n`, and `p`. No induction is needed,
just apply the previous results which show addition
is associative and commutative.

{% raw %}<pre class="Agda"><a id="26851" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}

#### Exercise `*-distrib-+` (recommended) {#times-distrib-plus}

Show multiplication distributes over addition, that is,

    (m + n) * p ≡ m * p + n * p

for all naturals `m`, `n`, and `p`.

{% raw %}<pre class="Agda"><a id="27076" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}

#### Exercise `*-assoc` (recommended) {#times-assoc}

Show multiplication is associative, that is,

    (m * n) * p ≡ m * (n * p)

for all naturals `m`, `n`, and `p`.

{% raw %}<pre class="Agda"><a id="27277" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}

#### Exercise `*-comm` {#times-comm}

Show multiplication is commutative, that is,

    m * n ≡ n * m

for all naturals `m` and `n`.  As with commutativity of addition,
you will need to formulate and prove suitable lemmas.

{% raw %}<pre class="Agda"><a id="27534" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}

#### Exercise `0∸n≡0` {#zero-monus}

Show

    zero ∸ n ≡ zero

for all naturals `n`. Did your proof require induction?

{% raw %}<pre class="Agda"><a id="27688" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}

#### Exercise `∸-|-assoc` {#monus-plus-assoc}

Show that monus associates with addition, that is,

    m ∸ n ∸ p ≡ m ∸ (n + p)

for all naturals `m`, `n`, and `p`.

{% raw %}<pre class="Agda"><a id="27886" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}

#### Exercise `+*^` (stretch)

Show the following three laws

    m ^ (n + p) ≡ (m ^ n) * (m ^ p)
    (m * n) ^ p ≡ (m ^ p) * (n ^ p)
    m ^ (n * p) ≡ (m ^ n) ^ p

for all `m`, `n`, and `p`.


#### Exercise `Bin-laws` (stretch) {#Bin-laws}

Recall that
Exercise [Bin]({{ site.baseurl }}/Naturals/#Bin)
defines a datatype of bitstrings representing natural numbers
{% raw %}<pre class="Agda"><a id="28284" class="Keyword">data</a> <a id="Bin"></a><a id="28289" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28289" class="Datatype">Bin</a> <a id="28293" class="Symbol">:</a> <a id="28295" class="PrimitiveType">Set</a> <a id="28299" class="Keyword">where</a>
  <a id="Bin.nil"></a><a id="28307" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28307" class="InductiveConstructor">nil</a> <a id="28311" class="Symbol">:</a> <a id="28313" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28289" class="Datatype">Bin</a>
  <a id="Bin.x0_"></a><a id="28319" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28319" class="InductiveConstructor Operator">x0_</a> <a id="28323" class="Symbol">:</a> <a id="28325" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28289" class="Datatype">Bin</a> <a id="28329" class="Symbol">→</a> <a id="28331" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28289" class="Datatype">Bin</a>
  <a id="Bin.x1_"></a><a id="28337" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28337" class="InductiveConstructor Operator">x1_</a> <a id="28341" class="Symbol">:</a> <a id="28343" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28289" class="Datatype">Bin</a> <a id="28347" class="Symbol">→</a> <a id="28349" href="plfa.https://agda.github.io/agda-stdlib/v1.1/Induction.html#28289" class="Datatype">Bin</a>
</pre>{% endraw %}and asks you to define functions

    inc   : Bin → Bin
    to    : ℕ → Bin
    from  : Bin → ℕ

Consider the following laws, where `n` ranges over naturals and `x`
over bitstrings:

    from (inc x) ≡ suc (from x)
    to (from x) ≡ x
    from (to n) ≡ n

For each law: if it holds, prove; if not, give a counterexample.

{% raw %}<pre class="Agda"><a id="28683" class="Comment">-- Your code goes here</a>
</pre>{% endraw %}

## Standard library

Definitions similar to those in this chapter can be found in the standard library:
{% raw %}<pre class="Agda"><a id="28820" class="Keyword">import</a> <a id="28827" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html" class="Module">Data.Nat.Properties</a> <a id="28847" class="Keyword">using</a> <a id="28853" class="Symbol">(</a><a id="28854" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11578" class="Function">+-assoc</a><a id="28861" class="Symbol">;</a> <a id="28863" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11734" class="Function">+-identityʳ</a><a id="28874" class="Symbol">;</a> <a id="28876" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11370" class="Function">+-suc</a><a id="28881" class="Symbol">;</a> <a id="28883" href="https://agda.github.io/agda-stdlib/v1.1/Data.Nat.Properties.html#11911" class="Function">+-comm</a><a id="28889" class="Symbol">)</a>
</pre>{% endraw %}
## Unicode

This chapter uses the following unicode:

    ∀  U+2200  FOR ALL (\forall, \all)
    ʳ  U+02B3  MODIFIER LETTER SMALL R (\^r)
    ′  U+2032  PRIME (\')
    ″  U+2033  DOUBLE PRIME (\')
    ‴  U+2034  TRIPLE PRIME (\')
    ⁗  U+2057  QUADRUPLE PRIME (\')

Similar to `\r`, the command `\^r` gives access to a variety of
superscript rightward arrows, and also a superscript letter `r`.
The command `\'` gives access to a range of primes (`′ ″ ‴ ⁗`).