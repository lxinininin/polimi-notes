Given a **decidable** problem, and an algorithm that solves it, we want to measure the **complexity** of that algorithm



If $f,g$ are postive functions, $f,g: \mathbb{N} \rightarrow \mathbb{R}^+$

* $f(n) = O(g(n))$ if and only if $\exist c \in \mathbb{R}^+$ , $c > 0$ , $\exist n_0 \in \mathbb{N}$ , $\forall n \geq n_0$ s.t. $f(n) \leq c \cdot g(n)$
	* $3n^2 = O(n^2)$
	* $3n^2 = O(n^2 \cdot log(n))$
	* $3n^2 = O(2^n)$
	* $3000n^2 = O(\frac{n^2}{1000})$

* $f(n) = \Theta(g(n))$ if and only if $\exists c_1, c_2 > 0$ , $\exists n_0 \in \mathbb{N}$ , $\forall n \geq n_0$ s.t. $c_1 \cdot g(n) \leq f(n) \leq c_2 \cdot g(n)$
	* $3n^2 = \Theta(n^2)$
		* $n^2 \leq 3n^2 \leq 5n^2 $
	* $(3n^2 + \log(n)) = \Theta(n^2)$
		* $3n^2 \leq (3n^2 + \log(n)) \leq 4n^2$
* $f(n) = \Omega (g(n))$ if and only if $\exist c > 0$ , $\exist n_0 \in \mathbb{N}$ , $\forall n \geq n_0$ s.t. $f(n) \geq c \cdot g(n)$
	* $n! = \Omega(2^n)$



If $\displaystyle \lim_{n \rightarrow +\infin} \frac{f(n)}{g(n)}$ **exist** then:

* $f(n) = O(g(n))$ if and only if $\displaystyle \lim_{n \rightarrow +\infin} \frac{f(n)}{g(n)} < + \infin$
* $f(n) = \Theta(g(n))$ if and only if $\displaystyle 0 < \lim_{n \rightarrow +\infin} \frac{f(n)}{g(n)} < + \infin$
* $f(n) = \Omega (g(n))$ if and only if $\displaystyle \lim_{n \rightarrow +\infin} \frac{f(n)}{g(n)} > 0$



# Determine $e^n = O / \Theta / \Omega (n \cdot 2^n)$ ?

$\displaystyle \lim_{n \rightarrow +\infin} \frac{e^n}{n \cdot 2^n} = \lim_{n \rightarrow +\infin} \frac{2^{\log_2^{e^n}}}{2^{\log_2^n} \cdot 2^n} = \lim_{n \rightarrow +\infin} 2^{n \cdot \log_2^e - n - \log_2^n} = +\infin$



# Determine $\log_2^n = O / \Theta / \Omega (\log_e^n)$ ?

$\displaystyle \lim_{n \rightarrow +\infin} \frac{\log_2^n}{\log_e^n} = \log_2^e$



# Design a (deterministic) TM that recognizes the language $L=\{w \in \{a,b\}^* \ | \ \#_a(w) = \#_b(w)\}$ and its space complexity is $O(\log(n))$ where $2n$ is the length of the input

We can recognize $L$ with DPA, but $S(n) = \Theta(n)$ and $T(n) = \Theta(n)$

Idea: a 2-tapes TM

* Write on the first tape the number of occurrences of letter $a$ in the input written in base 2

