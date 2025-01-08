# Diet Problem

A canteen has to plan the composition of the meals that it provides. A meal can be composed of the types of food indicated in the following table. Costs, in Euro per hg, and availabilities, in hg, are also indicated.

| Food  | Cost | Availability |
| :---- | ---- | ------------ |
| Bread | 0.1  | 4            |
| Milk  | 0.5  | 3            |
| Eggs  | 0.12 | 1            |
| Meat  | 0.9  | 2            |
| Cake  | 1.3  | 2            |

A meal must contain at least the following amount of each nutrient

| Nutrient | Minimal quantity |
| -------- | ---------------- |
| Calories | 600 cal          |
| Proteins | 50 g             |
| Calcium  | 0.7 g            |

Each hg of each type of food contains to following amount of nutrients

| Food  | Calories | Proteins | Calcium |
| ----- | -------- | -------- | ------- |
| Bread | 30 cal   | 5 g      | 0.02 g  |
| Milk  | 50 cal   | 15 g     | 0.15 g  |
| Eggs  | 150 cal  | 30 g     | 0.05 g  |
| Meat  | 180 cal  | 90 g     | 0.08 g  |
| Cake  | 400 cal  | 70 g     | 0.01 g  |

Give a linear programming formulation for the problem of finding a diet (a meal) of minimum total cost which satisfies the minimum nutrient requirements.

----

```python
# When using Colab, make sure you run this instruction beforehand
!pip install mip

# We need to import the package mip
import mip
```

* **Sets**:

	* $I$ = type of foods

	* $J$ = type of nutrients


* **Parameters**:

	* $c_{i}$ = cost of food $i \in I$

	* $q_{i}$ = availability of food $i \in I$

	* $b_{j}$ = minimum requirement of nutrient $j \in J$

	* $a_{ij}$ = amount of nutrient $j \in J$ in food type $i \in I$

	```python
	# Food
	I = {'Bread', 'Milk', 'Eggs', 'Meat', 'Cake'}
	
	# Nutrients
	J = {'Calories', 'Proteins', 'Calcium'}
	
	# Cost in Euro per hg of food
	c = {'Bread': 0.1, 'Milk': 0.5, 'Eggs': 0.12, 'Meat': 0.9, 'Cake': 1.3}
	
	# Availability per hg of food
	q = {'Bread': 4, 'Milk': 3, 'Eggs': 1, 'Meat': 2, 'Cake': 2}
	
	# Minimum nutrients
	b = {'Calories': 600, 'Proteins': 50, 'Calcium': 0.7}
	
	# Nutrients per hf of food
	a = {('Bread', 'Calories'): 30, ('Milk', 'Calories'): 50, ('Eggs', 'Calories'): 150, ('Meat', 'Calories'): 180, ('Cake', 'Calories'): 400, ('Bread', 'Proteins'): 5, ('Milk', 'Proteins'): 15, ('Eggs', 'Proteins'): 30, ('Meat', 'Proteins'): 90, ('Cake', 'Proteins'): 70, ('Bread', 'Calcium'): 0.02, ('Milk', 'Calcium'): 0.15, ('Eggs', 'Calcium'): 0.05, ('Meat', 'Calcium'): 0.08, ('Cake', 'Calcium'): 0.01}
	```

* **Decision Variables**:

	* $x_{i}$ = amount of food $i$

	```python
	# Define a model
	model = mip.Model()
	
	# Define variables
	x = {i: model.add_var(name=i) for i in I}
	```

* **Objective function**: 
  $$
  \displaystyle \min \sum_{i \in I}c_{i}x_{i}
  \notag
  $$

  ```python
  # Define the objective function
  model.objective = mip.minimize(mip.xsum(c[i]*x[i] for i in I))
  ```

* **Constraints**:

	* $x_{i} \leq q_{i} \quad \forall i \in I$ (Availability)

	* $\displaystyle \sum_{i \in I}a_{ij}x_{i} \geq b_{j} \quad \forall j \in J$ (Nutrients)

	```python
	# Availability constraint
	for i in I:
	    model.add_constr(x[i] <= q[i])
	
	# Minimum nutrients
	for j in J:
	    model.add_constr(mip.xsum(a[i, j]*x[i] for i in I) >= b[j])
	```

