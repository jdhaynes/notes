# Mathematics
Algorithms, Part I (Princeton, Coursera)

## Finite Arithmatic Series
Given an integer $n$, the sum of all positive integers up to and including $n$ is

$$
1+2+\cdots+n = \frac{n^2+n}{2}$$.

### Proof by Induction
Start with the hypothesis.

$$S(n)=\frac{n(n+1)}{2}$$

Proof the base case $n=1$.

$$
\begin{aligned}
S(1)&=\frac{1(1+1)}{2}\\
S(1)&=1
\end{aligned}
$$

Assume the hypothesis is correct for $S(k)$.

$$
S(k)=\frac{k(k+1)}{2}
$$

Assume the hypothesis is correct for $S(k+1)$.

$$
\begin{aligned}
S(k+1)&=(1+2+\cdots+k)+(k+1)\\
S(k+1)&=\frac{k(k+1)}{2}+(k+1)\\
S(k+1)&=\frac{k(k+1)}{2}+\frac{2(k+1)}{2}\\
S(k+1)&=\frac{k(k+1)+2(k+1)}{2}\\
S(k+1)&=\frac{(k+1)(k+2)}{2}\\
S(k+1)&=\frac{(k+1)((k+1)+1)}{2}
\end{aligned}
$$

The final expression for $S(k+1)$ is equivilent to that for $S(k)$.

## Finite Arithmetic Series of Powers of 2
Given an integer $n$, calculate the sum of all the powers of 2 up to and including $n$.

$$
\begin{aligned}
S&=1+2+4+8+\cdots+n\\
&=\sum_{n=0}^{m}q^n\\
&=\frac{1-q^{m+1}}{1-q}
\end{aligned}
$$.

The expression for the sum can be derived as below.

$$
\begin{aligned}
S&=\sum_{n=0}^{m}q^n\\
&=q^a+q^{a+1}+\cdots+q^{m}\\
&=q^a+q(q^a+q^{a+1}+\cdots+q^{m-1})\\
&=q^a+q(S+q^{m})\\
S&=q^a+qS+q^{m+1}\\
S-qS&=q^a+q^{m+1}\\
S(1-q)&=q^a+q^{m+1}\\
S&=\frac{q^a+q^{m+1}}{1-q}

\end{aligned}
$$

To calculate the number of items in the series from $n$.

$$
\begin{aligned}
2^x&=n\\
x&=\log_2n
\end{aligned}
$$

Subsitute into the sum expression for the number of items and power.

$$
\begin{aligned}
\sum_{n=0}^{\log_2n}2^n&=\frac{1-2^{\log_2n+1}}{1-2}\\
&=\frac{1-2x}{-2}\\
&=2x-1
\end{aligned}
$$

Note: if not including 1 in the series, then subtract it off. The solution becomes $2x-2$.