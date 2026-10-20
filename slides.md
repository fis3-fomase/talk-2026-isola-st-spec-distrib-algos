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
algorithms designed with a continuous space in mind often **converges** as density and execution
speed grow, so a run on a discrete network is an **approximation of an ideal continuous
behaviour**. Characterising that approximation is the goal of this work.

</v-click>

<v-click>

**What we contribute**

- A **unified framework** where discrete computations modelled on *event structures* and
  continuous computations over space-time live side by side
- ***Space-time consistency***: the notion of eventual consistency<sup>1</sup>, reframed on event structures
- A first characterisation of the **gradient-cast (G)** and **collect-cast &#40;C)** blocks

</v-click>

</div>

<div class="absolute bottom-0 left-0 m-6 text-xs opacity-60">

<sup>1</sup> Beal, Viroli, Pianini, Damiani. *Self-adaptation to device distribution in the IoT.* ACM TAAS 12(3), 2017.

</div>

<!--
⏱ ~60s

The systems we care about are dense deployments of devices embedded in a physical
environment: IoT, cyber-physical systems, sensor networks. No individual device matters;
what matters is what the ensemble does. 

In this context, it is often the case that programs are implemented at a device-centric level programming but with a collective result in mind. Ideally, the output of the program should converge to a continuous output on the whole space-time when
density and execution speed grow and a run on a discrete network is an approximation of an
ideal continuous behaviour. Characterising that approximation is what we try to do in this work.

Three contributions: a framework where discrete and continuous field computations coexist;
a property we call space-time consistency, which reframes eventual consistency on event
structures; and a first characterisation of the G and C building blocks and of their chain.
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

- Each device **repeatedly executes the same program**, exchanging information with its neighbours.
- The collective execution is described by a **computational field**: a collection of values distributed across devices and evolving over time.

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

We work with aggregate computing, a programming paradigm in which the goal is to express the macroscopic, global behaviour of an entire system of agents.

The key abstraction is the computational field: a space-time data structure
mapping every space-time position — and therefore the device occupying it — to a value.

A field can have, for example, boolean values representing which are the sources and which are the sinks of the network, or numeric values, representing the temperature registered by sensors, or vector values , representing the direction along which devices should move.  A program is a functional manipulation of fields.

The abstraction in independent of the topology of the network. That is what makes it meaningful to ask what happens when the
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

<div class="pt-3 text-sm">

- Input: two Boolean fields, **source** and **target**, plus a **width**
- Output: a Boolean field, true along the shortest paths connecting the two areas
- `distanceTo` is a **gradient**, a standard building block in aggregate computing
- A self-stabilising, self-healing, fully distributed algorithm

</div>

<div class="pt-3 text-sm">

Read over a continuous space, the program defines an **ellipse**:
$$\{\,x : d(x,S) + d(x,T) \le d(S,T) + w \,\}$$

</div>

::right::

<div class="flex justify-center pt-4">
  <Fig src="imgs/channel-flow.png" class="h-100" />
</div>

<!--
⏱ ~50s

Our motivating example is the channel, a reference algorithm in spatial and amorphous computing.

Here it is written in ScaFi, a DSL in Scala.

You give it two regions, a source and a target, described as Boolean fields, plus a width.
It produces a Boolean field that is true on the devices lying along the shortest paths
connecting the two regions.

And it is self-healing: if the source moves, or devices fail, the channel reshapes by itself.

On the right you can see how it works: estimate the distance to the source, the distance to
the target, the distance between the two regions, and then apply the triangle inequality.
-->

---

# The channel, round by round.

<div class="text-lg pt-1">
  Successive computation rounds: local distance estimates gradually propagate.
</div>

<div class="flex justify-center pt-3">
  <Fig src="imgs/channel-stabilisation.png" class="w-full max-h-[30rem] object-contain" />
</div>


<!--
⏱ ~45–55 s

Importantly, the channel is not produced in a single computation round.
Each device repeatedly runs the same program and exchanges information with its neighbours.
At first, distance estimates are incomplete. As information propagates, the output evolves.
Under stable conditions, after sufficiently many rounds per device, the channel stabilises.

Here we kept the network density fixed and changed only the stage of execution.
In the next slide, we do the opposite: we compare different densities, looking
at the stabilised output of each network.
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

At each density, we let the channel stabilise. As density grows, the stabilised output
increasingly resembles an **ellipse**.

</div>

<!--
⏱ ~50s  

Now, the interesting part. Here is the same algorithm, same parameters, simulated on
networks of increasing density: one thousand, five thousand, ten thousand, twenty thousand
devices. Source is top-left, target is bottom-right, and the channel is in orange.

 At twenty thousand the output is a
clean geometric shape — and that shape is exactly the set of points whose summed distance
to the two foci is below a threshold. It is an ellipse.

So the discrete network is *approximating* a
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

A field assigns a value to **every event**: one per device, per round.

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

A field assigns a value to **every event**: one per device, per round. What you see in a simulation screenshot is a snapshot of it: one event per device,
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


<!-- --- -->