The Model is complete, let's solve it and print the optimal solution

```python
# Optimizing command
model.optimize()

# Optimal objective function value
model.objective.x  # -> 3.37

# Printing the variables values
for i in model.vars:
    print(i.name)
    print(i.x)
    print('-----')
"""
Cake
0.0
-----
Bread
3.9999999999999996
-----
Meat
1.5000000000000002
-----
Milk
3.0
-----
Eggs
1.0
"""
```



# Oil Blending Problem

A refinery has to blend 4 types of oil to obtain 3 types of gasoline. The following table reports the available quantity of oil of each type (in barrels) and the respective unit cost (Euro per barrel)

| Oil type | Cost | Availability |
| -------- | ---- | ------------ |
| 1        | 9    | 5000         |
| 2        | 7    | 2400         |
| 3        | 12   | 4000         |
| 4        | 6    | 1500         |

Blending constraints are to be taken into account, since each type of gasoline must contain at least a specific, predefined, quantity of each type of oil, as indicated in the next table. The unit revenue of each type of gasoline (Euro per barrel) is also indicated

| Gasoline type | Requirements    | Revenue |
| ------------- | --------------- | ------- |
| A             | ≥ 20% of type 2 | 12      |
| A             | ≤ 30% of type 3 | 12      |
| B             | ≥ 40% of type 3 | 18      |
| C             | ≤ 50% of type 2 | 10      |

----

* **Sets**:

	* $I$ = Type of oil

	* $J$ = Type of gasoline


* **Parameters**:

	* $c_{j}$ = Cost of oil $i \in I$

	* $q_{i}$ = Availabilty of oil $i \in I$

	* $r_{j}$ = Price of gasoline $j \in J$

	* $q_{ij}^{min}$ = Minimum amount of oil $i \in I$ in $j \in J$

	* $q_{ij}^{max}$ = Maximum amount of oil $i \in I$ in $j \in J$

	```python
	# Set of oil types
	I = range(4) # give number 0-3
	
	# Set of gasoline types
	J = {'A', 'B', 'C'}
	
	# Unit cost for oil of type i
	c = {0:9, 1:7, 2:12, 3:6}
	
	# Availability of oil type i
	b = {0:5000, 1:2400, 2:4000, 3:1500}
	
	# Price of gasoline of type j
	r = {'A':12, 'B':18, 'C':10}
	
	# Maximum quantity (percentage) of oil
	q_max = {}
	for i in I:
	  for j in J:
	    q_max[(str(i),j)] = 1
	q_max[('3','A')] = 0.3
	q_max[('2','C')] = 0.5
	
	# Minimum quantity (percentage) of oil
	q_min = {}
	for i in I:
	  for j in J:
	    q_min[(str(i),j)] = 0
	q_min[('2','A')] = 0.2
	q_min[('3','B')] = 0.4
	```

* **Decision variables**:

	* $x_{ij}$ = Amount of oil $i \in I$ goes into $j \in J$

	* $y_{j}$ = Amount of $j \in J$

	```python
	# Define a model
	model2 = mip.Model()
	
	# Define variables
	x = {(str(i), j) : model2.add_var(name=str(i) + ',' + j) for i in I for j in J}
	y = {j : model2.add_var(name=j) for j in J}
	```

* **Objective function**:
  $$
  \max \displaystyle \{\sum_{j \in J} r_{j}y_{j} - \sum_{i \in I} \sum_{j \in J}c_{i}x_{ij} \} \quad \text{(Revenue)}
  \notag
  $$

  ```python
  # Define the objective function
  model2.objective = mip.maximize(mip.xsum(r[j]*y[j] for j in J) - mip.xsum(c[i]*x[str(i), j] for i in I for j in J))
  ```

