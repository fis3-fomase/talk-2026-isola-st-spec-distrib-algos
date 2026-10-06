---
theme: default
layout: cover
title: "A Specification Approach for Distributed Algorithms in Continuous Space-Time"
info: |
  ## A Specification Approach for Distributed Algorithms in Continuous Space-Time
  R. Casadei, M. Viroli, N. Castronuovo, G. Aguzzi

  ISoLA 2026 — ReoCAS track, Kos, Greece.
  Part of the FoMaSE project (FIS3 Starting Grant).
class: text-center
drawings:
  persist: false
transition: slide-left
duration: 23min
---

<style>
#slidev-goto-dialog { display: none !important; }
.slidev-layout h1 { line-height: 1.15; }
</style>

# A Specification Approach for Distributed Algorithms in Continuous Space-Time

<div class="pt-4 text-lg">

Roberto Casadei · Mirko Viroli · **Niccolò Castronuovo** · Gianluca Aguzzi

</div>

<div class="pt-2 text-sm opacity-70">

Alma Mater Studiorum — Università di Bologna &nbsp;

</div>

<div class="pt-8 text-sm opacity-60">

ISoLA 2026 — ReoCAS track &nbsp;·&nbsp; Kos, Greece

</div>

<!--
⏱ ~10s

Good morning. This is joint work
with Roberto Casadei, Mirko Viroli and Gianluca Aguzzi.
-->


---

# From discrete networks to continuous specifications

<div class="pt-2 text-lg max-w-5xl">



<v-click>

**Main idea**

In dense deployments — IoT, cyber-physical systems, sensor networks — the collective result of
algorithms designed with a continuous space in mind **converges** as density and execution
speed grow, so a run on a discrete network is an **approximation of an ideal continuous
behaviour**. Characterising that approximation is the goal of this work.

</v-click>

<v-click>

**What we contribute**

- A **unified framework** where discrete computations modelled on *event structures* and
  continuous computations over space-time live side by side
- ***Space-time consistency***: the notion of eventual consistency [TAAS 2017], reframed on
  event structures
- A first characterisation of the **gradient-cast (G)** and **collect-cast &#40;C)** blocks

</v-click>

</div>

<!--
⏱ ~60s

The systems we care about are dense deployments of devices embedded in a physical
environment: IoT, cyber-physical systems, sensor networks. No individual device matters;
what matters is what the ensemble does. Device-centric programming does not express that,
which is why we work in the macro-programming tradition — you specify the behaviour of the
whole, and the local behaviour of each device is derived from it.

Here is the observation the whole paper rests on. Once you program at that level, you are
reasoning about distances and regions, not about the sixteen neighbours of node 4711 — you
are implicitly assuming a continuous space. And the collective result does converge as
density and execution speed grow. So a run on a discrete network is an approximation of an
ideal continuous behaviour, and characterising that approximation is what we are after.
Note the word in the title: specifying — what an algorithm ideally means, not how to
implement it.

Three contributions: a framework where discrete and continuous field computations coexist;
a property we call space-time consistency, which reframes eventual consistency on event
structures; and a first characterisation of the G and C building blocks and of their chain.

Let me now make this concrete with one algorithm.
-->

---
layout: section
---

# Part 1
## **Motivation** — a distributed algorithm that "converges to a shape" 

<!--
⏱ ~10s
I will start from a concrete algorithm that motivates the whole
work.

-->

---

# Aggregate computing and computational fields

<div class="grid grid-cols-2 gap-10 pt-2">
<div>

Approach: we work with **aggregate computing**.

- The unit of composition is the **computational field**
- A field maps each space-time position (and thus the device there) to a value (constants, Booleans, temperature, vectors,...)
- Programs are **functional manipulations of fields**

Crucially, the abstraction is **independent of the network topology**, so it scales to
arbitrarily dense deployments.

</div>
<div>

<div class="flex justify-center items-center h-full">
  <Fig src="imgs/field-intuition.svg" class="max-h-90" />
</div>

</div>
</div>

<!--
⏱ ~50s  

We work with aggregate computing, which is rooted in field-based
coordination. The key abstraction is the computational field: a space-time data structure
mapping every space-time position — and therefore the device occupying it — to a value.

A field can be a constant, a sensed physical phenomenon, or an actuation signal. And a
program is a functional manipulation of fields.

The important point for today is the last one: the abstraction says nothing about the
topology of the network. That is what makes it meaningful to ask what happens when the
network gets denser and denser.
-->

---
layout: two-cols
---

# Example: the channel

```scala
// in ScaFi, "everything" is a field
def channel(source: Boolean,
            target: Boolean,
            width: Double): Boolean =
  distanceTo(source) + distanceTo(target) <=
    distanceBetween(source, target) + width
```

<div class="pt-4">