<!-- # Example: the channel as a field computation

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
</div> -->

<!--
⏱ ~35s  
Going back to our motivating example: the channel is a three-argument field computation. Two Boolean
fields for the two areas, a numeric field for the width, and a Boolean output field.

The pictures are snapshots of that output field after stabilisation, at
four different densities.
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

A field computation represents one specific execution. But a program must make sense on any
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
⏱ ~50s

So much for the discrete side. Now the continuous one, which is the actual contribution.

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

<div class="pt-2 text-base">

> **Definition (spacetime).** Let $M$ be an $n$-dimensional Riemannian manifold representing
> space, and $\mathbb{R}$ represent time. Spacetime is the manifold $S \equiv M \times
> \mathbb{R}$, with metric $g$.

</div>

<div class="grid grid-cols-12 gap-6 pt-3">
<div class="col-span-7 flex items-center">
  <Fig src="imgs/manifold.svg" class="w-full max-h-80" />
</div>
<div class="col-span-5 text-base flex flex-col justify-center gap-4">

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

---
layout: section
---

# Part 4
## Examples

<!--
⏱ ~8s
Last part: what happens to two fundamental building blocks in the limit.
-->
---

# Examples

<div class="pt-3 space-y-5 text-[0.95rem]">

<v-click>

<div>

**The easy cases**

1. **Round counting** is *not* consistent — as the grain shrinks, rounds get more frequent
   and the field diverges in time
2. **Point-wise operators** *are* consistent — the output depends neither on topology nor on
   execution frequency

</div>

</v-click>

<v-click>

<div>

**Why the general case is hard**

For stateful, neighbourhood-dependent computations the limit may depend on **how** the event
structures approach it, the speed of information propagation may **diverge**, **ties** (geodesic non-uniqueness) can leave a certain propagations undetermined — and proving consistency during the **transients** is often infeasible.

<!-- `min`-based -->

</div>

</v-click>

<v-click>

<div>

**In the rest of the talk: gradient-cast (G) and collect-cast &#40;C)**

**C** summarises a region into its leader, **G** broadcasts the decision back out. Chained, **C–G** is the core of *self-organising coordination regions*, a pattern that tunes how much computation is decentralised. Our preliminary characterisations focus only on their stabilised behaviour.
 

</div>

</v-click>

</div>

---

# Gradient-cast &#40;G) <span class="text-base font-normal opacity-70">— spread a given **value** outwards from the **sources**, transforming it through an **accumulation function**</span>

<style>
h1 { margin-bottom: 0.2rem !important; }
</style>

<div class="grid grid-cols-12 gap-x-6 gap-y-2 pt-1 text-sm items-center">

<div class="col-span-5">

**1. The gradient of Part 2**

```scala
G(source, 0.0, _ + nbr_dist)
```

Under suitable assumptions, **after stabilisation we have space-time consistency**.

</div>
<div class="col-span-7">
  <Fig src="imgs/g-limit.svg" class="max-h-55 mx-auto" />
</div>

<div class="col-span-5">

**2. Counting hops**

```scala
G(source, 0, x => x + 1)
```

The increment **does not vanish** with the grain: no limit.

</div>
<div class="col-span-7">
  <Fig src="imgs/hop-count.svg" class="max-h-40 mx-auto" />
</div>

</div>

---

# Collect-cast &#40;C) <span class="text-base font-normal opacity-70">— collect **values** along a **gradient**, inwards towards a **sink**</span>

<style>
.slidev-layout { padding-top: 0.5rem !important; }
h1 { margin-bottom: 0.2rem !important; }
</style>

<div class="grid grid-cols-3 gap-6 pt-1 text-sm">
<div>

**1. Summing areas** &nbsp;`_ + _` on `area`

Converges, but to a **Dirac delta**: a distribution, not a field

</div>
<div>

**2. MIN / MAX** &nbsp;`_ OR _` on Booleans

Converges to a **genuine field**: dominant values towards the sink

</div>
<div>

**3. Counting devices** &nbsp;`_ + _` on `1`

**Does not converge**: the total grows without bound with density

</div>
</div>

<div class="pt-2">
  <Fig src="imgs/c-examples.svg" class="w-full" />
</div>


---
layout: center
---

# What comes next

<div class="pt-4 text-lg space-y-4 max-w-4xl">

- **Self-stabilisation** — combine the framework with the known results, and cover the
  transient
- **Richer behaviours** — self-healing channels, self-organising spatial sampling
- **Exact limits** — describe the limit field analytically, starting from G
- **Comparison** — relate the framework to mean-field approximation

</div>


<!--
⏱ ~40s
To summarise. Situating an event structure in a manifold is what lets us compare a discrete
run with a continuous field at all. Space-time consistency then says the operator has an
ideal meaning as a continuous field, approximated by the network as the grain shrinks. And it
is not automatic — even G and C needed carefully chosen assumptions.

Ahead: self-stabilisation, which is what would let us cover the transient; richer behaviours,
the channel among them; analytical descriptions of the limit; and a comparison with
mean-field approximation.
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
Thank you — happy to take questions.
-->



