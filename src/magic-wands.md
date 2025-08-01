# Generating Annotations for Magic Wands

Clients like Prusti want to generate magic wands from function bodies.

The PCG facilitates this via the `UnblockGraph` interface. The interface takes a
PCG $\pcg$ and a PCG node $n$, and returns an ordered list $L$ of annotations
that describe how to unblock $n$ in $\pcg$. Prusti can consume these annotations
to generate magic wands.

<div class="info">

The interface could trivially be extended to unblock multiple nodes simultaneously

</div>

The annotations generated from the `UnblockGraph` are Borrow PCG edges.

At a high level, the resulting annotations are a topological sort of the edges
in the subgraph of $\pcg$ that contains $n$ and all of its ancestors.
Concretely, the procedure to generate the list $L$ of annotations is as follows:

1. Let $\mathcal{U}$ be the subgraph containing $n$ and its ancestors in $\mathcal{G}$
2. While $\mathcal{U}$ has at least one edge:
    1. Let $\overline{e}$ be the set of leaf edges in $\mathcal{U}$.
    2. If $\overline{e}$ is empty __fail__
    3. Append $\overline{e}$ to $L$
    4. Remove $\overline{e}$ from $\mathcal{U}$

We note that the above procedure could fail, for example, $\mathcal{U}$ could
contain a cycle. We expect that in practice this is quite rare. Furthermore, we
believe the following property should hold:

<div class="postulate">

For any list $\overline{e}$ of PCG edges forming a cycle, there does not exist
any execution path that call satisfy the validity conditions of every edge in
the cycle.

</div>

Therefore, it should be possible to modify the implementation to e.g. produce
multiple lists for distinct paths.