- Input: two Boolean fields, **source** and **target**, plus a **width**
- Output: a Boolean field, true along the shortest paths connecting the two areas
<!--
- `distanceTo` yields a **gradient**: the distance from a Boolean field
- `distanceBetween` yields the distance between the two areas
- the rest is **point-wise arithmetic and comparison**
-->
</div>

<div class="pt-4 text-base opacity-70">

A self-stabilising, self-healing, fully distributed algorithm.

</div>

::right::

<div class="flex justify-center pt-4">
  <Fig src="imgs/channel-flow.png" class="h-100" />
</div>

<!--
⏱ ~55s

Let me make this concrete with the algorithm that motivated the paper: the self-healing
channel, a reference algorithm in spatial and amorphous computing.

Here it is written in ScaFi, a DSL in Scala.

You give it two regions, a source and a target, described as Boolean fields, plus a width.
It produces a Boolean field that is true on the devices lying along the shortest paths
connecting the two regions.

And it is self-healing: if the source moves, or devices fail, the channel reshapes by itself.

On the right you can see how it works: estimate the distance to the source, the distance to
the target, the distance between the two regions, and then apply the triangle inequality.
-->


---

# What happens as the network gets denser?

<div class="grid grid-cols-4 gap-3 pt-2">
  <div class="text-center">
    <Fig src="imgs/channel-1000.png" class="w-full rounded" />
    <div class="text-sm pt-1 opacity-70">1,000 devices</div>
  </div>
  <div class="text-center">
    <Fig src="imgs/channel-5000.png" class="w-full rounded" />
    <div class="text-sm pt-1 opacity-70">5,000 devices</div>
  </div>
  <div class="text-center">
    <Fig src="imgs/channel-10000.png" class="w-full rounded" />
    <div class="text-sm pt-1 opacity-70">10,000 devices</div>
  </div>
  <div class="text-center">
    <Fig src="imgs/channel-20000.png" class="w-full rounded" />
    <div class="text-sm pt-1 opacity-70">20,000 devices</div>
  </div>
</div>

<div class="pt-6 text-center text-lg">

The output converges to an **ellipse** with foci in source and target:
$$\{\,x : d(x,S) + d(x,T) \le d(S,T) + w \,\}$$

</div>

<!--
⏱ ~70s  

Now, the interesting part. Here is the same algorithm, same parameters, simulated on
networks of increasing density: one thousand, five thousand, ten thousand, twenty thousand
devices. Source is top-left, target is bottom-right, and the channel is in orange.

At a thousand devices the result is a thin, ragged, noisy path. At twenty thousand it is a
clean geometric shape — and that shape is exactly the set of points whose summed distance
to the two foci is below a threshold. It is an ellipse.

So the discrete network is not really *computing* a ragged path. It is *approximating* a
continuous object, and the approximation gets better as density grows.
-->

---
layout: section
---

# Part 2
## Field computations over event structures

<!--
⏱ ~10s

Let me now recall the discrete model because it is the substrate for everything else.
-->

---

# Event structures

<div class="grid grid-cols-12 gap-6 pt-1">
<div class="col-span-4">

A discrete, **asynchronous** model: at each event, a device evaluates a program on the messages from past neighbour events,
producing **(i)** a message for its neighbours and **(ii)** an **output value** for that event.

</div>
<div class="col-span-8 flex items-center">
  <Fig src="imgs/event-structure.svg" class="w-full max-h-64" />
</div>
</div>

<div class="pt-4 text-sm">

An **event structure** is a triple $\langle E, \rightsquigarrow, d \rangle$: events $E$, a
messaging relation $\rightsquigarrow$ representing **causality**, and a map $d$ from events to devices on which they happen.

</div>

<!--
⏱ ~60s

The model is discrete and asynchronous. Devices compute at discrete steps, called rounds, and
interact with their neighbours by message passing.

A whole system execution is modelled by an event structure: a set of events, a messaging
relation from a sender event to a receiver event, and a map assigning each event to the device
where it occurred.

In the picture, each circle is one round of one device. Horizontal arrows are messages a device
sends to its own future — that is how state persists over time. Diagonal arrows are messages
between devices.
-->

---

# Computational fields

<div class="pt-2">

> **Definition (computational field).** Given an event structure $\mathcal{E} = \langle E,
> \rightsquigarrow, d \rangle$, a computational field on $\mathcal{E}$ is a function
> $f : E \to V$ mapping every event to a value in a value set $V$.

</div>

<div class="grid grid-cols-12 gap-6 pt-4">
<div class="col-span-4 text-lg">

Fields are **spatiotemporally distributed values**.

A "snapshot" — one event per device — is what you actually *see* in the
simulation pictures.

</div>
<div class="col-span-8 flex items-center">
  <Fig src="imgs/field-over-es.svg" class="w-full max-h-75" />
</div>
</div>

<!--
⏱ ~35s

A computational field is simply a function from the events of an event structure to values.
Nothing more.

