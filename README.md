# Zero-Knowledge School

Zero-knowledge proofs are a cryptographic protocol used to demonstrate that a statement is true without revealing any information beyond the statement itself. In other words, zero-knowledge proofs allow someone to prove they know something, without revealing what that something is.

Normally, the information that we are trying to keep secret is the answers to variables within a set of equations. Therefore, we are essentially proving that we know the answers to a system of equations without revealing them.

E.g I will be able to prove to you that I know the values of `x` and `y` that will make the following set of equations hold true, without revealing the values of these variables.

```
y^2 - 1 = 0
x^2 + 6x + 9 = 0
x + y = -4
```

Let's find the values of `x` and `y` together!

 - The possible values for `y` in `y^2 - 1 = 0` are `1` and `-1`.
 - The only possible value for `x` in `x^2 + 6x + 9 = 0` is `-3`.
 - Since the only possible value for `x` is `-3`, we can substitute `x` with `-3` in the third equation and then decide whether `y` should be `-1` or `1`. So `-3 + y = -4`, therefore `y = -1`.
 - Now, I can prove you with a proof `P` (Which is basically a set of numerical values) that I know the answer to theese equations without revealing you that `x=-3` and `y=-1`!

Now this was a very simple problem to make a proof for. Imagine there are billions of equations and variables that we want to solve!

People have invented many protocol for generating such proofs. Some of the very famous ones which are also popular in blockchain community are: `Groth16`, `PlonK`, `STARKs`, `Halo2` and etc.

We will not get deep in the details of these protocols. What we mostly care in this tutorial is that, ***there are ways one can prove that he knows the answer to all variables inside a set of equations, without revealing them!***


## Mimicking computation through equations!

You might say, well, it's not very useful to provide a proof for some useless math equations. What if we want to generate a proof for execution of a Python program? The answer is, although these equations seem to be useless in the first glance, they are actually pretty powerful! In fact, these simple equations turn out to be [Turing Complete]()!

This means you can translate any program in Python (Or whatever language) into these equations! They are very low-level representations of programs!

