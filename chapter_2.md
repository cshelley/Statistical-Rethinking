### Definitions:

A **parameter** is a conjectured proportion, *p*.

A **likelihood** is the relative number of ways that a value *p* can
produce the data, derived by enumerating all the possible data sequences
that could have happened and then eliminating those sequences
inconsistent with the data

A **prior probability** is the prior plausibility of any specific *p*

A **posterior probability** is the new, updated plausibility of any
specific *p*

### Building a model:

We will use a three-step process: (1) **Data Story** - motivate the
model by narrating how the data might arise, either through a
*descriptive* or a *causal* theory. Regardless of type, the data story
is complete in the sense that it is sufficient for specifying an
algorithm for simulating new data, (2) **Update** - educate the model by
providing data, and (3) **Evaluate** - revise the model in light of the
data.

For every parameter you intend your Bayesian model to consider, you must
provide a distribution of prior plausibility, it’s “PRIOR”. Two common
priors include the binomial and the uniform:

The uniform distribution, denoted *p* ∼ *U**n**i**f**o**r**m*(*A*,*B*):

$$ 
P(A,B\|p) = \frac{1}{B-A} 
$$

The binomial distribution, denoted
*H* ∼ *B**i**n**o**m**i**a**l*(*N*,*p*):

$$
P(N\|p) = \frac{N!}{H!(N-H)!}p^H(1-p)^{N-H}
$$

In R:

``` r
dunif(3, min = 0, max = 7)      # min = A, max = B
```

    ## [1] 0.1428571

``` r
dbinom(6, size = 9, prob = 0.5) # size = N, prob = p
```

    ## [1] 0.1640625

The `d` in `dbinom` stands for density. There is also `rbinom`
(generates random samples) and `pbinom` (for cumulative probabilities).
