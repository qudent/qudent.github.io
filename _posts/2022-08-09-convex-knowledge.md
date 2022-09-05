---
title: 'The \mathbb{R}-algebra of quasiknowledge and convex optimization'
date: 2022-08-09
permalink: /posts/2022/08/convex-knowledge/
tags:
  - cs.ml
  - cs.cc
  - quant-ph
---
---
title: 'The \mathbb{R}-algebra of quasiknowledge and convex optimization'
date: 2022-08-09
permalink: /posts/2022/07/convex-knowledge/
tags:
  - cs.ml
  - cs.cc
  - quant-ph
---
(older version [here](https://github.com/qudent/qudent.github.io/blob/935968fec7d4e89e7953f460d1c2b1093bf0da6b/_posts/2022-07-25-convex-knowledge.md).)

## 1. Abstract
In a bid to apply methods from convex optimization and duality to optimal learning/control problems, I develop a convex description of a classical or quantum learner's or agent's state of knowledge about its evironment.

### 1.1 Intuition and motivation
In the foundations of quantum physics, [generalized probabilistic theories](https://en.wikipedia.org/w/index.php?title=Generalized_probabilistic_theory&oldid=1095657221) are a family of mathematical structures that generalize both quantum physics and classical probability theory, and - to a limited degree - allow reasoning about quantum and classical systems using the same algebraic manipulations. The aim of this note is to develop a similarly general structure to describe the notion of _an agent's knowledge about its environment, and the actions it can perform to learn about and manipulate that environment_. For example, this framework could be applicable if one **trains a machine learning model and wants to determine the "most useful questions" to ask human labelers or the real-world environment,** if compute is much cheaper than gathering that data. I'll focus on situations where the agent has **unbounded internal computational power,** and is only restricted by the interface with the environment.

In probability theories, one would describe the "state of knowledge" by a joint probability distribution over the environment's and the agent's internal memory states. The approach I present is different in **two basic ways:**

1. **Equivalent joint probability distributions are the same state of knowledge:** In terms of "how much the agent knows about its environment", many joint probability distributions should be considered equivalent - for example, if the environment and the internal memory are both described by one bit, the joint probability matrices[^1] $\begin{pmatrix}0.5 & 0\\0 & 0.5\end{pmatrix}$ and $\begin{pmatrix}0 & 0.5\\0.5 & 0\end{pmatrix}$ both correspond to the agent having complete knowledge of the environment. We will formalize this idea by an equivalence relation over which we take equivalence classes - essentially letting states be equivalent if the agent can transform them into each other without interacting with the environment. Then only distinct equivalence classes correspond to distinct "states of knowledge".
2. **The addition used for convex mixtures is a direct, not entrywise sum:** A basic feature of classical probability distributions and mixed quantum states is _convexity_: If $A$ and $B$ are valid probability distributions, transition matrices or similar, and $0\leq p \leq 1,$ an object denoted by $c_p(A,B):=pA+(1-p)B$ exists and is to be interpreted as "the result of choosing $A$ with probability $p$ and $B$ with probability $1-p$". In probability theory, $A$ and $B$ are some vectors or matrices, $pA$ denotes the **entrywise** product with $p,$ and the $+$ sign denotes the **entrywise sum** of these objects.

   In our notion of "states of knowledge", a "convex mixture" $c_p(A,B)=pA+(1-p)B$ should correspond to the agent "being in state of knowledge $A$ with probability $p,$ and in state of knowledge $B$ with probability $1-p.$" **That wouldn't work if we just took elementwise convex combinations of the joint probability distributions:** Reusing the example from 1., $\frac{1}{2} \begin{pmatrix}0.5 & 0\\0 & 0.5\end{pmatrix}+\frac{1}{2}\begin{pmatrix}0 & 0.5\\0.5 & 0\end{pmatrix}=\begin{pmatrix}0.25 & 0.25\\0.25 & 0.25\end{pmatrix}$ corresponds to a state of complete ignorance even though the constituent matrices correspond to perfect knowledge. We need the system to remember _which_ constituent matrix was chosen. To do this, we use **direct sums:** Interpreting $\oplus$ as a direct sum of the columns, $\frac{1}{2} \begin{pmatrix}0.5 & 0\\0 & 0.5\end{pmatrix} \oplus \frac{1}{2}\begin{pmatrix}0 & 0.5\\0.5 & 0\end{pmatrix}=
\begin{pmatrix}
0.25 & 0    &    0 & 0.25\\
0    & 0.25 & 0.25 &    0
\end{pmatrix}.$
   We interpret the memory space of the right-hand side matrix as containing an additional bit that stores whether the knowledge is encoded as in the left or as in the right summand. It will turn out that the right-hand side matrix is the state of complete knowledge as well - in other words, another representative of the equivalence class that contains $\begin{pmatrix}0.5 & 0\\0 & 0.5\end{pmatrix}$ and $\begin{pmatrix}0 & 0.5\\0.5 & 0\end{pmatrix}$. This is as it should be, as having complete knowledge in either of two ways, with probability $1/2$ each, is equivalent to having complete knowledge with probability $1.$
   
   So in our formal algebraic structure of "states of knowledge" describing classical physics, multiplication with a nonnegative scalar is defined elementwise, and addition is implemented by a direct sum. If we find a way to subtract states of knowledge as well (or multiply with a negative scalar), we have all the ingredients needed for a real vector space (if everything fulfills the axioms). To make states subtractable, we use a _Grothendieck group construction_, which is a consistent way to add enough negatives to an algebraic structure that everything has an inverse (just like negative numbers were abstracted from natural numbers as "numbers that don't actually exist, but are useful for calculations"). In the end, the physical states of knowledge are a convex subset of a vector space.
   
I see two **applications:**

   1. We will be able to describe the problem of finding an optimal strategy to manipulate or learn about the environment as a **convex optimization problem** - and hopefully use duality to find lower bounds for the agent's ability to do so. We can describe results from quantum complexity theory, in particular [this paper by Barnum-Saks-Szegedy](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.113.1101&rep=rep1&type=pdf) and the related [quantum adversary bound-universal query algorithm duality](https://arxiv.org/abs/1504.06943), in our formalism - and plugging in the definitions, the convex optimization problems therein generalize to classical or decoherent-quantum situations. However, compared to the coherent-quantum case, I see no good bounds on the dimension of the involved vector spaces in the classical or decoherent-quantum case.[^2]
   2. Leaving mathematical rigour a bit (as we don't define infinite sums or limits, at least in this note), we can also describe **formal differential equations** and construct power series that describe e.g. the evolution of a learner's knowledge over time when it observes a Poisson process generating experimental data. This will involve a notion of multiplication of "states of knowledge", corresponding to an agent having access to multiple states of knowledge in parallel (e.g. multiple observations gathered at different times). So in fact, we are dealing with something called an $\mathbb{R}$-algebra (which we can always interpret as a vector space as well).

### 1.2. Outline
In sections 2-5, I develop the concrete situations of classical, pure quantum, and mixed quantum physics and obtain structures $\mathcal{S}^\pm_{\mathrm{class}},$ $\mathcal{S}^\pm_{\mathrm{quant}},$ $\mathcal{S}^\pm_{\mathrm{decoh}}.$ More precisely, section 2 defines sets, sections 3-4 structure on that sets, and section 5 an equivalence relation that makes equivalent "states of quasiknowledge" equal. Then section 5 mentions the algebraic axioms that these structures fulfill. The remaining sections discuss applications, in particular, section 6 is supposed to be a simple, concrete example.

**Remark:** If you want to speed up reading or don't know quantum physics, you can skip all mentions of non-classical situations. The example in section 6 may also be helpful.

## 2. Sets before modding out the equivalence relation
Consider an agent within an environment that could be in a state $e\in E,$ with some internal memory state $m\in M.$ For a fixed $E$, we define sets of joint, unnormalized "generalized probability destributions", which will turn into our desired notions of "states of knowledge" after modding out equivalence relation in section 5. We can do this for classical probability theory (probability vectors), pure quantum states (quantum wavefunctions), and mixed quantum states (density matrices):[^3]

$\left.\begin{array}
\mathcal{S}'^E_{\mathrm{class}}\\
\mathcal{S}'^E_{\mathrm{quant}}\\
\mathcal{S}'^E_{\mathrm{decoh}}
\end{array}\right\}:=
\left\{(M,P)\mid |M|<\infty, P\in\left\{
\begin{array}
((\mathbb{R}^+)^{E\times M}\\
\mathbb{C}^{E\times M}\\
\mathbb{S}^{E\times M}
\end{array}\right\}
\right\}.$

$\mathbb{R}^+$ denotes the **nonnegative** real numbers;  $\mathbb{S}^{E\times M}$ the complex positive semidefinite matrices on the complex vector space $\mathbb{C}^{E\times M}.$ Note that

1. we do not fix $M$, or limit its sizes beyond requiring it is finite), and
2. we don't enforce normalization of our distributions.

We leave out the superscript $E$ and/or the subscript $\mathrm{class},$ $\mathrm{quant},$ $\mathrm{decoh}$ if they are irrelevant.

We define $\mathcal{S}'^\pm:=\mathcal{S}'\times\mathcal{S}'$ (for classical, coherent-quantum, or decohering-quantum states). Denote a tuple $(S'_1,S'_2)\in\mathcal{S}'^\pm$ by $S'_1-S'_2$ and interpret it as a formal difference accordingly.

As discussed in the outline, we spend the next sections defining more structure on the sets before modding out the right equivalence relation in section 6.

## 3. Set inclusions (injective maps)
We define maps that will play the role of inclusion maps after our equivalence relation (i.e. allow us to consider some sets as subsets of other sets - will be/stay injections and form nothing but commutative diagrams when concatenated):

### 3.1. Classical and pure quantum as mixed quantum states
The inclusions are

1. $\mathcal{S}'_\mathrm{class}\to\mathcal{S}'_\mathrm{decoh},$ $(M,P)\to( (P_{e,m}\delta_{e,e'}\delta{m,m'})_{(e,m),e',m'})$ (in words, treating a classical as a completely decohered state),
2. $\mathcal{S}'_\mathrm{quant}\to\mathcal{S}'_\mathrm{decoh},$ $(M,P)\to((P_{e,m}P^*_{e',m'})_{(e,m),(e',m')})$ (in words, treating a pure quantum state as a quantum state with no decoherence).

### 3.2. Knowledge as quasiknowledge
$\mathcal{S}'\to\mathcal{S}'^\pm,$ $S'\to S'-0.$

### 3.3. Elements of E
We want to consider $e\in E$ as the state of knowledge in which the environment is guaranteed to be in state $e,$ with probability $1.$

1. $E\to\mathcal{S}'_\mathrm{class},$ $e\to(\{0\},(\delta_{e',e})_{(e',0)\in E\times \{0\}}))$

(where $\{0\}$ is an arbitrary 1-element memory space). The situation is the same in the pure-quantum or mixed-quantum case.

### 3.4. Real numbers
$\mathbb{R}^+\to \mathcal{S}'_\mathrm{class}$ maps $r$ to an all-$r$ matrix, again with trivial memory space. $\mathbb{R}^+\to  \mathcal{S}'_\mathrm{quant},$ $\mathcal{S}'_\mathrm{decoh}$ map to an all-$\sqrt{r}$ matrix with trivial memory space.

We embed real numbers $-r'<0$ in $\mathcal{S}'^\pm$ as $0-r',$ i.e. as the negation of the embedding of $r'.$

## 4. Operations
We now define some operations on the $\mathcal{S}'$ and $\mathcal{S}'^\pm.$

1. **Addition,** $+,$ is to be interpreted as the environment+agent being in either of the situations that the summands describe, and defined with **direct sums**:
   1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_1)+(M_2,P_2):=(M_1\biguplus M_2, P'),$ with $M_1\biguplus M_2$ denoting the disjoint union of $M_1$ and $M_2$ and the elements of $P'$ composed of the elements of $P_1$ and $P_2.$
   2. In $\mathcal{S}'_{\mathrm{quant}},$ the definition is equivalent.
   3. In $\mathcal{S}'_{\mathrm{decoh}},$ $(D_1,M_1,\Psi_1) + (D_2,M_2,\Psi_2) := (D_1 \biguplus D_2, M_1 \biguplus M_2, \Psi'),$ with the entries of $\Psi'$ taken from $\Psi'_1,\Psi'_2$ if $d$ and $m$ belong to the same summand and set to $0$ otherwise.

   In the $\mathcal{S}'^\pm,$ $(A-B) + (C-D) := ((A+C) - (B+D))$ as expected.
2. We denote **multiplication** on the $\mathcal{S}'$ by concatenating the factors and interpret it as the model having access to two pieces of knowledge independently. That is:
   1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_1)(M_2,P_2) := (M_1 \times M_2, P')$ with $P'_{e, (m_1, m_2)} := (P_1)_{e, m_1} (P_2)_{e, m_2},$
   2. equivalently in $\mathcal{S}'_{\mathrm{quant}},$
   3. and in $\mathcal{S}'_{\mathrm{decoh}},$ $(D_1,M_1,\Psi_1) (D_2,M_2,\Psi_2) := (D_1 \times D_2, M_1 \times M_2, \Psi')$ with $\Psi'_{(d_1, d_2), e, (m_1, m_2)} := (\Psi_1)_{d_1, e, m_1}(\Psi_2)_{d_2, e, m_2}.$

   In the $\mathcal{S}'^\pm,$ $(A-B)(C-D) := (AC+BD) - (AD+BC).$
3. Remember that when we consider different possible environments, we denote our sets by $\mathcal{S}'^E$ and similar. Considering a bipartite environment $E\times C,$ we define a **"partial trace"** $\mathrm{tr}_C \colon \mathcal{S}'^{E\times C}\to \mathcal{S}'^E.$ This works by considering the $C$ register, which previously was part of the environment, as part of the memory: it maps $M\to M\times C,$ copies the entries of $P$ or $\Psi$ and (in the decohering case) leaves $D$ untouched. After modding out an equivalence relation in section 6, this will look more like the partial trace  - after making some information accessible to the agent, the state of knowledge becomes equivalent with respect to all transformations that it can perform on that information.[^7]

   Of course, on the $\mathcal{S}'^\pm,$ the $\mathrm{tr}_C (A-B):= \mathrm{tr}_C A - \mathrm{tr}_C B.$

   The partial trace will be useful to discuss the interface between agent and the environment, i.e. discuss the possibility that the agent makes observations or performs actions that influence the next system state.
   
4. We define a **preorder** $S_1 \leq S_2$ to capture the notion that the agent can trivially transform a state into another state, as follows:
   1. In $\mathcal{S}'_{\mathrm{class}},$ $(M_1,P_2 T^T) \leq (M_2,P_2)$ for all transformation matrices on the memory $T \in {(\mathbb{R}^+)}^{M_1 \times M_2}$ with $\Vert T\Vert_1\leq 1.$ $P_2 T^T$ is the usual matrix product of $P_2$ with the transpose of $T.$ One can check that this corresponds to applying $T$ on the $M$ subsystem; this is an awkward technicality related to us not using graphical notation. The latter is the [1-norm of $T,$ i.e. the maximal column sum.](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms) This means that starting from any state $m_2\in M_2,$ the probabilities of transitioning to a final state must sum to **at most** one; if it is $<1$ for some column, we interpret this as the agent giving up with a certain probability. If _all_ column sums were $1,$ we'd get a "proper" transition matrix that doesn't lose probability mass.
   2. The definition in $\mathcal{S}'_{\mathrm{quant}}$ is analogous (with $\mathbb{R}^+$ replaced by $\mathbb{C}$) except that our constraint on $T$ is that $\Vert T\Vert_2\leq 1,$ using the [2-norm of $T$](https://en.wikipedia.org/w/index.php?title=Matrix_norm&oldid=1100495446#Matrix_norms_induced_by_vector_p-norms). This means that the maximal singular value of $T$ is $\leq 1.$ Analogously to 1., _all_ singular values being $1$ is equivalent to $T$ being an isometry, i.e. a "proper" quantum transition matrix. If some singular values of $T$ are $<1,$ the block-matrix $\begin{pmatrix}T\\\\ \sqrt{I-T^\dagger  T}\end{pmatrix}$ is an isometry nevertheless; we can imagine the agent gives up if it lands in the lower block of that matrix and/or consider $T$ to be one [Kraus operator of a quantum channel](https://en.wikipedia.org/w/index.php?title=Quantum_operation&oldid=1094787035#Statement_of_the_theorem).
   3. In $\mathcal{S}'_{\mathrm{decoh}},$ we want to consider transitions involving both $D$ and the memory. So we have $(D_1,M_1, T_D(\Psi_2 T^T_M)) \leq (D_2, M_2, \Psi_2),$ where $T_D(\Psi_2 T_M^T))$ denotes
	   1. The application of some $T_M \in \mathbb{C}^{M_2 \times (M_1 \times D_M)}$ to the memory space of $\Psi_2,$ where $D_M$ is an intermediate decohering space and $\Vert T_M\Vert_2\leq 1,$
	   2. following that, the application of $T_D\in \mathbb{C}^{D_1\times (D\times D_M)}$ to $D$ and $D_M,$ with $\Vert T_D\Vert_2\leq 1$ as well. So $T_M$ transforms the memory and generates a register $D_M$ that becomes part of the decohering space, and $T_D$ is an arbitrary transformation of that decohering space, which is **not necessarily an isometry either**.[^9]

   On $\mathbb{S}'^\pm,$ $(A-B)\leq (C-D)$ iff $A+D\leq B+D.$

## 5. The equivalence relation and the algebra
Finally, we define an **equivalence relation** on the $\mathcal{S}'$ and $\mathcal{S}'^\pm$ by $X\sim Y\leftrightarrow (X\leq Y \wedge Y\leq X),$ and call the equivalence classes the spaces of **states of knowledge** $\mathcal{S}$ and **states of quasiknowledge** $\mathcal{S}^\pm.$[^10]

It is tedious but mostly straightforward to check that all our operations behave well with this equivalence relation and $\mathcal{S}\subseteq\mathcal{S}^\pm$ becomes a **convex cone as a subset of an associative, commutative ordered $\mathbb{R}$-algebra**.[^13] In particular, this means:

1. For all operations, choosing different representatives of the same equivalence class leads to equivalent results, 
2. $\mathcal{S}^\pm$ is a commutative ring, fulfilling the ring axioms enumerated [here,](https://en.wikipedia.org/w/index.php?title=Ring_(mathematics)&oldid=1107386958#Definition) as well as the axiom that $*$ is commutative. Copying the content of the link, for any $A,B,C\in\mathcal{S}^\pm,$
   1. $(\mathcal{S}^\pm,+)$ is an Abelian (commutative) group, i.e.
	   1. $+$ is associative, $A+(B+C)=(A+B)+C,$
	   2. $+$ is commutative (abelian), $A+B=B+A,$
	   3. There is a neutral element $0$ such that $A+0=A,$
	   4. each $A$ has an inverse $-A$ such that $A+(-A)=0.$
   2. $(\mathcal{S}^\pm,*)$ is a monoid with neutral element $1$, i.e. 
	   1. $*$ is associative as above,
	   2. $*$ is commutative as above (this is what is meant by "commutative ring"),
	   2. There is a neutral element $1$ 
   3. the distributive law holds, i.e. $A(B+C)=(AB)+(AC);$ $(A+B)C=(AC)+(BC).$
4. 
   3. 

The partial trace $\mathrm{tr}_C\colon\mathcal{S}^{\pm (E\times C)}\to \mathcal{S}^{\pm E}$ is a **linear map**[^14] that surjectively maps states of knowledge to states of knowledge (i.e. the image $\mathrm{tr}_C(\mathcal{S}^{E\times C})=\mathcal{S}^{E}$). Conversely, however, the preimage of a SOK _does_ generally contain non-SOKs. $\mathrm{tr}_C$ is also monotonous in $\leq.$

Note that after the equivalence relation, the set inclusion $\mathbb{R}\subseteq \mathcal{S}^{\pm\{0\}}$ defined in 3.3. is actually _surjective_ for a 1-element $E=\{0\}.$ So it makes sense to define a "total trace" $\mathrm{tr}\colon \mathcal{S}^{\pm}\to\mathbb{R},$ which treats the environment $E$ as $\{0\}\times E$ and calculates the partial trace over $E.$ Interpreting the result as an element of $\mathbb{R},$ this returns the $1$-norm/squared $2$-norm of a SOK.


## 6. An example: determining the bias of a coin
God has chosen a biased (classical) coin which either shows heads with $p=0.6$ or tails with $p=0.6,$ the prior probabilities for each are equal. The "agent" (in this simple example) can't make any decisions to influence the environment, it only observes a sequence of coin flips.

The environment is a 2-element set $E=\{\mathrm{HeadsBiased},\mathrm{TailsBiased}\};$ denote this by $E=\{0,1\}$ for simplicity. One representative for the initial state of knowledge $S_0$ is $(M,P)$ with $M=\{0\}$ and $P=\begin{pmatrix}1/2 \\1/2\end{pmatrix}=\frac{1}{2}\mathbf{1}.$ This corresponds to the agent having a trivial internal memory.

The agent could perform some internal memory transformations. For example, it could introduce a  3-state memory $M'=\{0,1,2\},$ and randomly choose $0$ with probability $1/2,$ and $1$ or $2$ with probability $1/4$ each. Then we'd get a representative $(M', P')$ with $P':=\begin{pmatrix}
\frac{1}{4} & \frac{1}{8} & \frac{1}{8} \\
\frac{1}{4} & \frac{1}{8} & \frac{1}{8} 
\end{pmatrix}.$ By our definitions, these representatives are equivalent as states of knowledge:

$P'=P T^T $ with $T:=\begin{pmatrix} \frac{1}{2} \\ \frac{1}{4} \\ \frac{1}{4} \end{pmatrix},$

$P= P'T'^T $ with $T':=\begin{pmatrix} 1 & 1 & 1\end{pmatrix},$

and both $T$ and $T'$ have $1$-norm (maximal column sums) $\leq 1.$ The latter transformation matrix corresponds to forgetting $M'$ and getting back to a trivial memory state. So as we hoped, we have modded out equivalent transformations that the agent could perform on its internal memory.

The situation becomes more interesting after the agent made a few observations of coin tosses. Suppose it makes a single observation and stores the result as a memory state in $M:=\{h,t\}.$ Then the joint probability matrix becomes

$P:=\begin{pmatrix} \frac{0.6}{2} & \frac{0.4}{2} \\ \frac{0.4}{2} &\frac{0.6}{2} \end{pmatrix},$

and a representative of our state of knowledge is $(M,P).$

After it made $2$ observations (and stored both results), a representative is

$(M_2,P_2)$ with $M_2:=\{hh,ht,th,tt\}$ and $P_2:=\begin{pmatrix}
\frac{0.6*0.6}{2} & \frac{0.6*0.4}{2} & \frac{0.4*0.6}{2} &\frac{0.4*0.4}{2}\\
\frac{0.4*0.4}{2} & \frac{0.4*0.6}{2} & \frac{0.6*0.4}{2} &\frac{0.6*0.6}{2}
\end{pmatrix}$ - the internal memory can store all combinations of outcomes, and standard probability theory yields the joint probabilities.

We know that it is superfluous to store all outcomes: It suffices to keep track of _how many times_ the coin showed heads, as the order doesn't give additional information about which coin was chosen. We will now see that the formalism reflects this, as a representative for the latter form of storage is equivalent as a state of knowledge: When counting the coin flips, the joint probability matrix for observing a given numbers of heads is given by binomial distributions, i.e. we get a representative $(\{0,1,2\}, P_2')$ with

$P_2':=\begin{pmatrix}
0.18 & 0.24 & 0.08\\
0.08 & 0.24 & 0.18
\end{pmatrix}.$

We find transformation matrices with column sums $\leq 1$ such that $P_2'=P_2 T_2^T$ and $P_2=P_2' T_2'^T,$ proving that these representatives are equivalent:

$T_2:=\begin{pmatrix}
1 & 0 & 0 & 0\\
0 & 1 & 1 & 0\\
0 & 0 & 0 & 1
\end{pmatrix},$

$T_2':=\begin{pmatrix}
1 & 0 & 0\\
0 & 0.5 & 0 \\
0 & 0.5 & 0\\
0 & 0 & 1\end{pmatrix}.$

$T_2$ corresponds to counting the number of heads; $T_2'$ corresponds to randomly generating an order of flips given a count.

In general, given a SOK $S\in\mathcal{S},$ observing an additional coin flip and storing the result corresponds to multiplying $S$ by a SOK $Q$ represented by $(M_Q,P_Q):=\left(\{h,t\},\begin{pmatrix}0.6 & 0.4\\
0.4 & 0.6\end{pmatrix}\right)$ according to our formalism. In other words, if $S$ is represented by $(M,P),$ it gets mapped to the equivalence class of $(M\times M_Q, P')$ with $P'_{e,(m,x)}:= P_{e,m}(P_Q)_{e,x}.$ So after $n$ coin flips, the state of knowledge is

$S=Q^n S_0.$

Finally, suppose that in each step, the agent only observes a coin flip with probability $1/4,$ and doesn't change its state of knowledge otherwise. So it gets one of the outcomes $M_p:=\{h,t,n\},$ with a conditional probability matrix

$\begin{pmatrix}0.15 & 0.10 & 0.75\\0.10 & 0.15 & 0.75\end{pmatrix}=\frac{1}{4}\begin{pmatrix}0.6 & 0.4\\0.4 & 0.6\end{pmatrix}\oplus\frac{3}{4}\begin{pmatrix}1\\1 \end{pmatrix}=\frac{1}{4}Q+\frac{3}{4}\mathbf{1}.$

Here, the $\oplus$ signs stand for concatenating the columns, just like in our definition of addition. Then the state of knowledge after $n$ events (that are coin flips with probability $1/4$) is

$(\frac{1}{4}Q+\frac{3}{4}\mathbf{1})^n S_0.$

Similarly as in the situation above, one can show that it's sufficient for the agent to store a counter of heads and tails observed so far.

## 7. An agent interacting with its environment/convex optimization
### 7.1. Evolution
Now consider an agent interacting with its environment. So we introduce some output and input subsystems $O,$ $I,$ and a "law of nature" $T\colon\mathcal{S}^{E\times O}\to \mathcal{S}^{E\times I}$ that evolves the environment influenced by the agent's choices, and generating measurement data. The "law of nature" should be instantiated as some transition matrix $T',$ similar to the ones in our order definition in 4.4.:

1. For $\mathcal{S}_\mathrm{class},$ $T'\in(\mathbb{R}^+)^{(E\times I)\times(E\times O)}$ and $\Vert T'\Vert_1\leq 1,$
2. For $\mathcal{S}_\mathrm{quant},$ $T'\in\mathbb{C}^{(E\times I)\times(E\times O)}$ and $\Vert T'\Vert_2\leq 1,$
3. For $\mathcal{S}_\mathrm{decoh},$ $T'\in\mathbb{C}^{(D_T\times E\times I)\times(E\times O)}$ and $\Vert T'\Vert_2\leq 1,$ i.e. $T'$ may add some new space $D_T$ to the decohering space.

Then, starting with a SOK $S\in \mathcal{S}^{E},$ the states of knowledge attainable by the agent in one step are

$\left\{ \mathrm{tr}_I (T(S_O)) \mid\mathrm{tr}_O S_O \leq S, S_O\in \mathcal{S}^{E\times O} \right\}:$

To write something in $O,$ the agent is allowed to apply an arbitrary transformation to its internal state, which corresponds to creating an arbitrary SOK $S_O$ whose partial trace is at most $S.$ Then the transformation corresponds to applying $T,$ and incorporating the input into the internal memory is the same as application of the partial trace.

### 7.2. Measurements
Now suppose that given $S\in\mathcal{S}^E,$ the agent wants to calculate a function $f\colon E\to C.$ As before, it produces some $S_C\in \mathcal{S}^{E\times C}$ with $\mathrm{tr}_C S_C \leq S.$ Given this $S_C,$ the "probability" (in scare quotes because $S$ doesn't need to be normalized) of getting output $C$ for input $E$ is $\mathrm{tr}((E,C) S_C)$ (with $(E,C)$ interpreted as a member of $\mathcal{S}^{E\times C}$ as in 3.3): Multiplying by $(E,C)$ zeroes out all probabilities that don't correspond to that combination, and $\mathrm{tr}$ calculates the remaining probability.

We can easily turn this into some output condition, e.g. the condition that a SOK allows the agent to determine a property of the environment with a given maximal error probability. What's more, these are **convex** conditions because the SOKs are convex and partial traces are linear. So when we **truncate to a finite basis, we should be able to use convex optimization and duality**[^17] to find optimal algorithms that allow the agent to determine some value or perform some manipulation of the environment. This is a generalization of [Barnum-Saks-Szegedy](https://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.113.1101&rep=rep1&type=pdf).

### 7.3. The adversary bound and universal query algorithm
TODO: We can also generalize the adversary bound.

Consider a multi-step evolution with each step as in 7.1.: Starting from $S_0\in \mathcal{S}^E,$ the system goes through intermediate states $S_1,S_2,\ldots,S_{N-1}$ and arrives at a final state $S_N$ - influenced by the agent, whose choices in the different steps are encoded by SOKs $S_{O,k}\in \mathcal{S}^{E\times O}:$

$S_{k+1} = \mathrm{tr}_I (T(S_{O,k})),$  $\mathrm{tr}_O S_{O,k} \leq S_k,$ $S_{O,k}\in \mathcal{S}^{E\times O}.$

By linearity, we can add these expressions and obtain

$\sum_{k=0}^{N-1} S_k =\mathrm{tr}_I (T(\sum^{N-1}_{k=0}S_{O,k})),$ $\mathrm{tr}_O(\sum_{k=0}^{N-1}S_{O,k})\leq \sum_{k=0}^{N-1} S_{k}.$

We add $S_0$ on both sides of the equality, and $S_N$ on both sides of the inequality. Then we can combine these expressions:

$S_T+\mathrm{tr}_O(\sum_{k=0}^{N-1}S_{O,k})\leq \sum_{k=0}^{N} S_{k}=S_0+\mathrm{tr}_I (T(\sum^{N-1}_{k=0}S_{O,k})).$

Let $\sum_{k=0}^{N-1}S_{O,k}$


A bit of intuition: Up to renormalizing (multiplying by $1/T$), this sum corresponds to a "random step" of the agent.

## 8. Formal power series of knowledge
Together, addition and multiplication allows us to define formal power series of knowledge (we leave mathematical rigour for a moment, as we didn't define infinite sums or limits). For example, suppose our learner observes the stars with a telescope without making any choices. In an infinitesimal time $\Delta t\to 0,$ it observes a supernova with probability $r \Delta t,$ generating experimental data $A\in \mathcal{S}.$ Otherwise, it observes nothing. If $S(t)$ is the state of knowledge over time, we obtain

   $S(t+\Delta t)\to ((1-r \Delta t)\mathbf{1}+r\Delta t A) S(t).$

   This is solved by

   $S(t)=\exp((A-\mathbf{1})rt) S(0),$

   which is to be interpreted as the appropriate formal infinite power series. In fact, writing

   $K(t)=\exp(-rt)\exp(Art)S(0)=\sum^\infty_{k=0} \frac{\exp(-rt) (rt)^k}{k!} A^k S(0)$

   shows that the amount of knowledge obtained follows a Poisson distribution. Note that these equations contain minus signs, so our Grothendieck construction was probably helpful.
   
   TODO: Maybe we can also define such a formal power series when the agent is allowed to make choices - when considering _sets of attainable states of knowledge_ instead of _individual states of knowledge_.

## 9. (Further) references
TODO

## Acknowledgements
Thanks to [John Baez](https://math.ucr.edu/home//baez/README.html) for helpful comments.

## Footnotes
[^1]: Our convention is that each row is a different environmental state, and each column a different internal memory state.

[^2]: In fact, as I understand, they are infinite-dimensional.

[^3]: In an earlier version of this note, I defined this using an additional register into which the states decohered instead, thinking that the formalism wouldn't work anymore otherwise. But I now think that using density matrices works.

[^7]: In the correspondence $\mathcal{S}
^E_\mathrm{quant}$<->reduced density matrices on $E$, it should work out to be a literal partial trace.

[^9]: I think one can obtain a more elegant description of possible transformations by adapting the parallel developments by [Gutoski and Watrous](https://arxiv.org/pdf/quant-ph/0611234.pdf) and [Chiribella, D'Ariano, and Perinotti](https://journals.aps.org/pra/abstract/10.1103/PhysRevA.80.022339). But I don't see a use of that as of this note.

[^10]: If one looks into the definitions, one can see that the "states of quasiknowledge" construction is related to a [Grothendieck group construction](https://en.wikipedia.org/wiki/Grothendieck_group). I looked into Wikipedia to read that this has his name from a specific case "which resulted in the development of [K-theory](https://en.wikipedia.org/w/index.php?title=K-theory&oldid=1072713370)". What's written there looks formally quite similar to what I do here, though I don't understand it in detail. So maybe one can call these thoughts "K-theory on the space of probabilistic transformations?"

[^13]: A side remark: $\mathcal{S}_{\mathrm{quant}}\subset\mathcal{S}^\pm_{\mathrm{quant}}$ should be isomorphic to the algebra of complex positive semidefinite $E\times E$ matrices (as a subset of the Hermitian matrices), with elementwise addition and multiplication. Very roughly, there is a 1-to-1 correspondence between bipartite, unnormalized wavefunctions modulo unitary transformations on one part, and positive semidefinite matrices (which are the same as density matrices without normalization): Every density matrix can be purified, local unitaries on the purifying space don't change the reduced density matrix, and [all purifications of a given reduced density matrix are related by a local unitary](https://quantumcomputing.stackexchange.com/questions/9368/why-do-purifications-only-differ-by-a-local-unitary).

[^14]: It is **not** a homomorphism of $\mathbb{R}$-algebras.


[^17]: Apparently, [this Oberwolfach seminar](https://homepages.cwi.nl/~monique/ow-seminar-sdp/) contains a lecture on infinite-dimensional semidefinite optimization.