It is worth keeping in mind that a field is a *space-time* object, spanning the whole
execution. What you see in a simulation screenshot is a snapshot of it: one event per device,
taken after the computation has settled.
-->

---

# Field computations

<div class="pt-1">

> **Definition ($n$-argument field computation).** Let $\mathcal{F}_{E,V}$ be the set of fields
> on domain $E$ with values in $V$. An $n$-argument field computation over $\mathcal{E}$ is a
> function
> $$ \Phi_{\mathcal{E},n} : \mathcal{F}_{E,V}^{\,n} \longrightarrow \mathcal{F}_{E,V} $$

</div>

<div class="grid grid-cols-12 gap-4 pt-2">
<div class="col-span-8 flex items-center">
  <Fig src="imgs/field-computation.svg" class="w-full max-h-60" />
</div>
<div class="col-span-4 text-lg flex flex-col justify-center">

Fields in, field out — over a given event structure.

A field computation is therefore a natural denotation for a **global computation**.

</div>
</div>

<!--
⏱ ~35s

A field computation is then just a function from n input fields to an output field, all over
the same event structure.

This is the denotation of a global computation: it says what the collective does, over a
given execution, as a whole. No device appears in this definition.
-->

---

# Example: the channel as a field computation

<div class="pt-1 text-sm">

$$ \Phi_{\text{Channel}} : \mathcal{F}_{E,\mathbb{B}} \times
\mathcal{F}_{E,\mathbb{B}} \times \mathcal{F}_{E,\mathbb{R}_{\ge 0}}
\longrightarrow \mathcal{F}_{E,\mathbb{B}} $$

</div>

<div class="pt-2 grid grid-cols-12 gap-8">
<div class="col-span-8 text-sm">

**Inputs** — two Boolean fields for the source and target areas, plus a numeric field for the width (typically constant)

<div class="pt-2">
  <Fig src="imgs/channel-inputs.png" class="w-full" />
</div>

</div>
<div class="col-span-4 text-sm">

**Output** — a Boolean field: true on the devices belonging to the channel

<div class="pt-2 flex justify-center">
  <Fig src="imgs/channel-5000.png" class="max-h-44 rounded" />
</div>

</div>
</div>

<!--
⏱ ~35s  
Going back to our example: the channel is a three-argument field computation. Two Boolean
fields for the two areas, a numeric field for the width, and a Boolean output field.

The pictures are snapshots of that output field after stabilisation, at
four different densities.
-->
---

# Example: the gradient

<div class="pt-1">

The distance **to the nearest source**, computed by the network itself.

</div>

<div class="pt-3 text-[1rem] flex justify-center">

$$
\Phi_G :\;
\underbrace{\mathcal{F}_{E,\mathbb{B}}}_{\substack{\text{who is}\\ \text{a source}}} \times
\underbrace{\mathcal{F}_{E,\,E \to \mathbb{R}\cup\{\infty\}}}_{\substack{\text{distance to}\\ \text{each neighbour}}}
\;\longrightarrow\;
\underbrace{\mathcal{F}_{E,\mathbb{R}}}_{\substack{\text{distance to the}\\ \text{nearest source}}}
$$

</div>

<div class="grid grid-cols-12 gap-6 pt-4">
<div class="col-span-5 text-base">

**The local rule**

- a **source** holds $0$
- anyone else takes the **smallest** value among *neighbour's estimate + distance to it*

This is the `distanceTo` of the channel.

</div>
<div class="col-span-7 flex items-center">
  <Fig src="imgs/gradient.svg" class="w-full max-h-56" />
</div>
</div>


<!--
⏱ ~55s

The gradient maps a
field of Boolean values denoting sources and a metric field associating neighbours
to an estimation of the corresponding spatial distance to
the output field stabilising to the minimum distances to sources
-->

---

# From computations to programs: field operators

<div class="pt-1 text-base">

> **Definition ($n$-argument field operator).** A field operator, or field *program*, is a
> function $P_n:\mathcal{E} \mapsto \Phi_{\mathcal{E},n}$ from event structures to field computations.

</div>

<div class="grid grid-cols-12 gap-6 pt-3">
<div class="col-span-4 text-base flex flex-col justify-center">

A field computation lives on **one** event structure.

A field **operator** is the meaning of a *program*: it says what computation would occur on
**any** event structure.

This is the level at which we ask our question — consistency is a property of *operators*,
not of individual runs.

</div>
<div class="col-span-8 flex items-center">
  <Fig src="imgs/field-operator.svg" class="w-full" />
</div>
</div>

<!--
⏱ ~45s

One more level of abstraction, and it matters for the rest of the talk.

A field computation is tied to one specific execution. But a *program* must make sense on any
execution. So we define a field operator as a function from event structures to field
computations: given an execution, it returns the computation that would occur on it.

