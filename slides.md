---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
#background: /imgs/banner2026.png # https://cover.sli.dev
# some information about your slides (markdown enabled)
layout: cover
title: "Macro-to-micro behavioural mappings in distributed systems: a characterisation on event structures"
# info provides ..
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# apply UnoCSS classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable Comark Syntax: https://comark.dev/syntax/markdown
comark: true
# duration of the presentation
duration: 5min
---

<style>
#slidev-goto-dialog {
  display: none !important;
  visibility: hidden !important;
  pointer-events: none !important;
}
</style>

# Macro-to-micro behavioural mappings in distributed systems
## A characterisation on event structures

<!--
Good afternoon everybody, my name is Paolo Baldini and I'm here to present the work that I conducted together with Roberto Casadei and Nicolò Castronovo, titled "Macro-to-micro behavioural mappings in distributed systems: A characterisation on event structures".
-->

---
layout: image-right
image: https://fis3-fomase.github.io/talk-2026-sasso-macro-to-micro-mapping/imgs/collective.png
backgroundSize: 75% 80%
---

<div class="h-110 grid content-evenly"><div>

# Context

- Collective systems
- Programming paradigms

</div><div>

# Goal

- Common, unified framework
- Simpler share of ideas

</div></div>

<!--
I will begin this presentation by providing some context.
<u>This work falls within</u> the topics of <u>collective systems</u>, so systems composed of multiple interacting elements, <u>and programming paradigms</u>.
Specifically, it considers paradigms <u>permitting the definition of the system overall behavior</u> rather than that of its composing elements – we will refer to those as macroscopic programming paradigms.

The <u>issue that we address is the difference</u> between all those different approaches that, we believe, complicates the sharing of ideas and the interaction between different communities.
What <u>we propose</u> is, therefore, to adopt <u>a common, unified definition framework</u> capable of simplifying the sharing of ideas.
-->

---
layout: two-cols-header
---

# Examples

<div class="grid grid-cols-2 gap-8 my-6 pt-5">
<div class="min-w-0 w-full pr-3 overflow-auto space-y-0">

## Aggregate computing

```java
// Gradient field computation
def distanceTo(s) {
  rep(∞) {
    (dist) => mux(s, 0, minHood(nbr{dist} + nbrRange()))
  }
}
```


- Program into common instructions
- Abstract concept of field

</div>

<div class="min-w-0 w-full pr-3 overflow-auto space-y-0">

## Choreography

```java
// Conditional information sharing

C = if p.c == x
    then (p → r[L]; p.c → r; 0)
    else (p → r[R]; r.c → p; 0)

```


- Program into device-specific instructions
- Explicit concept of message / interaction

</div>

</div>

<div class="text-center pt-3">

Two formalisms to express the behavior of collective systems that are not mutually intelligible.

</div>

<!--
In order to better highlight the problem, I will briefly introduce <u>two examples</u>, one using aggregate computing and another using a choreographic approach for the definition of the behavior of collective systems.
The former shows the <u>computation of a gradient field</u>, the latter a conditional <u>information sharing between two processes</u>.

While both permit the definition of high-level programs, they present <u>some differences</u> that make them quite different.
Among the most notable, we find the <u>way in which the program is deployed</u> to the devices: in aggregate computing the program is simply copied to the devices and executed locally; in choreographic programming, the program is projected into device (or role) specific programs.
Furthermore, they employ two different <u>approach to computation</u>: aggregate computing leverage an abstract concept of field values shared across the collective system; choreographic programming employs an explicit message abstraction.
-->

---

# A unifying framework

<div class="h-100 flex flex-col justify-center">

To capture and describe:

- Expected scenarios --- <u>_macro-input_</u>
- Desired outcomes --- <u>_macro-output_</u> and <u>_effect_</u>
- Deployment process --- <u>_macro-program_</u> to device <u>_policy_</u>
- Adaptation process --- <u>_policy_</u> evolution over time

</div>

<!--
So, in other to unify these two programming approaches, we propose a unifying framework to capture and describe:
- the expected execution scenarios, that we refer to as macro-input
- the desired outcomes, that we call macro-output or macro-effect depending is they are the result of a computation or an effect on the environment
- the deployment process of the macro-program to the devices, and
- the adaptation process, that describes the evolution of the program over time – we will discuss about it, more in detail, later.
-->

