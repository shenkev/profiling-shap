# profiling-shap

## Summary of Results

```
Effective runtimes (variables that cannot be the bottleneck in computation time are not included, they cannot be the bottleneck because xgboost training would be much slower than SHAP).

SHAP = O(N^2)

SHAP Interactions = O(N^2P^2)

Base time

N = 100  # number of samples
P = 100  # number of features
R = 100  # number of rounds
D = 30  # depth of trees

SHAP = 0.03s
SHAP Interaction = 1.87s

```

- Up to N=100,000 samples is reasonable to run for SHAP, N=10,000 for SHAP Interaction (you might get away with more if you distribute SHAP computation or use fewer than 100 trees, 30 max depth).
- Up to P=inf for SHAP (training xgboost will be slower than SHAP when P is large), P=100,000 for SHAP Interaction
- Up to R=inf for both SHAP and SHAP Interaction (training xgboost will be slower than SHAP when R is large)
- Up to D=inf for both SHAP and SHAP Interaction (training xgboost will be slower than SHAP when D is large)

## Hypothesis

O(NPTLD^2) time

O(D^2+M) memory

- D - depth
- L - number of leaves
- T - number of trees
- N - number of samples
- P - number of features

O(NP^2TLD^2) time for pair-wise interactions

## Scanning N

Default settings

```
N = 100  # number of samples
P = 100  # number of features
R = 100  # number of rounds
D = 30  # depth of trees
```

- bold means it was estimated

SHAP

| N        | Time (s)           |
| ------------- |:-------------:|
| 100      | 0.025 |
| 200      | 0.076 |
| 400      | 0.275 |
| 1000      | 1.68      |
| 10000 |   245.5    |


SHAP Interaction

| N        | Time (s)           |
| ------------- |:-------------:|
| 100      | 1.87 |
| 200      | 6.85 |
| 400      | 45.6 |
| 1000      |   319.7    |
| 10000 | **34357** (9.5 hours)     |

Conclusion

- algorithm for SHAP is quadratic, not linear like we thought O(N^2)
- algorithm for SHAP interaction is also quadratic O(N^2)

## Scanning P

Default settings

```
N = 100  # number of samples
P = 100  # number of features
R = 100  # number of rounds
D = 30  # depth of trees
```

- bold means it was estimated

SHAP

| P        | Time (s)           |
| ------------- |:-------------:|
| 100      | 0.023 |
| 200      | 0.033 |
| 400      | 0.05 |
| 1000      | 0.039      |
| 10000 |   0.059    |
| 1000000 |   1.98    |


SHAP Interaction

| P        | Time (s)           |
| ------------- |:-------------:|
| 100      | 1.53 |
| 200      | 3.19 |
| 400      | 5.22 |
| 1000      |   11.77    |
| 10000 |   309   |

Conclusion

- algorithm for SHAP is linear with a tiny constant or logarithmic but for all intents and purposes O(1)
- algorithm for SHAP interaction is quadratic in P O(P^2)

## Scanning R

Default settings

```
N = 100  # number of samples
P = 100  # number of features
R = 100  # number of rounds
D = 30  # depth of trees
```

- bold means it was estimated

SHAP

| R       | Time (s)           |
| ------------- |:-------------:|
| 100      | 0.030 |
| 200      | 0.037 |
| 400      | 0.02 |
| 1000      | 0.028      |
| 1000      | 0.028      |
| 100000      | 0.078      |


SHAP Interaction

| R       | Time (s)           |
| ------------- |:-------------:|
| 100      | 1.822 |
| 200      | 1.8 |
| 400      | 1.68 |
| 1000      |   1.87    |
| 100000      | 8.2      |

Conclusion

- algorithm for SHAP is effectively constant in R, not linear like we thought O(1)
- algorithm for SHAP interaction is effectively constant in R, not linear like we thought O(1)

## Scanning D

Default settings

```
N = 100  # number of samples
P = 100  # number of features
R = 100  # number of rounds
D = 30  # depth of trees
```

- bold means it was estimated

SHAP

| D       | Time (s)           |
| ------------- |:-------------:|
| 30      | 0.033 |
| 300      | 0.027 |
| 3000      | 0.032 |

SHAP Interaction

| D       | Time (s)           |
| ------------- |:-------------:|
| 30      | 1.64 |
| 300      | 2.14 |
| 3000      | 1.56 |

Conclusion

- algorithm for SHAP is effectively constant in D, not linear like we thought O(1)
- algorithm for SHAP interaction is effectively constant in D, not linear like we thought O(1)