This is the right level for our question. Space-time consistency will be a property of an
operator — of a program — not of a single run.
-->

---
layout: section
---

# Part 3
## Fields over continuous space-time

<!--
⏱ ~10s

So much for the discrete side. Now the continuous one, which is the actual contribution.
-->

<!--
⏱ ~45s

Why bother with a continuous model at all?

Because the discrete details of an event structure are accidental. Which device happened to
be at that position, which message arrived first — none of it should matter for the meaning
of the algorithm. We want to abstract over those peculiarities.

The move we make is to situate the event structure inside a manifold, so that the computation
can be related to genuine geometrical notions: distances, angles, areas. And since we need to
measure distances, we work with Riemannian manifolds.
-->

---

# Space-time

<div class="pt-2 text-sm">

> **Definition (spacetime).** Let $M$ be an $n$-dimensional Riemannian manifold representing
> space, and $\mathbb{R}$ represent time. Spacetime is the manifold $S \equiv M \times
> \mathbb{R}$, with metric $g$.

</div>

<div class="grid grid-cols-12 gap-6 pt-3">
<div class="col-span-7 flex items-center">
  <Fig src="imgs/manifold.svg" class="w-full max-h-80" />
</div>
<div class="col-span-5 text-sm flex flex-col justify-center gap-4">

<div>

**Why a manifold**

Locally homeomorphic to Euclidean space but globally it can be curved,
bounded, holed, as real deployment environments are.

</div>

<div>

**Why Riemannian**

We need to *measure*: the metric gives the geodesic distance, which is
exactly what the algorithms estimate.

</div>

</div>
</div>

<!--
⏱ ~40s

So, spacetime for us is the product of a Riemannian manifold representing space and the reals
representing time.

Why a manifold rather than just Euclidean space? Because real environments are curved,
bounded, have obstacles and holes, and the manifold structure is local, which fits.

Why Riemannian? Because we need to measure distances, and the geodesic distance on the
manifold is precisely what the gradient algorithm is trying to estimate.

Note that time here is a single global parameter. A more relativistic treatment would be
possible and is left for future work.
-->

---

# Situated event structures

<div class="pt-1 pb-2 text-lg">

An event structure is **situated in $S$ with grain $\delta$** when spacetime can be
partitioned into connected regions $R_i$ of size below $\delta$, in bijection with the
events, respecting neighbourhood.

</div>

<div class="flex justify-center">
  <Fig src="imgs/situated-es.svg" class="h-64" />
</div>

<div class="pt-2 text-center">

Each event **owns** a portion of space-time. The grain guarantees coverage that is both
**full** and **uniform**.

</div>

<!--
⏱ ~65s

Here is the central construction. We say an event structure is situated in spacetime with
grain epsilon when there is a partition of spacetime into connected regions, each of size
smaller than epsilon, in bijection with the events, and such that neighbour events live in
neighbouring regions.

The intuition is that each event owns a portion of space-time, which we take as the
spatiotemporal extent of that event — the piece of the world that the event is responsible
for. And the grain does two jobs at once: coverage is full, because it is a partition, and it
is uniform, because no region is larger than epsilon.

Taking epsilon to zero is then exactly what "density and round frequency go to infinity"
means — and note it constrains *both* space and time, since a region is a space-time region.

We also require the situation to be physically coherent: `dt` must report the time that
actually elapsed, and `nbrRange` must measure distances according to the metric.
-->

---

# Two translations

<div class="grid grid-cols-2 gap-8 pt-1 text-sm">
<div class="rounded border border-slate-300 bg-slate-50 px-4 py-2">

**Fields on an event structure**

$f : E \to V$ — one value per **event**: a discrete, space-time distributed value

</div>
<div class="rounded border border-amber-300 bg-amber-50 px-4 py-2">

**Fields on spacetime**

$\phi : S \to V$ — one value per **point** of the manifold $S \equiv M \times \mathbb{R}$

</div>
</div>

<div class="grid grid-cols-2 gap-8 pt-4 text-sm">
<div>

**Discrete → continuous**

> The **continuous interpretation** of $f$ is the field $\phi : S \to V$ with
> $\phi(a) = f(\epsilon),$ where $\epsilon \in R_i,$ for all $a \in R_i$.

Expand each event's value over the region it owns. A piecewise-constant field on the manifold.

</div>
<div>

**Continuous → discrete**

> $\mathcal{E}$ **samples** $\phi$ into $f$ if, for each event $\varepsilon$, there is a point
> $y$ in its region with $f(\varepsilon) = \phi(y)$.

Read each continuous input at one point of the region the event owns.

</div>
</div>

<div class="pt-6 text-center text-lg">

Now discrete and continuous fields can be **compared**.

</div>

<!--
⏱ ~50s

Once an event structure is situated, we get two translations, and they are duals of each other.