---
layout: image-right
image: https://fis3-fomase.github.io/talk-2026-sasso-macro-to-micro-mapping/imgs/event_structure.png
backgroundSize: contain
---

# Event structure

<div class="h-100 w-100 flex flex-col justify-center">

- Captures the execution of a collective system
- Express execution in function of events (_sense_, _compute_, _(inter)act_)

</div>

<!--
To do so, we leverage the concept of <u>event structure</u>, previously <u>defined to capture the execution of a collective system</u>.
This employs the event abstraction, that comprises <u>perceptual update, computation, and inter-action</u> – <u>communication and action on the environment</u>.
The main point is that <u>events execution cause other events</u> on the system, on the same or different devices.
-->

---

# Expected scenarios --- _macro-input_

<div class="h-100 flex flex-col justify-center">

The _initial_ conditions that affect the system dynamics:

- environmental state
- system state
- network topology

</div>

<!--
The first use that we make of event structures is to define the expected execution scenarios – the macro-input of the system.
We imagine that the <u>characteristics of the operational environments</u> for which the system is designed <u>can be expressed in function of events</u>.
Specifically, <u>those comprise initial environmental states</u> captured by the perceptual step of the event, the <u>initial computation</u> of the device depending on its instantiation, and the <u>network topology</u> – i.e., <u>how the computation of other nodes depends on the initial events</u>.
-->

---

# Desired outcomes --- _macro-output_ and _effect_

<div class="h-100 flex flex-col justify-center">

- Macro-output: informative result of the collective computation

&ensp;&ensp; function of final events: $f(\epsilon_{\textit{T}_\textit{end}}^*)$

- Macro-effect: changes on the environment caused by the system execution

&ensp;&ensp; function of the final collective perception: $f(s_{\textit{T}_\textit{end}}^*)$

</div>

<!--
We also define the desired outcomes in function of events.
This include the <u>results of a computation, that are the events produced at the end</u> of the computation itself, <u>and effects on the environment, that are the changed that the collective system made to the environment during execution</u> – this includes also intermediate events.
Examples of these two could include <u>consensus</u> and <u>environmental cleanup</u> performed by robots.
-->

---

# Macro-programming goal

<div class="h-100 flex flex-col justify-center">

- Compute the _macro-output_ or cause the _macro-effect_ starting from _macro-inputs_

- Map space-time circumstances to actions (i.e., define specific behavioral policies)

</div>

<!--
So, given macro-inputs and a macro-output/-effect, the goal of macro-programming becomes <u>mapping space-time circumstances, including device types, to specific actions</u> (i.e., behavioral policies), so as to obtain the desired outcome.
For instance, given as desired effect the execution of a <u>foraging behavior</u> in a group of robots, our goal will be the <u>creation of behavioral policies</u> defining, for each space-time situation resulting from the initial conditions, the corresponding action to perform.
-->

---
layout: image-right
image: https://fis3-fomase.github.io/talk-2026-sasso-macro-to-micro-mapping/imgs/deployment.png
backgroundSize: 80% 60%
---

<div class="w-200">

# Localization process & compatibility condition

</div>

<div class="h-100 flex flex-col justify-center">

- Map devices to policies --- decide which device will execute a specific policy
- Deploy policies on devices capable of executing them: &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$\small\mathit{requirement}(\mathit{policy}) \subseteq \mathit{capability}(\mathit{device})$
</div>

<!--
We imagine this process to be performed by a localization procedure that maps devices to specific behavioral policies.
Furthermore, we imagine it capable of deploying policies to devices according to their execution capabilities and program requirements – we refer to this constraint as "compatibility condition".
-->

---

# Characterization of macro-programs

<div class="h-100 flex flex-col justify-center">

<!-- - _static_: $\forall \delta \in \Delta, \enspace \forall\,t, t+1 \in T, \enspace \mu^\delta_t = \mu^\delta_{t+1} \enspace$
- _dynamic_: $\exists\, \delta \in \Delta \; \land \;\,t, t+1 \in T \enspace | \enspace \mu^\delta_t \not= \mu^\delta_{t+1}$
- _homogeneous_: $\forall\,\delta, \delta' \in \Delta, \enspace \mu^\delta = \mu^{\delta'}$
- _heterogeneous_: $\exists\,\delta, \delta' \in \Delta \enspace | \enspace \mu^\delta \not= \mu^{\delta'}$ -->