* **Constraints**:

	* $\displaystyle \sum_{j \in J}x_{ij} \leq q_{i} \quad \forall i \in I$ (Availability)

	* $\displaystyle \frac{x_{ij}}{y_{j}} \geq q_{ij}^{min}, \space \frac{x_{ij}}{y_{j}} \leq q_{ij}^{max} \quad \forall i \in I \space \forall j \in J$ (Quantity)

	* $\displaystyle \sum_{i \in I}x_{ij} = y_{j} \quad \forall j \in J$ (Conservation)

	```python
	# Availability constraint
	for i in I:
	  model2.add_constr(mip.xsum(x[str(i), j] for j in J) <= b[i])
	
	# Minimum quantity
	for i in I:
	  for j in J:
	    model2.add_constr(x[str(i), j] >= q_min[str(i), j] * y[j])
	
	# Maximum quantity
	for i in I:
	  for j in J:
	    model2.add_constr(x[str(i), j] <= q_max[str(i), j] * y[j])
	    
	# Conservation constraint
	for j in J:
	  model2.add_constr(mip.xsum(x[str(i), j] for i in I) == y[j])
	```

The Model is complete, let's solve it and print the optimal solution

```python
# Optimizing command
model2.optimize()

# Optimal objective function value
model2.objective.x

# Printing the variables values
for i in model2.vars:
  print(i.name)
  print(i.x)
  print('-----')
```



# June School Olympic Games

The students of the G. Galilei high school are enrolled in the June School Olympic Games. The instructors are planning the event schedule.

In a time span of 10 days, the students will be involved in matches of 100, 200, and 1000 meters, cross country running, discus throw, javelin throw, football, and baseball. The games will be played both in week days and week-end days.

The students have already enrolled for the sports they are interested in. Since some athlets will play matches of different disciplines, the activities must be schedules so as to entirely avoid overlaps.

The table reports, with an X, the pair of matches with at least one athlete in common. It also indicates, for each discipline, the total number of matches to be played.

| Disciplines   | 100   | 200   | 1000  | cross country | discus | javelin | football | baseball |
| ------------- | ----- | ----- | ----- | ------------- | ------ | ------- | -------- | -------- |
| 100           | -     | X     | -     | X             | -      | X       | -        | -        |
| 200           | -     | -     | X     | X             | -      | -       | -        | -        |
| 1000          | -     | -     | -     | X             | -      | -       | X        | X        |
| cross country | -     | -     | -     | -             | -      | X       | -        | -        |
| discus        | -     | -     | -     | -             | -      | X       | -        | -        |
| javelin       | -     | -     | -     | -             | -      | -       | -        | X        |
| football      | -     | -     | -     | -             | -      | -       | -        | X        |
| baseball      | -     | -     | -     | -             | -      | -       | -        | -        |
| -----         | ----- | ----- | ----- | -----         | -----  | -----   | -----    | -----    |
| Total Matches | 2     | 2     | 2     | 1             | 3      | 3       | 4        | 4        |

For each match, the instructors estimated the 'degree of interest', which is shown in the table

| Disciplines | 100  | 200  | cross country | discus | javelin | football | baseball |
| ----------- | ---- | ---- | ------------- | ------ | ------- | -------- | -------- |
| Interest    | 7    | 8    | 10            | 4      | 5       | 6        | 10       |

The matches are played in sequence, during the first 3 afternoon hours (180 minutes). For each day, at most one match per discipline can be scheduled. The length, in minutes, of a match of each discipline is reported in the table

| Disciplines | 100  | 200  | cross country | discus | javelin | football | baseball |
| ----------- | ---- | ---- | ------------- | ------ | ------- | -------- | -------- |
| Length      | 15   | 15   | 20            | 60     | 30      | 30       | 100      |

To exploit the enthusiasm of the athlets, the instructors decided that no more than 7 days must pass from the first and the last match of each discipline.

Give an integer linear programming formulation for the problem of determining a schedule for the matches such that the interest of the less interesting day is maximized. Per day, the interest amounts to the sum of the degree of interest of the matches that are played.

-----

```python
import mip
from mip import BINARY
```

- **Sets**:

    - $I$ = disciplines
    - $J = \{1,...,G\}$: days