Going from discrete to continuous: take the value at an event and spread it over the whole
region that the event owns. You obtain a piecewise-constant field defined on the entire
manifold — the continuous interpretation.

Going the other way: to sample a continuous field, each event reads it at some point inside
its own region.

With these two in place, we can finally compare a run on a network with a continuous field —
they now live in the same space.
-->

---

# Space-time consistency


<div class="grid grid-cols-12 gap-6 pt-2">
<div class="col-span-5 text-[0.8rem] leading-snug">

> **Definition (space-time consistent field operator).** An $n$-argument field operator
> $P_n$ is **space-time consistent** if for any $n$ continuous input fields $\phi_i : S \to V$
> **there exists** a continuous output field $\phi^o$ such that, **for every** monotonically
> decreasing sequence of grains $\delta_j\to 0$ and **for every** event structure
> $\mathcal{E}_j$ situated on $S$ with grain $\delta_j$, it holds that
> $$ \lim_{j \to \infty} \int_{S} d_V(\phi^o,\, \phi^o_j) \;=\; 0 $$
> where, if $f_i$ is a sample of $\phi_i$ by $\mathcal{E}_j$, &nbsp;
> $f^o_j = P_n(\mathcal{E}_j)(f_1,\dots,f_n)$, &nbsp; $\phi^o_j$ is the continuous
> interpretation of $f^o_j$, and $d_V$ is a metric over $V$.

</div>
<div class="col-span-7 flex items-center">
  <Fig src="imgs/st-consistency.svg" class="w-full" />
</div>
</div>
<!--
⏱ ~70s

And here is the definition. Let me read the diagram rather than the formula.

You start from continuous input fields — the top-left box. You sample them with an event
structure of grain epsilon-j; you run the operator on that network; you take the continuous
interpretation of the discrete output. That gives you a continuous field, phi-o-j, for each
grain.

The operator is space-time consistent if there exists a *single* continuous output field —
the top dashed arrow, the ideal behaviour — that all these approximations converge to, in
the integral sense, for *every* sequence of grains going to zero and *every* choice of
situated event structures.

Two consequences. First, a consistent operator is fully characterised by its input-output
behaviour on continuous fields: you can specify it geometrically and forget the network.
Second, an actual run on a discrete network is an approximation whose distance from the ideal
tends to zero as density and round frequency grow.

That is exactly the story the channel pictures told.
-->

---

# Two quick examples

<div class="grid grid-cols-2 gap-8 pt-4">
<div>

**Round counting is *not* consistent**

A 1-argument field operator such that 
at every round, each device increments by one an integer value representing the number of rounds performed by itself. As the grain shrinks, rounds get more frequent and the
field **diverges in time**. No continuous limit exists.

</div>
<div>

**Point-wise operators *are* consistent**

A 2-argument field operator that applies the mathematical operator $\star$ to ist two inputs.

The output at an event depends neither on the topology nor on the execution frequency, so the
limit is just $\star$ applied to the continuous inputs, point by point.

</div>
</div>

<!--
⏱ ~55s

Two examples to give a feel for the property.

On the left, a counter: each device increments its own round count. This is not space-time
consistent, and the reason is instructive — as the grain shrinks, rounds become more and more
frequent, so at any fixed instant of physical time the count grows without bound. The field
diverges. There is simply no continuous field it converges to.

On the right, any point-wise operator — addition, multiplication, comparison. Here the output
at an event depends neither on the topology nor on the execution frequency, so the continuous
interpretation trivially converges to the point-wise application on the continuous inputs.

The lesson: anything that counts rounds, or measures time in rounds rather than in seconds,
is immediately suspect.
-->

---
layout: center
---

# Why the general case is hard

<div class="text-lg pt-4 space-y-5 max-w-4xl">

Point-wise operators are easy. **Stateful, neighbourhood-dependent** computations — the
gradient, the channel — are not.

- as density and frequency grow, the **speed of information propagation** may diverge or
  behave chaotically
- the limit may depend on *how* the sequence of event structures approaches the limit
- proving consistency at **every** space-time coordinate, including during transients, is
  often infeasible

</div>

<div class="pt-8 text-lg">

This motivates shifting attention to **asymptotic** behaviour, once perturbations cease:
**self-stabilisation**.

</div>

<!--
⏱ ~55s

A word of honesty about the scope of the property.

For point-wise operators consistency is trivial. For stateful, neighbourhood-dependent
computations — which is to say, for all the interesting ones — it is hard.

The core difficulty is information propagation. As density and round frequency grow together,
the speed at which information travels can diverge, or behave chaotically, and the limit can
depend on the particular way in which the sequence of event structures approaches the limit.
Remember the definition quantifies universally over all such sequences.

So proving strict consistency at every space-time coordinate, including during transients, is
generally out of reach. This strongly motivates looking at the asymptotic behaviour instead —
self-stabilisation — which we plan to integrate into this framework.
-->