* The same for $b$ in second tape

	```mermaid
	stateDiagram
		direction LR
		[*] --> q0
		q0 --> q1 : a, Z0, Z0 | Z0, Z0, (S, R, S)
		q1 --> q1 : a, 1, Z0 | 0, Z0, (S, R, S)
		q1 --> q2 : a, _, Z0 | 1, Z0, (S, L, S) \n a, 0, Z0 | 1, Z0, (S, L, S)
		q2 --> q2 : a, 1, Z0 | 1, Z0, (S, L, S) \n a, 0, Z0 | 0, Z0, (S, L, S)
		q2 --> q0 : a, Z0, Z0 | Z0, Z0, (R, S, S)
		q0 --> q1' : b, Z0, Z0 | Z0, Z0, (S, S, R)
		q1' --> q1' : b, 1, Z0 | 0, Z0, (S, S, R)
		q1' --> q2' : b, _, Z0 | 1, Z0, (S, S, L) \n b, 0, Z0 | 1, Z0, (S, S, L)
		q2' --> q2' : b, 1, Z0 | 1, Z0, (S, S, L) \n b, 0, Z0 | 0, Z0, (S, S, L)
		q2' --> q0 : b, Z0, Z0 | Z0, Z0, (R, S, S)
		q0 --> q3 : _, Z0, Z0 | Z0, Z0, (S, S, S)
		q3 --> q3 : _, 1, 1 | 1, 1, (S, R, R) \n _, 0, 0 | 0, 0, (S, R, R)
		q3 --> q4 : _, _, _ | _, _, (S, S, S)
		q4 --> [*]
	```
	
	* `q0 --> q1 --> q2 --> q0` : Binary encoding for $\#_a(w)$
	
	* `q0 --> q1' --> q2' --> q0` : Binary encoding for $\#_b(w)$
	
	* e.g., `a b a a a b b a b b a b` $\in L$
		* for a: `Z0 0 1 1` ($[6]_2=110$)
		* for b: `Z0 0 1 1`
	
* $S(n) = O(\log n)$ for sure, let us compute the $T(n)$ :

	1. Convert $a^n \rightarrow$ base 2
	2. Convert $b^n \rightarrow$ base 2
	3. Compare $[\#_a(w)]_2$ with $[\#_b(w)]_2$

	* $\displaystyle T(n) \leq O(\log (n) + 2 \cdot (3 + 2 \cdot \sum_{m=1}^n \log (m)) =$

		* $\log (n)$ : Compare 2 binary encodings of $a$ and $b$

		* $2$ : For $a$ and $b$

		* $3$ : 3 Jumps (transitions)

		* $\displaystyle 2 \cdot \sum_{m=1}^n \log(m)$ : 2 Loops

		$\displaystyle =O(\log(n) + \sum_{m=1}^n \log(m)) = O(\log(\prod_{m=1}^n m)) = O(\log(n!)) \overset{n! \leq n^n}{=} O(\log(n^n)) = O(n \cdot log(n)) $



# Describe time/space complexity of a

## 3-tapes TM that recognizes the language $L = \{a^nb^nc^n \ | \ n \in \mathbb{N}\}$

* First, check if the input belongs to $a^*b^*c^*$ :

	* $T(n) = \Theta(n)$
	* $S(n) = \Theta(1)$

* Next, save in first tape the number of $a$ , the same for $b$ , $c$ :

	* Base 1:
		* $T(n) = \Theta(n)$
		* $S(n) = \Theta(n)$

	* Base 2:
		* $T(n) = \Theta(n)$
		* $S(n) = \Theta(\log(n))$

* Compare the tapes:

	* Base 1:
		* $T(n) = \Theta(n)$
		* $S(n) = \Theta(1)$
	* Base 2:
		* $T(n) = \Theta(\log(n))$
		* $S(n) = \Theta(1)$

* Total:

	* Base 1:
		* $T(n)=\Theta(n)$
		* $S(n) = \Theta(n)$
	* Base 2:
		* $T(n)=\Theta(n)$
		* $S(n) = \Theta(\log(n))$



## Single-tape TM that recognizes the language $L = \{a^nb^nc^n \ | \ n \in \mathbb{N}\}$

Single-tape: input tape, memory tape, output tape are in the same one tape, $S(n)$ is always $\Omega(n)$

* e.g., `a a a a a b b b b b c c c c c`
	* First iteration: mark first $a$ , $b$ and $c$
	* Second iteration: mark second $a$ , $b$ and $c$
	* ...
	* Last iteration: mark last $a$, check if next is marked $b$ ; mark last $b$ , check if next is marked $c$ ; mark last $c$, check if next is empty
* For space complexity
	* $S(n) = \Theta(n)$
* For time complexity, for each $a$ ($\#_a(w) = n$ , $|w| = 3n$) , we read $\frac{2}{3}$ symbols $2$ times (e.g., in second iteration, we move from second $a$ to second $c$)
	* $T(n) = n \cdot \frac{4}{3}n = \Theta(n^2)$
