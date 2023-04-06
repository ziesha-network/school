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

## Hello World!

Ziesha uses Bellman library for verifying Groth16 proofs, therefore it might be preferable to develop ZK circuits through this library as well.

We are going to develop a very simple (Yet useful!) circuit using this library. Imagine I have a very large number `N` and I want to prove that this number is NOT a prime number! Normally I can do this by providing two number `p` and `q` such that `N=pq`. But what if I don't want to reveal `p` and `q` to the verifier? I can build a Zero-Knowledge proof for this!

So basically what I need to do is to prove that ***I KNOW*** two numbers `p` and `q` such that `N=pq` **WITHOUT** revealing `p` and `q`. Let's begin!

First things first, make sure Rust toolchain is installed on your machine!

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Then initialize your first circuit by running:

```bash
cargo init mycircuit --bin
cd mycircuit
```

Edit `Cargo.toml` file and make sure you have the required dependencies:

```toml
[dependencies]
bellman = "0.14.0"
ff = "0.13"
bls12_381 = "0.8"
rand = "0.8.5"
```

You can see a prototype of such a circuit in the following piece of code. As an example, we want to prove that the number `369` is not prime. We can generate a proof for this claim using `p=123` and `q=3`.

The circuit has 3 variables `p`, `q` and `n`, where `n` is public but `p` and `q` are secrets.

```rust
use bellman::groth16::{
    create_random_proof, generate_random_parameters, prepare_verifying_key, verify_proof,
};
use bellman::{Circuit, ConstraintSystem, SynthesisError};
use bls12_381::{Bls12, Scalar};
use ff::PrimeField;
use rand::thread_rng;

#[derive(Debug, Default, Clone)]
pub struct NotPrime<S: PrimeField> {
    pub witness_p: Option<S>, // Secret ;)
    pub witness_q: Option<S>, // Secret ;)
    pub input_n: Option<S>,   // Public :D
}

impl<S: PrimeField> Circuit<S> for NotPrime<S> {
    fn synthesize<CS: ConstraintSystem<S>>(self, cs: &mut CS) -> Result<(), SynthesisError> {
        let n = cs.alloc_input(
            || "n",
            || self.input_n.ok_or(SynthesisError::AssignmentMissing),
        )?;
        let p = cs.alloc(
            || "p",
            || self.witness_p.ok_or(SynthesisError::AssignmentMissing),
        )?;
        let q = cs.alloc(
            || "q",
            || self.witness_q.ok_or(SynthesisError::AssignmentMissing),
        )?;
        cs.enforce(|| "p * q == N", |lc| lc + p, |lc| lc + q, |lc| lc + n);
        Ok(())
    }
}

fn main() {
    let mut rng = thread_rng();
    let params = {
        let c = NotPrime {
            witness_p: None,
            witness_q: None,
            input_n: None,
        };

        generate_random_parameters::<Bls12, _, _>(c, &mut rng).unwrap()
    };
    let pvk = prepare_verifying_key(&params.vk);

    let c = NotPrime {
        witness_p: Some(Scalar::from(123)),
        witness_q: Some(Scalar::from(3)),
        input_n: Some(Scalar::from(369)),
    };
    let proof = create_random_proof(c, &params, &mut rng).unwrap();

    let inputs = [Scalar::from(369)];

    assert!(verify_proof(&pvk, &proof, &inputs).is_ok());
}
```

Wait a minute! Is this enough? I can generate proof for non-prime numbers too! All I need to do is set `p=N` and `q=1`!

I can prevent such behavior by adding some constraints that prevent `p` and `q` to be set to `1`. Think a bit about how we can do this before proceeding to the solution!

## Mimicking computation through equations!

You might say, well, it's not very useful to provide a proof for some useless math equations. What if we want to generate a proof for execution of a Python program? The answer is, although these equations seem to be useless in the first glance, they are actually pretty powerful! In fact, these simple equations turn out to be [Turing Complete]()!

This means you can translate any program in Python (Or whatever language) into these equations! They are very low-level representations of programs!