---
layout: section
---

# Part 4
## A first result: the C–G chain

<!--
⏱ ~10s

Let me close with a first, preliminary result about the two building blocks I introduced.
-->

---

# Why G and C, and why chained

<div class="pt-2 grid grid-cols-2 gap-8">
<div>


**G** — gradient-cast: spread a value outwards from sources, along the gradient.

**C** — collect-cast: aggregate values inwards, towards a sink.

</div>
<div>

Chained, **C–G** is the core of *self-organising coordination regions*: a pattern that tunes
how much computation is decentralised.

1. **C** summarises a region into its leader
2. **G** broadcasts the decision back out

</div>
</div>

<div class="pt-8 text-center text-lg">

If we can characterise the limit of **C** and **G**, we can characterise a whole family of
self-organising behaviours.

</div>

<!--
⏱ ~45s  — OPTIONAL: compress to one sentence if running late

Why these two blocks in particular?

Because chained together they form the core of a pattern called self-organising coordination
regions, which is the standard way of tuning the degree of decentralisation in a collective
system: collect-cast summarises a region towards a leader, the leader decides, and
gradient-cast broadcasts the decision back out to the region.

So if we can characterise the limit behaviour of C and of G, we get a handle on a whole
family of self-organising behaviours at once.
-->

---

# Gradient-cast &#40;G)

<div class="pt-1">

Generalises the gradient: instead of the distance, spread a **value** outwards from the
sources, transforming it at each step.
$$
\Phi_G :\;
\underbrace{\mathcal{F}_{E,\mathbb{B}}}_{\substack{\text{who is}\\ \text{a source}}} \times
\underbrace{\mathcal{F}_{E,V}}_{\substack{\text{value the source}\\ \text{emits}}} \times
\underbrace{(\oplus: V \to V)}_{\substack{\text{accumulation}\\ \text{function}}} \times
\underbrace{\mathcal{F}_{E,\,E \to \mathbb{R}\cup\{\infty\}}}_{\substack{\text{distance to}\\ \text{each neighbour}}}
\;\longrightarrow\;
\underbrace{\mathcal{F}_{E,V}}_{\substack{\text{accumulated}\\ \text{value}}}
$$

</div>

<div class="grid grid-cols-12 gap-6 pt-4">
<div class="col-span-5 text-sm">

**The gradient of Part 1**
```scala
G(source, 0.0, _ + nbrRange, nbrRange) 
```

**Counting hops** from the sources is then just:

```scala
G(source, 0, x => x + 1, metric)
```


</div>
<div class="col-span-7 flex items-center">
  <Fig src="imgs/hop-count.svg" class="w-full max-h-56" />
</div>
</div>

---

# G is space-time consistent

<div class="pt-1 text-[1rem] leading-snug">

**Assumptions.** The neighbourhood graph is connected and locally consistent with the geometry
(`metric` agrees with the geodesic distance $d_M$); every point of $M$ is joined to the source by a **unique geodesic**; and the increment of `acc` is bounded by $K\operatorname{Vol}(R_i)$.

**Limit.** By uniqueness of geodesics the propagated field is determined, and
$\mathcal{F}(x) = A_{\gamma_x}(\texttt{initial})$, with $\gamma_x$ the unique geodesic from $x$
to the source and $A$ the **continuum lift** of `acc` along geodesic paths.

</div>

<div class="flex justify-center pt-2">
  <Fig src="imgs/g-limit.svg" class="w-full max-h-60" />
</div>

<!--
⏱ ~75s

Take gradient-cast first. The argument sketch goes like this.

We assume the neighbourhood graph is connected and locally consistent with the geometry of the
manifold — that is, the metric the devices use agrees with the geodesic distance. And we
assume that every point is joined to the source set by a unique geodesic, which rules out
degenerate configurations with ties.

During the computation, each device keeps a pair: its estimated distance from the source, and
the propagated value. It takes the minimum over its neighbours of distance-plus-metric, and
applies the accumulation function to the value of that best neighbour.

Now let the diameter of the regions go to zero. The local metric converges to the geodesic
distance on the manifold. By uniqueness of the geodesic through each point, the propagated
value field is uniquely determined in the limit, and converges pointwise and locally
uniformly.

And the limit has a clean geometrical description: the value at a point x is obtained by
propagating the source value along the unique geodesic connecting x to the sources, where the
continuum lift A is defined as the limit of repeated discrete applications of `acc` along
refining chains approximating the geodesic.

So G is space-time consistent.
-->

---

# Collect-cast &#40;C)

<div class="pt-1">

Dual to **G**: information is **collected along a gradient, towards a sink**.

</div>

<div class="pt-3 text-[1rem] flex justify-center">

