Alphabet $\Sigma=\{a, b, \$, *\}$



# $L_1 = \{a^n b^n \space | \space n \geq 1\} \cup \{a^n b^{2n} \space | \space n \geq 1\}$

```mermaid
stateDiagram
	direction LR
	[*] --> q0
	q0 --> DPA(a^n*b^n) : ε, Z0 | Z0
	q0 --> DPA(a^n*b^2n) : ε, Z0 | Z0
```



# $L_2 = \{*a^n b^n \space | \space n \geq 1\} \cup \{\$a^n b^{2n} \space | \space n \geq 1\}$

This is a **deterministic** pushdown automata: 

```mermaid
stateDiagram
	direction LR
	[*] --> q0
	q0 --> q1 : *, Z0 | Z0
	q1 --> q2 : a, Z0 | Z0 A
	q2 --> q2 : a, A | A A
	q2 --> q3 : b, A | ε
	q3 --> q3 : b, A | ε
	q3 --> q4 : ε, Z0 | ε
	q0 --> q5 : $, Z0 | Z0
	q5 --> q6 : a, Z0 | Z0 A A
	q6 --> q6 : a, A | A A A
	q6 --> q7 : b, A | ε
	q7 --> q7 : b, A | ε
	q7 --> q4 : ε, Z0 | ε
	q4 --> [*]
	
```



# $L_3 = \{a^n * b^n \space | \space n \geq 1\} \cup \{a^n \$ b^{2n} \space | \space n \geq 1\}$

This is a **deterministic** pushdown automata: 

```mermaid
stateDiagram
	direction LR
	[*] --> q0
	q0 --> q1 : a, Z0 | Z0 A
	q1 --> q1 : a, A | A A
	q1 --> q2 : *, A | A
	q2 --> q3 : b, A | ε
	q3 --> q3 : b, A | ε
	q3 --> q4 : ε, Z0 | Z0
	q1 --> q5 : $, A | A
	q5 --> q6 : b, A | A
	q6 --> q5 : b, A | ε
	q5 --> q4 : ε, Z0 | Z0
	q4 --> [*]
```



# $L_4 = \{a^n b^n * \space | \space n \geq 1\} \cup \{a^n b^{2n} \$ \space | \space n \geq 1\}$

```mermaid
stateDiagram
	direction LR
	[*] --> q0
	q0 --> DPA(a^n*b^n(*)) : ε, Z0 | Z0
	q0 --> DPA(a^n*b^2n($)) : ε, Z0 | Z0
```



# $L = \{ww \space | \space w \in \{a,b\}^*\}$

It's complementary is $L'=\overline{\{ww \space | \space w \in \{a,b\}^*\}} = \{u \in \{a,b\}^* \space | \space \forall w \in \{a,b\}^* : ww \neq u\}$ which is recognized by a **NDPA**

$L' = \{w \in \{a,b\}^* \space | \space |w| \space \text{is odd}\} \\ \cup \{w = \alpha a \beta b \gamma \space | \space \alpha, \beta, \gamma \in \{a,b\}^* \space \text{and} \space |\beta| = |\alpha \gamma| \} \\ \cup \{w = \alpha b \beta a \gamma \space | \space \alpha, \beta, \gamma \in \{a,b\}^* \space \text{and} \space |\beta| = |\alpha \gamma| \}$

e.g. $aabbabaaabbb \in L'$

* we can select first two $a$ as $\alpha$
* last three $b$ as $\gamma$
* the middle $babaa$ as $\beta$

```mermaid
stateDiagram
	direction LR
	[*] --> q0
	q0 --> q1 : ε, Z0 | Z0
	q1 --> q2 : a, Z0 | Z0 \n b, Z0 | Z0
	q2 --> q1 : a, Z0 | Z0 \n b, Z0 | Z0
	q2 --> [*]
	q0 --> q3 : ε, Z0 | Z0
	q3 --> q3 : a, Z0 | Z0 X \n b, Z0 | Z0 X \n a, X | X X \n b, X | X X
	q3 --> q4 : a, Z0 | Z0 \n a, X | X
	q4 --> q4 : a, X | ε \n b, X | ε
	q4 --> q5 : ε, Z0 | Z0
	q5 --> q5 : a, Z0 | Z0 X \n b, Z0 | Z0 X \n a, X | X X \n b, X | X X
	q5 --> q6 : b, Z0 | Z0 \n b, X | X
	q6 --> q6 : a, X | ε \n b, X | ε
	q6 --> q7 : ε, Z0 | Z0
	q7 --> [*]
	q3 --> q8 : b, Z0 | Z0 \n b, X | X
	q8 --> q8 : a, X | ε \n b, X | ε
	q8 --> q9 : ε, Z0 | Z0
	q9 --> q9 : a, Z0 | Z0 X \n b, Z0 | Z0 X \n a, X | X X \n b, X | X X
	q9 --> q6 : a, Z0 | Z0 \n a, X | X
```

* `q0 --> q1 --> q2 --> [*]`  accepts $\{w \in \{a,b\}^* \space | \space |w| \space \text{is odd}\}$
* `q0 --> q3 --> q4 --> q5 --> q6 --> q7 --> [*]` accepts $\{w = \alpha a \beta b \gamma \space | \space \alpha, \beta, \gamma \in \{a,b\}^* \space \text{and} \space |\beta| = |\alpha \gamma| \}$
* `q0 --> q3 --> q8 --> q9 --> q6 --> q7 --> [*]` accepts $\{w = \alpha b \beta a \gamma \space | \space \alpha, \beta, \gamma \in \{a,b\}^* \space \text{and} \space |\beta| = |\alpha \gamma| \}$