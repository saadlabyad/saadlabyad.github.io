# aslsd
Parametric estimation of multivariate Hawkes processes with general kernels. Under active development.

##Description
  -----------

Linear multivariate Hawkes processes (MHP) are a fundamental class of point processes with self-excitation. When estimating parameters for these processes, a difficulty is that the two main error functionals, the log-likelihood and the least squares error (LSE), as well as the evaluation of their
gradients, have a quadratic complexity in the number of observed events. In practice, this prohibits
the use of exact gradient-based algorithms for parameter estimation. 

We propose a stochastic optimization algorithm for parametric MHP estimation that does not directly evaluate the conditional intensity of the MHP; we
call this the ASLSD algorithm (Adaptively Stratified Least Squares Descent). This results in a fast parametric estimation
method for MHP with general kernels, applicable to large datasets, which compares favourably with
existing methods.

This code implements

* the ASLSD algorithm for the estimation of MHP;
* exact cluster based simulation of MHP;
* goodness-of-fit tests.

##Link
  ------------
`The aslsd package. <https://github.com/saadlabyad/aslsd>`_


# lob
Data analysis of limit order book data, statistical functions for jump processes. Under active development.

Description
-----------

Limit order books are dynamic systems which store trading messages and trading intentions in financial markets.

This code implements data analysis tools for jump processes and limit order books (with presets for Nasdaq and Euronext Amsterdam equities data feeds).

Link
------------
`The lob package. <https://github.com/saadlabyad/lob>`_
