---
title: Molecular representations and optimization variables
date: 2026-09-11
tags:
  - math
  - molecular modeling
  - graph theory
  - machine learning
  - optimization theory
---

# Molecular representations and optimization variables

This is the first post in a series on the **mathematical foundations of molecular modeling**. The starting point is to distinguish the chemical object being modeled, its spatial arrangement, and the parameters of a predictive model.

We then introduce molecular graphs and feature matrices, explain the roles of several matrix operations, and connect these representations to optimization and sampling. One small example, ethanol and dimethyl ether, illustrates why element identities and connectivity must be used together.

---

## 1. Chemical identity, coordinates, and model parameters

The phrase **molecular optimization** is ambiguous until we specify what changes during the calculation. Three objects will appear throughout this post.

| Symbol | Meaning | What a change can represent |
| --- | --- | --- |
| $m$ | Chemical specification of a molecule | A different chemical candidate |
| $R$ | Atomic coordinate matrix | A different spatial arrangement |
| $\theta$ | Trainable parameters of a predictive model | A different prediction for the same input |

The chemical specification $m$ includes element identities, connectivity, bond types, formal charges, and relevant hydrogen and stereochemical information. A molecular formula alone is generally insufficient to specify a molecule.

Two chemical terms help separate these ideas:

- **Constitution** describes atom identities, connectivity, and bond multiplicities.
- **Conformation** describes spatial arrangements that can primarily be interconverted through rotations around single bonds.