$$
\Phi_C :\;
\underbrace{\mathcal{F}_{E,\mathbb{R}}}_{\substack{\text{a potential}\\ \text{field}}} \times
\underbrace{\mathcal{F}_{E,V}}_{\substack{\text{values to be}\\ \text{collected}}} \times
\underbrace{(\oplus: V\times V \to V)}_{\substack{\text{accumulation}\\ \text{function}}}
\;\longrightarrow\;
\underbrace{\mathcal{F}_{E,V}}_{\substack{\text{accumulated}\\ \text{value}}}
$$

</div>

<div class="grid grid-cols-2 gap-8 pt-5 text-lg">
<div>


- a potential field induces a **spanning forest**
- each event picks as parent a neighbour with strictly smaller potential
- local minima of the potential act as **sinks**

</div>
<div>

**What it stabilises to:**

Each sink holds the $\oplus$-combination of the local values in its **basin of attraction**;
every other event holds the partial aggregate of the subtree rooted at it.

</div>
</div>


---

# C, case 1: arithmetic accumulation

<div class="pt-1 text-[1rem] leading-snug">

**Assumptions.** Let `acc` be commutative and associative on $\mathbb{R}$, with the contribution of an event in region $R_j$ bounded by $D \cdot \mu(R_j)$ and assume the potential is generated by the flow lines of a **smooth potential.** 

**Limit.** Everywhere except the source the value tends to **zero**: each device has a single parent and the regions shrink. Thus the limit is **a Dirac delta $K\cdot\delta_S$ at the sink,** where $K$ is the total accumulated value. 

</div>

<div class="flex justify-center pt-2">
  <Fig src="imgs/c-limit.svg" class="w-full max-h-65" />
</div>


<!--
⏱ ~50s

Collect-cast is more delicate.

We assume the potential comes from the flow lines of a smooth scalar potential, so that the
descent flow gives, for each point, a well-defined trajectory towards a minimum. That flow
induces a directed forest on the devices: each device has exactly one parent, and the sinks
have none.

At each event, a device sends its parent the result of applying the accumulation function to
what it received from its children at the previous round and to its own local value.

Now, what happens in the limit turns out to depend dramatically on the accumulation function,
and we distinguish two cases.
-->


<!--
⏱ ~60s

Case one: the accumulation function is an ordinary commutative, associative arithmetic
operation — think of summing up an area, or counting a population. We also assume that what a
child contributes scales with the area of its region, which is the natural assumption when C
is used to integrate a quantity over a region.

Then, in the limit, something interesting happens. The value at every point other than the
sink converges to zero — because each device has exactly one parent, and the regions shrink to
nothing. All the mass concentrates at the sink.

So the limit object is a Dirac delta at the sink, with weight K, the total accumulated value,
and the integral distance does go to zero.

But note what this means: C converges to a *distribution*, not to a continuous function. In
this case the limit exists, but it falls outside the class of continuous fields, so this case
does not fit our definition of consistency as stated.
-->

---

# C, case 2: MIN / MAX accumulation

<div class="pt-2 text-[0.95rem]">

Now let the value set be **totally ordered** and `acc` be MIN or MAX — this includes the
Boolean case with OR. Take `acc` = MAX, without loss of generality.

In the limit, the output field associates to each point $p$ is $\mathcal{F}(p) = \max_{t \le t_0} w(\gamma_p(t))$ where $\gamma_p(t_0) = S_i,$ i.e. the largest local value found **along the flow line through $p$**, up to the sink $S_i$ it is connected to.

</div>

<div class="pt-6 text-center text-lg">

$\mathcal{F}$ describes a **propagation of dominant values towards the sinks**.

No mass is lost as regions shrink — the limit is a genuine field.

</div>

<!--
⏱ ~55s

Case two is the good one. Take the value set to be totally ordered and the accumulation to be
minimum or maximum. This includes the Boolean case with logical OR, which is what you use when
you want to know whether *any* device in a region observed something.

Here the limit is a genuine field over the manifold: the value at a point p is the largest
local value found along the flow line passing through p, up to the sink it is connected to.

So the limit field describes a propagation of dominant values towards the sinks. And the
crucial difference with case one is that MIN and MAX are idempotent: nothing is lost, and
nothing accumulates, as regions shrink. The limit stays a well-behaved field.
-->

---

# Simulation evidence: collect-cast with OR

<div class="pt-1">
  <Fig src="imgs/collect-evolution.png" class="h-40 mx-auto" />
  <div class="text-sm opacity-70 pt-1 text-center">Evolution over time, 19,600 nodes — <code>C(potential, _ || _, value, false)</code></div>
</div>