- **Parameters**:

    - T = available time per day
    - $n$ = maximum number of days between first and last match
    - $c_{ii'}$ = 1 if $i$-th and $i'$-th matches are in conflict $i,i' \in I$
    - $m_i$ = number of matches for discipline $i \in I$
    - $w_i$ = degree of interest for discipline $i \in I$
    - $t_i$ = length of a match for discipline $i \in I$

    ```python
    # Types of matches
    I = {'hundred', 'twohundred', 'thousand', 'crossCountry','discus', 'javelin', 'football', 'baseball'}
    
    # Number of days
    G = 10
    # List of days
    J = range(1, G+1)
    
    # Available time per day
    T = 180
    
    # Maximum number of days between first and last match
    n = 7
    
    # Pair of matches with at least one athlete in common
    c = [('hundred', 'twohundred'), ('hundred', 'crossCountry'), ('hundred', 'javelin'), ('twohundred', 'thousand'), ('twohundred', 'crossCountry'), ('thousand', 'crossCountry'), ('thousand', 'football'), ('thousand', 'baseball'), ('crossCountry', 'javelin'), ('discus', 'javelin'), ('javelin', 'baseball'), ('football', 'baseball')]
    
    # Total matches
    m = {'hundred':2, 'twohundred':2, 'thousand':2, 'crossCountry':1,'discus':3, 'javelin':3, 'football':4, 'baseball':4}
    
    # Degree of interest
    w = {'hundred':7, 'twohundred':8, 'thousand':10, 'crossCountry':4,'discus':5, 'javelin':6, 'football':10, 'baseball':9}
    
    # Length of a match of each discipline
    t = {'hundred':15, 'twohundred':15, 'thousand':20, 'crossCountry':60, 'discus':30, 'javelin':30, 'football':100, 'baseball':100}
    ```

- **Decision variables**:

    - $x_{ij}$ = 1 if a match of discipline $i \in I$ is played in day $j \in J$, 0 otherwise

    - $\eta$ = interest of the day with the minimum interest among all days

    ```python
    # Model definition
    model = mip.Model()
    
    # Variables definition
    x = {(i, str(j)): model.add_var(var_type=BINARY, name = i+'_'+str(j)) for i in I for j in J}
    eta = model.add_var()
    ```

- **Objective function**:
    $$
    \max \quad \eta \qquad (\text{min interest})
    \notag
    $$

    ```python
    # Objective definition
    model.objective = mip.maximize(eta)
    ```

- **Constraints**:
  $$
  \begin{array}{lll}
  & \sum_{i \in I} x_{ij} w_i \geq \eta &  j \in J & \text{(interest)} &\\
  & x_{ij}+x_{i^*j} \leq 1 &  i,i^* \in I,j \in J: c_{ii^*}=1 & \text{(conflicts)} &\\
  & \sum_{j \in J} x_{ij}=m_i  &  i \in I & \text{(number)} &\\
  & \sum_{i \in I}t_i x_{ij}\leq T  &  j \in J & \text{(time)} &\\
  & x_{ij}+x_{ij^*} \leq 1 &  i \in I,j,j^* \in J: j - j^* > n & \text{(limit)} &\\
  & x_{ij} \in \{0,1\} &  i\in I,j \in J & \text{(binary variables)} &\\
  & \eta \geq 0 & & \text{(nonnegative variables)} &\\
  \end{array}
  \notag
  $$

  ```python
  # Interest constraint
  for j in J:
      model.add_constr(mip.xsum(w[i]*x[i, str(j)] for i in I) >= eta)
  
  # Conflicts constraint
  for j in J:
      for i in I:
          for i1 in I:
              if (i, i1) in c:
                  model.add_constr(x[i, str(j)]+x[i1, str(j)] <= 1)
  
  # Number constraint
  for i in I:
      model.add_constr(mip.xsum(x[i, str(j)] for j in J) == m[i])
  
  # Time constraint
  for j in J:
      model.add_constr(mip.xsum(t[i]*x[i, str(j)] for i in I) <= T)
  
  # Limit constraint
  for i in I:
      for j in J:
          for j1 in J:
              if (j - j1) > n:
                  model.add_constr(x[i, str(j)]+x[i, str(j1)] <= 1)
  ```

The Model is complete, let's solve it and print the optimal solution

```python
# Optimizing command
model.optimize()

# Optimal objective function value
model.objective.x

# Printing the variables values
for i in model.vars:
    if i.x != 0:
        print(i.name, i.x)
```



