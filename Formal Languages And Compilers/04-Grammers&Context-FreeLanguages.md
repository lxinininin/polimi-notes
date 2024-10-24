# Limits of Regular Languages

Simple languages such as $L=\{a^{n}b^{n} \space | \space n > 0\}$ 

* are **not regular** because regular expressions and finite automata cannot enforce a condition where the number of `a` equals the number of `b`

* It represents basic syntactic structures like

	```
	begin begin ... begin ... end ... end end
	```

	This structure also implies that you need some form of **nesting** (like matched parentheses or matching `begin` and `end` blocks). Regular languages cannot handle such **nested dependencies**



Grammars: a more powerful means to define languages

* allow for **rewriting rules** that can handle complex structures like the matching of `begin` and `end`, or the balancing of `a` and `b`
	* **Rewriting rules**: These are rules applied repeatedly to generate valid strings in the language. This means that context-free grammars can manage **recursive** structures like nested syntactic elements, something regular languages cannot do.



Example: **Languages of palindromes of odd length**

A palindrome is $L=\{uu^{R} \space | \space\ u \in {a,b}^{*}\}=\{\epsilon, aa, bb, abba, baab,...,abbbba\}$

* $P \rightarrow \epsilon$ : This rule generates an **empty palindrome**, represented by the empty string $\epsilon$
* $P \rightarrow aPa$ : This rule generates a palindrome by surrounding an existing palindrome with two `a`
* $P \rightarrow bPb$ : Similarly, this rule generates a palindrome by surrounding an existing palindrome with two `b`

A chain of **derivation steps**: $P \Rightarrow aPa \Rightarrow abPba \Rightarrow abbPbba \Rightarrow abb \epsilon bba = abbbba$

**Note: Distinguish the two metasymbols**:

* $\rightarrow$ : separates the left and right part of a rule
* $\Rightarrow$ : derivation relation (rewriting)



# Derivation & Generated Language

## Derivation relation $\Rightarrow$

For $\delta,\eta \in (V \cup \Sigma)^*$, where:

* $V$ is the set of **non-terminal symbols** (e.g., variables like $S , A , P$ )

* $\Sigma$ is the set of **terminal symbols** (e.g., the alphabet of the language)

