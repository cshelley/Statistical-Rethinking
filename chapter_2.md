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
*W* ∼ *B**i**n**o**m**i**a**l*(*N*,*p*):

$$P(X = x\|p) = {N \choose x}p^x (1-p)^{N-x}$$

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

  

### Generating Posteriors from the Model:

**Bayes’ theorem.** For the joint probability of the data wins and
losses (W and L) and any particular value of *p* is:

*P*(*W*,*L*,*p*) = *P*(*W*,*L*\|*p*) *P*(*p*)

This just says that the probability of *W*, *L*, and *p* is the product
of *P(W, L\|p)* and the prior probability *P(p)*.

But also,

*P*(*W*,*L*,*p*) = *P*(*p*\|*W*,*L*) *P*(*W*,*L*)

Since both right-hand sides are equal, they are also equal to each
other:

*P*(*W*,*L*\|*p*) *P*(*p*) = *P*(*p*\|*W*,*L*) *P*(*W*,*L*)

and so, **Bayes’ Theorem**:

$$P(p\|W,L) = \frac{P(W,L\|p)\\P(p)}{P(W,L)}$$

In the above, *P*(*W*,*L*) is the *average probability of the data*.

In words:

$$Posterior = \frac{Probability\\of\\the\\data\\\times\\Prior}{Average\\probability\\of\\the\\data}$$
The average probability of the data is also known as the *average
likelihood* or *marginal likelihood*. It’s job is just to standardize
the posterior:

*P*(*W*,*L*) = *E*(*P*(*W*,*L*\|*p*)) = ∫*P*(*W*,*L*\|*p*) *P*(*p*) *d**p*

**Model estimation techniques.**

1.  *Grid approximation.* We can approximate continuous posterior
    distributions by considering only a finite grid of parameter values.
    At any particular value of *p’* it’s a simple matter to compute the
    posterior probability: just multiply the prior probability of *p’*
    by the likelihood of *p’*. Repeating this procedure for each value
    in the grid generates an approximate picture of the exact posterior
    distribution.

``` r
## Globe-tossing example:

# define grid
p_grid1 <- seq(from = 0, to = 1, length.out = 20)
p_grid2 <- seq(from = 0, to = 1, length.out = 5)
p_grid3 <- seq(from = 0, to = 1, length.out = 100)

# define prior
prior1 <- rep(1, 20)
prior2 <- rep(1, 5)
prior3 <- rep(1, 100)

# compute likelihood at each value in grid
likelihood1 <- dbinom(6, size = 9, prob = p_grid1)
likelihood2 <- dbinom(6, size = 9, prob = p_grid2)
likelihood3 <- dbinom(6, size = 9, prob = p_grid3)

# compute product of likelihood and prior
unstd.posterior1 <- likelihood1*prior1
unstd.posterior2 <- likelihood2*prior2
unstd.posterior3 <- likelihood3*prior3

# standardize the posterior so it sums to 1
posterior1 <- unstd.posterior1/sum(unstd.posterior1)
posterior2 <- unstd.posterior2/sum(unstd.posterior2)
posterior3 <- unstd.posterior3/sum(unstd.posterior3)
```

``` r
par(mfrow = c(1,3))
plot(p_grid1, posterior1, type = "b", 
     xlab = "probability of water", ylab = "posterior probability")
mtext("20 points")

plot(p_grid2, posterior2, type = "b", 
     xlab = "probability of water", ylab = "posterior probability")
mtext("5 points")

plot(p_grid3, posterior3, type = "b", 
     xliab = "probability of water", ylab = "posterior probability")
```

    ## Warning in plot.window(...): "xliab" is not a graphical parameter

    ## Warning in plot.xy(xy, type, ...): "xliab" is not a graphical parameter

    ## Warning in axis(side = side, at = at, labels = labels, ...): "xliab" is not a
    ## graphical parameter

    ## Warning in axis(side = side, at = at, labels = labels, ...): "xliab" is not a
    ## graphical parameter

    ## Warning in box(...): "xliab" is not a graphical parameter

    ## Warning in title(...): "xliab" is not a graphical parameter

``` r
mtext("100 points")
```

![](chapter_2_files/figure-markdown_github/unnamed-chunk-3-1.png)

1.  *Quadratic approximation.* Under general conditions the region near
    the peak of the posterior distribution will be nearly Gaussian in
    shape. This means the posterior distribution can be usefully
    approximated by a Gaussian distribution. A Gaussian distribution is
    convenient, because it can be completely described by only two
    numbers: the location of its center (mean) and its spread
    (variance). A Gaussian approximation is called “quadratic
    approximation” because the logarithm of a Gaussian distribution
    forms a parabola, and a parabola is a quadratic function. So this
    approximation essentially represents any log-posterior with a
    parabola.