<!-- - sta-hom: $\forall\,\delta, \delta' \in \Delta \land \forall\,t,t' \in T, \; \mu^\delta_t = \mu^{\delta'}_{t'}$
- sta-het: $\forall\,t,t' \in T, \; \exists\,\delta, \delta' \in \Delta | \mu^\delta_t = \mu^{\delta}_{t'} \land  \mu^\delta_t \not= \mu^{\delta'}_{t}$
- dyn-hom: $\mu $
- dyn-het: $\mu $ -->

According to time:
- <u>_static_</u>: the policy of each device remains the same over time
- <u>_dynamic_</u>: the policy of at least one device changes during execution

According to the collective:
- <u>_homogeneous_</u>: all devices share, at any moment, the same policy
- <u>_heterogeneous_</u>: at least two devices employ, at any moment in time, different policies

</div>

<!--
In this work <u>we also characterize different macro-programming approaches according to the type of policies produced by the localization process</u>.
If the policy of every device remains the same over the execution of the computation, we refer to static macro-programming.
If, instead, the policy of at least a device changes during execution we refer to dynamic macro-programming.
This distinction directly captures both fixed and adaptable mechanisms, including the programming of learning collectives.

Another distinction depends on the type of policies spread over the collective system.
Specifically, if all device share, at any moment, the same policy, they are called homogeneous, while, if at least two device employ different policies at any time, they are called heterogeneous.
-->

---

# Results

<div class="grid grid-cols-2 gap-8 my-6 pt-5">

<div class="min-w-0 w-full pr-3 overflow-auto space-y-0">

## Aggregate computing

```java
// Gradient field computation
def distanceTo(s) {
  rep(∞) {
    (dist) => mux(s, 0, minHood(nbr{dist} + nbrRange()))
  }
}
```


- _Homogeneous_ localization
- Common device capability and unique requirement sets
- Events capture field-based computation

</div>

<div class="min-w-0 w-full pr-3 overflow-auto space-y-0">

## Choreography

```java
// Conditional information sharing

C = if p.c == x
    then (p → r[L]; p.c → r; 0)
    else (p → r[R]; r.c → p; 0)

```


- _Heterogeneous_ localization
- Requirement and capability sets associated with device type / role
- Events capture message-based computation

</div>

</div>

<!--
So, <u>returning to the previous examples</u>, with the proposed definition framework, <u>aggregate computing and choreographic programming can the observed from the same perspective</u>.
The former program falls under the definition of homogeneous macro-programming approach, while the latter under the class of heterogenous macro-programming.
<u>Aggregate computing</u> expect that <u>all devices satisfy the given requirements such as</u>, in this case, the presence of some <u>primitive functions</u>, while <u>choreographic programming</u> distinguish requirements and capabilities <u>for devices and roles</u>.
Finally, the <u>event abstraction captures both the field-based and message-based computation</u>, simply representing the computation on shared data.
-->

---

# Results

<div class="h-100 w-full flex flex-col justify-center">

- Unified interpretation of different paradigms
- Possibility to propose approach-agnostic solutions
- Simplified sharing of ideas

</div>

<!--
The result is, therefore, that this framework enables a unified interpretation of different paradigms, enabling the possibility to propose approach-agnostic solutions leveraging event abstractions and thus simplifying the sharing of ideas and the interaction <u>between communities</u>.
-->

---

# Future works

<div class="h-100 w-full flex flex-col justify-center">

- Further elaborate the macro-to-micro mapping mechanism
- Further elaborate dynamic macro-programming and enabling techniques

</div>

<!--
As future work, we would like to further elaborate the macro-to-micro mapping mechanism, which we believe could be improved in its definition, and to more deeply investigate techniques enabling dynamic macro-programming, such as <u>learning based methodologies</u>.
-->

---

# Acknowledgments

<div class="h-100 w-full flex flex-col justify-center">

This work has been founded by the project ["FoMaSE: Foundations for Macro-programming-based Software Engineering"](https://fis3-fomase.github.io).

</div>

<!--
To conclude, I would like to acknowledge the importance of the FoMaSE project, focussing on the investigation of macro-programming based engineering of collective system, which founded the present work.
-->
