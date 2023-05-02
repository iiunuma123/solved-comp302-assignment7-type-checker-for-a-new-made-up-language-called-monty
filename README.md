Download Link: https://assignmentchef.com/product/solved-comp302-assignment7-type-checker-for-a-new-made-up-language-called-monty
<br>
In this assignment we will implement part of a type checker for a new made-up language called Monty. In this assignment we are looking exclusively at the part of Monty that deals with its type system. We will define an OCaml type called typExp to encode the types of Monty. Note that Monty has a polymorphic type system.

We will use this to implement unification which is part of the <em>type reconstruction </em>algorithm for Monty. Let us recap basic facts about unification first. Unification is one of the central operations in type-reconstruction algorithms, theorem proving and logic programming systems. In the context of type-reconstruction, unification tries to find an instantiation for the free variables in two types <em>τ</em><sub>1 </sub>and <em>τ</em><sub>2 </sub>such the two types are syntactically <em>identical</em>. If such an instantiation exists, we say the two types <em>τ</em><sub>1 </sub>and <em>τ</em><sub>2 </sub>are <em>unifiable</em>.

Here is the definition of the types of Monty. Remember we are writing a program in OCaml that deals with another language (Monty). Do not confuse the types of the language Monty with OCaml types. The type definition below is written in OCaml it <em>describes </em>the types of Monty.

type typExp =

| TypInt

| TypVar of char

| Arrow of typExp * typExp

| Lst of typExp

We want to implement unification for Monty type expressions. This means finding a substitution for the type variables. A substitution is a list of pairs; each pait gives a type expression for a type variable. We say that two type expressions are <em>unifiable </em>if there is a substitution that makes them identical. This is key step in the type reconstruction algorithm.

We are using simple characters to represent type variables with the constructor TypVar prefixing it. In the substitution we just write the character and not this constructor but this is just for convenience. I have only got one base type (TypInt) and one unary type constructor (Lst) and one binary type constructor (Arrow). A type that we would write as <em>int </em>→ (‘<em>a </em>→ ‘<em>b </em>− <em>list</em>) would be written in our representation as

Arrow(TypInt, Arrow(TypVar ’a’, Lst (TypVar ’b’)))

The above is an example of a polymorphic type expression. Here is another one:

Arrow(TypVar ’a’, Arrow (TypInt, Lst (TypInt)))

These are, of course, not the same. Are they unifiable? In other words, is there some replacement for the type variables that makes these two type expressions identical? Yes there is! If we replace both the type variables with TypInt they will both be identical. This is the kind of thing that we will write our program to discover. Here is a script of the program in action.

Notice that this is more than simple pattern matching. Each type expression constrains the other. We are finding the <em>most general unifier</em>.

<strong>Question 1</strong>

In this question you will implement some of the auxiliary functions needed for unification. First of all recall that we do not allow a substitution where a variable is replaced by a type expression containing it. So we need to check if a type variable occurs in a type expression. This is called the “occur check.” Before we call occur check we strip off the TypVar constructor so we need a function of the following type. occurCheck : v:char -&gt; tau:typExp -&gt; bool

A substitution is defined as follows.

type substitution = (char * typExp) list

Now we want a function that performs a replacement of a variable with a type expression. This should have the following type.

val substitute : tau1:typExp -&gt; v:char -&gt; tau2:typExp -&gt; typExp

This replaces all occurrences of the type variable TypVar v with the type expression tau1 in the type expression tau2.

The above function just does one replacement. A substituion is a whole list of these so we need another function called applySubst val applySubst : sigma:substitution -&gt; tau:typExp -&gt; typExp applySubst should apply the substitutions in a list in order from <em>right to left</em>.

<strong>Question 2</strong>

In this question you are asked to implement a function unify which checks whether two types are unifiable and produces the unifier if there is one. It should return the most general unifier or fail with an appropriate error message.

val unify : tau1:typExp -&gt; tau2:typExp -&gt; substitution Here are some examples:

let te1 = Arrow(TypInt, Arrow(TypVar ’c’, TypVar ’a’));; val te1 : typExp = Arrow (TypInt, Arrow (TypVar ’c’, TypVar ’a’)) # let te3 = Arrow(TypVar ’a’,Arrow (TypVar ’b’,TypVar ’c’));; val te3 : typExp = Arrow (TypVar ’a’, Arrow (TypVar ’b’, TypVar ’c’)) # let result = unify te1 te3;; val result : substitution = [(’b’, TypInt); (’c’, TypVar ’b’); (’a’, TypInt)]

# applySubst result te1;;

<ul>

 <li>: typExp = Arrow (TypInt, Arrow (TypInt, TypInt))</li>

</ul>

# applySubst result te3;;

<ul>

 <li>: typExp = Arrow (TypInt, Arrow (TypInt, TypInt))</li>

</ul>