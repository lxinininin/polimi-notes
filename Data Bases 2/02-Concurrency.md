# TS-mono

- The scheduler has two counters: $RTM(x)$ and $WTM(x)$ for each object
- The scheduler receives read/write requests tagged with the timestamp of the requesting transaction:

  - $r_{ts}(x)$:
    - If $\textcolor{red}{ts < WTM(x)}$, the request is **rejected** and the transaction is killed
    - Else, access is **granted** and $RTM(x)$ is set to $max(RTM(x), ts)$

  - $w_{ts}(x)$:
    - If $\textcolor{red}{ts < RTM(x)}$ or $\textcolor{red}{ts < WTM(x)}$, the request is **rejected** and the transaction is killed
    - Else, access is **granted** and $WTM(x)$ is set to $ts$



# TS-multi

* The scheduler receives read/write requests tagged with the timestamp of the requesting transaction:

	* <font color=red>$r_{ts}(x)$ is always accepted.</font> A copy $x_k$ is selected for reading such that:
		- If $ts > WTM_N(x)$, then $k = N$
		- Else take $k$ such that $WTM_k(x) \leq ts < WTM_{k+1}(x)$

	==For read operations, the value reported in the $RTM(x)$ column is $max(RTM(x), ts)$==

	- $w_{ts}(x)$:
	  - If $\textcolor{red}{ts < RTM(x)}$ or $\textcolor{red}{ts < WTM(x)}$, the request is **rejected**
	  - Else a new version is **created** ($N$ is incremented) with $WTM_N(x) = ts$