<div class="grid grid-cols-4 gap-3 pt-3">
  <div class="text-center"><Fig src="imgs/collect-1024.png"  class="h-36 mx-auto rounded" /><div class="text-xs pt-1 opacity-70">1,024</div></div>
  <div class="text-center"><Fig src="imgs/collect-4096.png"  class="h-36 mx-auto rounded" /><div class="text-xs pt-1 opacity-70">4,096</div></div>
  <div class="text-center"><Fig src="imgs/collect-10000.png" class="h-36 mx-auto rounded" /><div class="text-xs pt-1 opacity-70">10,000</div></div>
  <div class="text-center"><Fig src="imgs/collect-19600.png" class="h-36 mx-auto rounded" /><div class="text-xs pt-1 opacity-70">19,600</div></div>
</div>

<!--
⏱ ~60s

And here is the picture for case two. The sink is the red star in the bottom-left corner; the
yellow region is where the collected Boolean value is true.

The top row is the evolution over time on a network of nearly twenty thousand nodes. It starts
as the disc where the local value is true. Then, round after round, the true value is dragged
down along the flow lines towards the sink, and a tail forms. At convergence you get a cone:
the union of all the flow lines that pass through the disc.

The bottom row is the stabilised result at four densities. At a thousand nodes the cone is
ragged and incomplete; as density grows the boundary sharpens, and at twenty thousand nodes
you see a clean continuous cone directed towards the sink.

Exactly as in the channel, the discrete run is approximating a continuous geometrical object.
-->

<!--
⏱ ~50s

Putting the two halves together gives the result.

The chain of collect-cast with minimum, followed by gradient-cast with the identity — which is
just a broadcast — is space-time consistent.

The argument is short: G is space-time consistent, and crucially G only reads the value held
at the source. So the only thing we need from C is that it converges at the source, and case
two gives us precisely that.

And note the contrast: the same argument does *not* go through for C with arithmetic
accumulation, because there the limit at the source is a distribution rather than a field
value. Which is a reminder that composing consistent-looking blocks is not automatic.
-->

---
layout: center
---

# Take home

<div class="text-lg pt-4 space-y-5 max-w-4xl">

<v-clicks>

1. **Situating** an event structure in a manifold lets discrete runs and continuous fields be
   compared directly.

2. **Space-time consistency** says: the operator has an ideal meaning as a continuous field,
   and a network run approximates it as the grain goes to zero.

3. Consistency is **not** free — round counting fails; stateful, neighbourhood-dependent
   operators are genuinely hard.

4. **G** is consistent; **C** converges, but to a distribution in the arithmetic case and to a
   field in the MIN/MAX case; so, e.g., the **C(MIN)–G** chain is consistent.

</v-clicks>

</div>

<!--
⏱ ~50s

To summarise.

Situating an event structure in a manifold is what lets us compare a discrete run with a
continuous field at all — the two translations, sampling and continuous interpretation.

Space-time consistency then states that an operator has an ideal meaning as a continuous
field, and that running it on a network approximates that meaning as the grain shrinks.

The property is not free: counting rounds already breaks it, and for stateful operators it is
genuinely hard because of propagation speed.

And for the two blocks we studied: G is consistent; C converges, but the nature of the limit
depends entirely on the accumulation function; and their chain with MIN is consistent.
-->

---

# Future work

<div class="pt-4 text-lg space-y-5">

- **Self-stabilisation.** Combine this framework with the known results on self-stabilisation 

- **Richer behaviours.** Apply it to self-healing channels and to self-organising spatial sampling

- **Comparison.** Relate the framework to **mean-field approximation**

- **Exact limit.** In some easy cases, e.g. the gradient-cast (G), describe the limit field evolution in an analytical way

</div>



<!--
⏱ ~40s

Three directions for future work.

First, and most importantly, combining this with self-stabilisation, so that we can talk about
asymptotic behaviour rather than requiring consistency during transients.

Second, applying the framework to more complex self-organising behaviours — the channel we
started from, and self-organising spatial sampling.

Third, comparing it with mean-field approximation, which addresses a similar question from a
rather different angle.

Let me acknowledge the FoMaSE project, which funded this work.
-->

---
layout: center
class: text-center
---

# Thank you

<div class="pt-6 text-lg opacity-80">

Questions?

</div>

<div class="pt-10 text-sm opacity-70">

This work contributes to the project **FoMaSE** — Foundations for  Macro-programming-based Software Engineering.

</div>


<!--
⏱ leave ~7 minutes

Thank you for your attention — I'm happy to take questions.
-->

---
layout: section
---

# Backup slides


---

# Backup: what could break consistency

<div class="pt-4 text-lg space-y-5">

- **Propagation speed.** Density and frequency both grow; the speed at which information
  travels per unit of *physical* time may diverge or oscillate.

- **Dependence on the sequence.** The definition quantifies over *all* sequences
  $\{\epsilon_j\}$ and *all* situated event structures — a limit that exists only for
  well-behaved sequences is not enough.

- **Transients.** Consistency as defined is required at every space-time coordinate, including
  while the computation is still settling.

- **Ties.** Geodesic uniqueness matters: with ties, the limit of a `min`-based propagation need
  not be determined.

</div>