These definitions follow the IUPAC Gold Book entries for [constitution](https://goldbook.iupac.org/terms/view/C01282) and [conformation](https://goldbook.iupac.org/terms/view/C01258).

One chemical specification can admit many coordinate arrangements. In particular, a rigid translation or rotation changes coordinates without changing the internal conformation. A change in constitution changes $m$; coordinates for the new chemical input must be specified separately.

The notation $m$, $R$, and $\theta$ is used here to separate quantities that can participate in different stages of one computational workflow.

---

## 2. Molecular graphs and features

### 2.1 Graph representation

A graph is a pair

$$
G=(V,E),
$$

where $V$ is the set of nodes and $E$ is the set of edges. In a molecular graph, nodes represent atoms and edges represent bonds.

An unlabeled connectivity graph alone does not contain all the information required to identify a molecule. Atom and bond attributes must also be recorded. The graph and feature-matrix definitions used here follow [Hamilton, *Graph Representation Learning*, Sections 1.1 and 1.1.2](https://www.cs.mcgill.ca/~wlh/grl_book/files/GRL_Book.pdf#page=10).

An atom's **index** identifies it in a chosen ordering. This is distinct from its **atomic number** $Z$, which identifies its element.

### 2.2 Adjacency matrix

For a simple undirected graph with $N=|V|$ represented atoms, define the adjacency matrix by

$$
A\in\mathbb{R}^{N\times N},
\qquad
A_{ij}=
\begin{cases}
1, & \text{if atoms }i\text{ and }j\text{ are directly bonded},\\
0, & \text{otherwise}.
\end{cases}
$$

With no self-loops, $A_{ii}=0$. Because the graph is undirected,

$$
A=A^\top.
$$

The number of represented neighbors of atom $i$ is its degree:

$$
\deg(i)=\sum_{j=1}^{N}A_{ij}.
$$

Each undirected edge contributes twice to the sum of all matrix entries, so

$$
|E|=\frac12\sum_{i=1}^{N}\sum_{j=1}^{N}A_{ij}.
$$

**Degree and bond order describe different things.** Degree counts neighboring nodes, whereas bond order describes a bond's multiplicity. A binary adjacency matrix records a double bond as one connection, not two. Omitted hydrogen nodes do not contribute to degree.

### 2.3 Atom and bond features

An atom-feature matrix

$$
X\in\mathbb{R}^{N\times d}
$$

contains $d$ features per atom. Row $i$ of $X$ must describe the atom associated with row and column $i$ of $A$.

Features may encode element identity, formal charge, aromaticity, or other relevant information. Bond features can be stored separately, with each feature vector explicitly associated with its corresponding atom pair.

Consistent reindexing changes the arrays but not the molecule they represent. Reordering $X$ alone while leaving $A$ unchanged can misassign chemical features.

### 2.4 Hydrogens and representation size

$N$ counts the atoms explicitly represented, not necessarily all atoms in the chemical formula. Ethanol has three non-hydrogen atoms and nine atoms including hydrogens. The [RDKit documentation](https://www.rdkit.org/docs/GettingStartedInPython.html#modifying-molecules) illustrates implicit and explicit hydrogen handling.

Adding hydrogen nodes changes the atom-related dimensions of $A$, $X$, and $R$. The feature definition must also specify how hydrogen is represented.

---

## 3. What the matrix operations mean

### 3.1 Feature transformation: $XW$

Let

$$
X\in\mathbb{R}^{N\times d},
\qquad
W\in\mathbb{R}^{d\times h}.
$$

Then

$$
H=XW\in\mathbb{R}^{N\times h}.
$$

Writing $\mathbf{x}_i=X_{i,:}$ and $\mathbf{h}_i=H_{i,:}$ as row vectors,

$$
\mathbf{h}_i=\mathbf{x}_iW.
$$

The same transformation is applied independently to each atom. The number of atoms remains $N$, while the number of features changes from $d$ to $h$.

$W$ may be part of the trainable parameters $\theta$. The output columns of $H$ are transformed features; they need not retain the original interpretation of the columns of $X$.

Importantly, **$XW$ alone does not use connectivity**. Identical input rows produce identical output rows under the same $W$.

### 3.2 Neighborhood aggregation: $AX$

The product

$$
S=AX\in\mathbb{R}^{N\times d}
$$

has rows

$$
\mathbf{s}_i
=\sum_{j=1}^{N}A_{ij}\mathbf{x}_j
=\sum_{j\in\mathcal{N}(i)}\mathbf{x}_j,
$$

where $\mathcal{N}(i)$ denotes the neighbors of atom $i$.

Thus, $AX$ sums the features of neighboring atoms. With $A_{ii}=0$, it does not include the central atom itself.

This is an elementary form of neighborhood aggregation, an operation used within graph neural networks. The corresponding concepts are developed in [Hamilton, Section 5.1.3](https://www.cs.mcgill.ca/~wlh/grl_book/files/GRL_Book.pdf#page=59). Here, $AX$ has no trainable parameters and is not a complete GNN model.

### 3.3 Molecular aggregation: summing atom vectors

One way to obtain a vector for an entire molecule is

$$
\mathbf{g}=\sum_{i=1}^{N}\mathbf{h}_i\in\mathbb{R}^{h}.
$$

This sums **across atoms**, combining their row vectors component by component. It does not sum the entries within each row to produce one scalar per atom.

This operation is a simple form of graph pooling, discussed in [Hamilton, Section 5.5](https://www.cs.mcgill.ca/~wlh/grl_book/files/GRL_Book.pdf#page=72).

### 3.4 Example: ethanol and dimethyl ether

For this illustrative example, omit hydrogen nodes and order the atoms along each chain:

$$
\begin{aligned}
\text{Ethanol:}\quad
&\mathrm{C}_1-\mathrm{C}_2-\mathrm{O}_3,\\
\text{Dimethyl ether:}\quad
&\mathrm{C}_1-\mathrm{O}_2-\mathrm{C}_3.
\end{aligned}
$$

Both have the same unlabeled connectivity matrix under these orderings:

$$
A=
\begin{bmatrix}
0&1&0\\
1&0&1\\
0&1&0
\end{bmatrix}.
$$

Using the feature columns [carbon indicator, oxygen indicator],

$$
X_{\mathrm{ethanol}}=
\begin{bmatrix}
1&0\\
1&0\\
0&1
\end{bmatrix},
\qquad
X_{\mathrm{ether}}=
\begin{bmatrix}
1&0\\
0&1\\
1&0
\end{bmatrix}.
$$

Their atom-labeled molecular graphs differ even though $A$ is the same.

If we only transform each atom by a shared $W$ and sum, then

$$
\mathbf{g}
=\sum_i(XW)_{i,:}
=\left(\sum_iX_{i,:}\right)W.
$$

For both molecules,

$$
\sum_iX_{i,:}=[2,1].
$$

This representation therefore cannot distinguish them for any shared $W$: it uses element counts but ignores connectivity.

Incorporating adjacency gives

$$
AX_{\mathrm{ethanol}}=
\begin{bmatrix}
1&0\\
1&1\\
1&0
\end{bmatrix},
\qquad
AX_{\mathrm{ether}}=
\begin{bmatrix}
0&1\\
2&0\\
0&1
\end{bmatrix}.
$$

The oxygen row is row 3 in ethanol and row 2 in dimethyl ether. Its neighboring-carbon count is respectively 1 and 2.

Reading $X$ together with $AX$ therefore exposes a difference in oxygen's local environment. This does not imply that $AX$ alone uniquely identifies every molecule.

---

## 4. Coordinates, conformations, and poses

Let $\mathbf{r}_i\in\mathbb{R}^{3}$ be the position of atom $i$, written as a column vector. The coordinate matrix is

$$
R=
\begin{bmatrix}
\mathbf{r}_1^\top\\
\vdots\\
\mathbf{r}_N^\top
\end{bmatrix}
\in\mathbb{R}^{N\times3}.
$$

The distance between two atoms is

$$
d_{ij}=\|\mathbf{r}_i-\mathbf{r}_j\|_2.
$$

Under a common rigid transformation,

$$
\mathbf{r}_i'=Q\mathbf{r}_i+\mathbf{t},
\qquad
Q^\top Q=I,
\qquad
\det Q=1,
$$

where $Q$ is a proper rotation matrix and $\mathbf{t}$ is a translation vector.

For any pair of atoms,

$$
\begin{aligned}
\|\mathbf{r}_i'-\mathbf{r}_j'\|_2^2
&=\|Q(\mathbf{r}_i-\mathbf{r}_j)\|_2^2\\
&=(\mathbf{r}_i-\mathbf{r}_j)^\top
Q^\top Q(\mathbf{r}_i-\mathbf{r}_j)\\
&=\|\mathbf{r}_i-\mathbf{r}_j\|_2^2.
\end{aligned}
$$

Rigid translation and rotation preserve internal geometry even though $R$ changes.

A ligand's **pose** describes its placement and configuration relative to a receptor. If the receptor is fixed while only the ligand moves, ligand–receptor distances can change despite unchanged distances within the ligand.

Docking therefore searches over position, orientation, and relevant internal torsions. The [AutoDock Vina paper](https://doi.org/10.1002/jcc.21334) provides a concrete example of this distinction between search variables and the scoring function.

---

## 5. Three optimization problems

The operator $\min$ returns an objective value; $\operatorname{arg\,min}$ identifies the arguments attaining it. Writing an optimization problem specifies the target, not a guarantee that a numerical algorithm finds its global solution.

### 5.1 Coordinate optimization

For a fixed chemical specification $m$ and energy model $U_m$, write

$$
R^*\in
\operatorname*{arg\,min}_{R\in\mathcal{C}(m)}
U_m(R),
$$

where $\mathcal{C}(m)$ denotes the allowed coordinates under the chosen chemical specification and constraints.

Alternatively, use internal or pose variables $q$ and construct coordinates through

$$
R=R(m,q).
$$

**Gradient descent is not restricted to convex objectives.** Gradient-based methods can be applied to suitable nonconvex objectives, but global optimality is not automatically guaranteed. This distinction is discussed in [*Deep Learning*, Section 4.3](https://www.deeplearningbook.org/contents/numerical.html).

Molecular energy landscapes are generally nonconvex. For a constrained problem to be convex, convexity of both the objective and the feasible set is relevant. Convergence guarantees also depend on regularity, step-size choices, and the algorithm used.

### 5.2 Predictive-model training and inference

Given labeled examples $\{(m_i,y_i)\}_{i=1}^{n}$, a simple training objective is

$$
\theta^*\in
\operatorname*{arg\,min}_{\theta}L(\theta),
$$

where

$$
L(\theta)=\frac1n\sum_{i=1}^{n}
\bigl(f_\theta(m_i)-y_i\bigr)^2.
$$

Here, $n$ counts data pairs, whereas $N$ counts represented atoms in one molecule. The notation $f_\theta(m)$ is shorthand for a model operating on a numerical representation of $m$; a three-dimensional model may also take $R$ as input.

**Training** changes $\theta$ using data and an objective. **Inference** evaluates a new input with trained parameters held fixed:

$$
\widehat{y}=f_{\theta^*}(m_{\mathrm{new}}).
$$

Supplying a new molecule to a trained model does not, by itself, constitute training. A prediction is also distinct from a new experimental measurement. The underlying model-and-loss viewpoint is developed in [*Deep Learning*, Chapter 6](https://www.deeplearningbook.org/contents/mlp.html).

### 5.3 Molecular design and search

Let $\mathcal{M}$ be the set of allowed chemical candidates. If larger scores are better, molecular search seeks

$$
m^*\in
\operatorname*{arg\,max}_{m\in\mathcal{M}}s(m).
$$

A learned evaluator can be used as

$$
m^*\in
\operatorname*{arg\,max}_{m\in\mathcal{M}}s_{\theta^*}(m),
$$

with $\theta^*$ fixed during this search stage.

A score may also be computed without a learned model. [MolFinder](https://link.springer.com/article/10.1186/s13321-021-00501-7) illustrates evolutionary molecular search whose basic method does not require training a generative neural network.

Direct changes to atom identities and connectivity involve discrete choices. Ordinary gradients with respect to Cartesian coordinates do not directly define updates to those choices. Differentiable alternative representations or other search methods require an explicit formulation.

---

## 6. Optimization versus sampling

Optimization seeks solutions that improve an objective. Sampling seeks states distributed according to a target probability distribution $\pi$.

For a discrete state space $\mathcal{S}$,

$$
\pi(x)\ge0,
\qquad
\sum_{x\in\mathcal{S}}\pi(x)=1.
$$

Selecting a most probable state,

$$
x^*\in\operatorname*{arg\,max}_{x\in\mathcal{S}}\pi(x),
$$

is different from generating samples from the full distribution. Finite-sample frequencies need not equal the target probabilities exactly.

An optimization algorithm may use random proposals. Randomness alone does not establish that it samples an equilibrium distribution correctly. The connection between molecular energies and equilibrium probabilities requires additional statistical-mechanical concepts, beyond the scope of this introductory post.

---

## 7. Summary

| Task | Main variable | Intended result |
| --- | --- | --- |
| Coordinate optimization | $R$ or internal/pose variables $q$ | Favorable geometry or pose |
| Model training | $\theta$ | Improved predictive performance under a training objective |
| Molecular search | $m$ | A candidate with a favorable objective value |
| Sampling | Generated or visited states | Samples consistent with a target distribution |

One possible ML-guided design workflow is to train a predictor, hold its parameters fixed, and use its predictions to guide molecular search. This is one workflow rather than a prerequisite for all molecular modeling.

The matrix operations can also be summarized by their roles:

| Representation or operation | Role |
| --- | --- |
| $X$ | Input features of individual atoms |
| $H=XW$ | Shared transformation of each atom's features |
| $AX$ | Summation of neighboring atoms' features |
| $\mathbf{g}=\sum_i\mathbf{h}_i$ | Aggregation into a molecular vector |

**To understand a molecular modeling algorithm, identify the representation, the variables that change, the quantities held fixed, and the objective or target distribution.**

---

## 8. References

1. William L. Hamilton. *Graph Representation Learning*. 2020. Sections 1.1, 1.1.2, 5.1.3, and 5.5. [Author's PDF](https://www.cs.mcgill.ca/~wlh/grl_book/files/GRL_Book.pdf).
2. Ian Goodfellow, Yoshua Bengio, and Aaron Courville. *Deep Learning*. MIT Press, 2016. Chapters 2, 4, and 6. [Official online textbook](https://www.deeplearningbook.org/).
3. IUPAC Gold Book. [Constitution](https://goldbook.iupac.org/terms/view/C01282) and [Conformation](https://goldbook.iupac.org/terms/view/C01258).
4. RDKit documentation. *Getting Started with the RDKit in Python*. Sections “Looping over Atoms and Bonds” and “Modifying molecules.” [Official documentation](https://www.rdkit.org/docs/GettingStartedInPython.html).
5. Oleg Trott and Arthur J. Olson. “AutoDock Vina: improving the speed and accuracy of docking with a new scoring function, efficient optimization and multithreading.” *Journal of Computational Chemistry*, 31:455–461, 2010. [DOI: 10.1002/jcc.21334](https://doi.org/10.1002/jcc.21334).
6. Y. Kwon and J. Lee. “MolFinder: an evolutionary algorithm for the global optimization of molecular properties and the extensive exploration of chemical space using SMILES.” *Journal of Cheminformatics*, 13:24, 2021. [DOI: 10.1186/s13321-021-00501-7](https://doi.org/10.1186/s13321-021-00501-7).
