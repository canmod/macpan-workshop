Workshop on `macpan2` – Companion Materials
================

Author: [Steve Walker](https://github.com/stevencarlislewalker)

Last Updated: 2025-01-10

[![CC BY-NC-SA
4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

This is a (currently draft) set of companion materials for a workshop on
using [`macpan2`](https://canmod.github.io/macpan2) for applied public
health modelling.

(Note: I highlight areas that cannot be finalized yet, with notes like
this)

<!-- omit from toc -->

## Table of Contents

-   [Preliminaries](#preliminaries)
    -   [Syllabus](#syllabus)
    -   [Philosophy](#philosophy)
    -   [Organization](#organization)
    -   [Code Style](#code-style)
    -   [Dependencies](#dependencies)
-   [Exploration](#exploration)
    -   [Starter Model Library](#starter-model-library)
    -   [Simulating Dynamics](#simulating-dynamics)
    -   [Relating Model Specifications to Box
        Diagrams](#relating-model-specifications-to-box-diagrams)
    -   [Relating Model Specifications to Explicit
        Dynamics](#relating-model-specifications-to-explicit-dynamics)
    -   [Basics of Modifying Model
        Specifications](#basics-of-modifying-model-specifications)
    -   [Compare Simulated and Observed
        Incidence](#compare-simulated-and-observed-incidence)
    -   [Reporting Delays](#reporting-delays)
    -   [Hospital Admissions and
        Occupancy](#hospital-admissions-and-occupancy)
    -   [Symptom Status](#symptom-status)
    -   [Seroprevalence](#seroprevalence)
    -   [Vaccination](#vaccination)
    -   [Wastewater Pathogen
        Concentrations](#wastewater-pathogen-concentrations)
    -   [Vital Dynamics](#vital-dynamics)
    -   [Importation and Stochasticity](#importation-and-stochasticity)
-   [Parameterization](#parameterization)
    -   [Modify Default Values](#modify-default-values)
    -   [Express Uncertainty in Model
        Parameters](#express-uncertainty-in-model-parameters)
    -   [Optimized Calibration](#optimized-calibration)
    -   [MAP Estimation](#map-estimation)
    -   [Likelihood and Prior Distribution
        Assumptions](#likelihood-and-prior-distribution-assumptions)
    -   [Can I hack `macpan2` to use MCMC
        esimation?](#can-i-hack-macpan2-to-use-mcmc-esimation)
    -   [Calibrating Time-Varying
        Parameters](#calibrating-time-varying-parameters)
-   [Inference](#inference)
    -   [Assessing Model Fits](#assessing-model-fits)
    -   [Forecasts](#forecasts)
    -   [Counter-Factuals](#counter-factuals)
    -   [Reproduction Numbers of Generation Interval
        Distributions](#reproduction-numbers-of-generation-interval-distributions)
-   [Stratification](#stratification)

## Preliminaries

### Syllabus

<!-- comments denoted as <BMB> ... </BMB> -->

<BMB>It's not obvious to me why you have this as a `.md` file rather than a `.[qr]md` file where the outputs are generated dynamically? That makes sure everything is properly synced ...</BMB>

Please read the
[syllabus](https://canmod.github.io/macpan-workshop/syllabus) before
participating in this workshop. The instructor will review this syllabus
with participants before the sessions start.

The syllabus provides a roadmap for the workshop, guiding participants
through the exploration-parameterization-inference-stratification
strategy essential for compartmental modeling. Each session is organized around these steps, offering a
structured approach to model refinement. Participants will follow a
practical example that demonstrates each phase of the strategy, with
companion materials available for deeper exploration of related skills.
Each session contains exercises designed to reinforce key concepts and
encourage participants to apply lessons to their own compartmental model
diagrams. For those without a pre-existing model or whose models are not
applicable, a detailed example model is provided to ensure a hands-on
learning experience <BMB>"to ensure a hands-on learning experience" feels jargony. Could delete?</BMB>. By the end of the workshop, participants will learn to evaluate the suitability of compartmental modeling for
public health problems, develop models using `macpan2`, navigate its
documentation to address challenges, and provide constructive feedback
for improving the tool.

### Philosophy

The goal of `macpan2` is to implement well-understood
techniques, optimized for use by epidemiological modelers  who are tasked with using data-calibrated compartmental models to answer real-world public health questions.

### Organization

<BMB>I found some of the opinionated stuff here off-putting. Do you need to emphasize
this so much?  Do we need this explanatory paragraph at all? (</BMB>
Our effort to present standard tools in a convenient manner has led us
to develop concepts that encapsulate how I believe modellers do (or
should) think. By building and updating our software with these concepts
in mind, I seek to converge on a tool that will help modellers focus
more on epidemiology and less on computational details. We highlight
these concepts in special boxes.

| <img src="images/concept.svg" width="120" />                                                                                                                                        |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **User Feedback**<br>We would like feedback on our modelling framework. What concepts seem helpful/useful and what concepts seem harmful/useless? |

While `macpan2` tries to abstract away many of the ugly details of computational modeling,
knowing a few technical details will help you use it more effectively. We collect these
details in tip boxes.

| <img src="images/tip.svg" width="120" />                                                                                             |
|:-------------------------------------------------------------------------------------------------------------------------------------|
| Tip boxes like this contain technical information that could interrupt the flow of ideas, but is too important to bury in footnotes. |

If you’re attending this workshop, you’re already an experienced
modeller. Many of the concepts will be familiar to you, so you may gain
more by actively engaging with the tool itself. To support this process
of self-discovery, I have designed a series of exercises. <BMB>clunky: rephrase?</BMB>

| <img src="images/exercise.svg" width="120" />                                                                                                                                                                                                                                                                 |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| For your first exercise, please familiarize yourself with the package website: <https://canmod.github.io/macpan2>. Pay particular attention to the [quickstart guide](https://canmod.github.io/macpan2/quickstart) and other [user guides](https://canmod.github.io/macpan2/articles/index.html#user-guides). |

### Code Style

Throughout these materials I will repeatedly use several coding idioms.
Please familiarize yourself with these in advance of the workshop.

We will often use the [base R pipe
operator](https://www.tidyverse.org/blog/2023/04/base-vs-magrittr-pipe/#pipes),
`|>`, when it improves readability.

### Dependencies

The `macpan2` package is designed to be used with two other standard
packages <BMB>which two? you've listed three here. Why not just load the whole tidyverse, which will include these three and a couple of others that may be useful ...</code>  and so all of the code in these materials assumes that the
following packages are loaded.

``` r
library(macpan2) ## obviously
library(ggplot2)
library(dplyr)
library(lubridate)
```

This document was produced with the following R environment.

<BMB>I'm not sure why you are using the `|> print()` idiom. I know JD prefers this for explicitness in code that's going to be batch-run, but it seems unnecessary and confusing in this context ... (I might suggest including this info at the *end* of the document, as a postscript, since most of this info will only be useful for troubleshooting. You could if you liked mandate minimum versions of R/packages (e.g. tidyverse version 2.0.0 and R version >= 4.4.0 ... [or whatever, given constraints on PHAC setup])</BMB>
``` r
sessionInfo() |> print()
```

    ## R version 4.4.2 (2024-10-31)
    ## Platform: x86_64-apple-darwin20
    ## Running under: macOS Sequoia 15.0.1
    ## 
    ## Matrix products: default
    ## BLAS:   /Library/Frameworks/R.framework/Versions/4.4-x86_64/Resources/lib/libRblas.0.dylib 
    ## LAPACK: /Library/Frameworks/R.framework/Versions/4.4-x86_64/Resources/lib/libRlapack.dylib;  LAPACK version 3.12.0
    ## 
    ## locale:
    ## [1] en_CA.UTF-8/en_CA.UTF-8/en_CA.UTF-8/C/en_CA.UTF-8/en_CA.UTF-8
    ## 
    ## time zone: America/New_York
    ## tzcode source: internal
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ## [1] lubridate_1.9.3 dplyr_1.1.4     ggplot2_3.5.1   macpan2_1.13.0 
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] vctrs_0.6.5       cli_3.6.3         knitr_1.49        rlang_1.1.4      
    ##  [5] xfun_0.49         generics_0.1.3    jsonlite_1.8.9    glue_1.8.0       
    ##  [9] colorspace_2.1-1  TMB_1.9.15        htmltools_0.5.8.1 fansi_1.0.6      
    ## [13] scales_1.3.0      rmarkdown_2.29    grid_4.4.2        tibble_3.2.1     
    ## [17] evaluate_1.0.1    munsell_0.5.1     MASS_7.3-61       fastmap_1.2.0    
    ## [21] yaml_2.3.10       lifecycle_1.0.4   memoise_2.0.1     compiler_4.4.2   
    ## [25] timechange_0.3.0  pkgconfig_2.0.3   lattice_0.22-6    digest_0.6.37    
    ## [29] R6_2.5.1          tidyselect_1.2.1  utf8_1.2.4        oor_0.0.2        
    ## [33] pillar_1.9.0      magrittr_2.0.3    Matrix_1.7-1      withr_3.0.2      
    ## [37] tools_4.4.2       gtable_0.3.6      cachem_1.1.0

## Exploration

<BMB>Who is the audience here/who is this statement aimed at? If you want to be friendlier you could say "in this seection, you ..."</BMB>
Participants will learn how to explore and modify models, and informally
compare them with observed data.

<BMB>do you want to be consistent about `macpan2` vs macpan2?</BMB>
At the core of macpan2 are model specifications, which define
the flows between compartments and set default values for parameters.
<BMB>"primarily" seems overly cautious/distracting here</BMB>
These specifications offer a clear and systematic way to describe the
behavior of a system, starting with the foundational elements of
compartmental models. While additional complexities, such as vital
dynamics, external sources and sinks, or initialization of state
variables, are often necessary, I begin with simple specifications.

| <img src="images/concept.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Parsimony**<br>Starting with simplicity reflects an essential modelling principle: begin with a straightforward model and gradually add complexity as the situation demands, whether for specific data, additional details, or particular public health objectives. This approach will help you quickly get started with macpan2 and transition smoothly to more intricate applications. It also highlights the value of abstract compartmental models that can be stored in a library of reusable templates. These templates can be adapted to different scenarios, allowing for efficient modelling tailored to public health challenges.<br><br>TODO: link to model modification section. |

### Starter Model Library

Throughout the workshop, I provide you with starting points that take
care of much of the boilerplate, enabling you to concentrate on the
unique aspects of your applications. For instance, you will explore a
library of dynamical models, identify simple specifications, and see how
they define flows and default parameter values. Hands-on exercises will
guide you to experiment with these models, helping you uncover their
structure and applications in a variety of public health contexts.

To list available models in the macpan2 library:

``` r
show_models()
```

<BMB> Can this table be auto-generated?</BMB>
| Directory                                                                                                                    | Title                    | Description                                                                                                     |
|:-----------------------------------------------------------------------------------------------------------------------------|:-------------------------|:----------------------------------------------------------------------------------------------------------------|
| [awareness](https://github.com/canmod/macpan2/tree/main/inst/starter_models/awareness)                                       | Awareness Models         | Behaviour modifications in response to death                                                                    |
| [hiv](https://github.com/canmod/macpan2/tree/main/inst/starter_models/hiv)                                                   | HIV                      | A simple HIV model                                                                                              |
| [lotka_volterra_competition](https://github.com/canmod/macpan2/tree/main/inst/starter_models/lotka_volterra_competition)     | Lotka-Volterra           | Simple two-species competition model                                                                            |
| [lotka_volterra_predator_prey](https://github.com/canmod/macpan2/tree/main/inst/starter_models/lotka_volterra_predator_prey) | Lotka-Volterra           | Simple predator-prey model                                                                                      |
| [macpan_base](https://github.com/canmod/macpan2/tree/main/inst/starter_models/macpan_base)                                   | Macpan Base              | Re-implementation of the McMaster group’s COVID-19 model                                                        |
| [nfds](https://github.com/canmod/macpan2/tree/main/inst/starter_models/nfds)                                                 | NFDS and Vaccine Design  | An ecological model using population genomics to design optimal vaccines as implemented in Colijn et al. (2020) |
| [seir](https://github.com/canmod/macpan2/tree/main/inst/starter_models/seir)                                                 | Basic SEIR               | Simple epidemic model with an exposed class                                                                     |
| [shiver](https://github.com/canmod/macpan2/tree/main/inst/starter_models/shiver)                                             | SHIVER = SEIR + H + V    | A modified SEIR model with Hospitalization and Vaccination                                                      |
| [si](https://github.com/canmod/macpan2/tree/main/inst/starter_models/si)                                                     | Basic SI                 | A very simple epidemic model                                                                                    |
| [sir](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir)                                                   | Basic SIR                | A very simple epidemic model                                                                                    |
| [sir_demog](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir_demog)                                       | SIR with Demography      | An SIR model with birth and death                                                                               |
| [sir_mosquito](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir_mosquito)                                 | Mosquito-Vector SIR      | SIR model for mosquito vectors                                                                                  |
| [sir_waning](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir_waning)                                     | SIR with Waning Immunity | A basic SIR model with a flow from R back to S                                                                  |
| [ww](https://github.com/canmod/macpan2/tree/main/inst/starter_models/ww)                                                     | Wastewater Model         | Macpan base with an additional wastewater component                                                             |

<BMB>The "SIR with waning immunity" is more traditionally called "SIRS"</BMB>

<BMB>Maybe this is obvious?</BMB>
This function displays the available models, along with their
directories, titles, and descriptions.

To examine a specific model, such as the SIR model, load it into R:

``` r
sir = mp_tmb_library("starter_models", "sir", package = "macpan2")
print(sir)
```

    ## ---------------------
    ## Default values:
    ## ---------------------
    ##  quantity value
    ##      beta   0.2
    ##     gamma   0.1
    ##         N 100.0
    ##         I   1.0
    ##         R   0.0
    ## 
    ## ---------------------
    ## Before the simulation loop (t = 0):
    ## ---------------------
    ## 1: S ~ N - I - R
    ## 
    ## ---------------------
    ## At every iteration of the simulation loop (t = 1 to T):
    ## ---------------------
    ## 1: mp_per_capita_flow(from = "S", to = "I", rate = "beta * I / N", 
    ##      abs_rate = "infection")
    ## 2: mp_per_capita_flow(from = "I", to = "R", rate = "gamma", abs_rate = "recovery")

There are three parts to this specification.

-   **Default values:** The default values that model quantities take
    before each simulation begins[^1]. In the `sir` example there are
    two parameters (the transmission rate `beta` and removal rate `gamma`), the total population size, `N`,
    and the initial values of two state variables, `I` and `R`.
-   **Before the simulation loop:** Expressions that 
    `macpan2` evaluates before the dynamical simulation loop. In the `sir` example
    the initial value of `S` is computed. The specification
    could also have placed `S` in the defaults: in this case we compute it before the simulation loop
    instead in order to allow it to depend on other model quantities.
-   **At every iteration of the simulation loop:** Expressions that get
    evaluated iteratively during the simulation loop. In the `sir`
    example there are two flows, which I discuss more throughout the
    next few sections.

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Model specification objects, like `sir` above, can be produced <BMB>try for active voice: "you can use several functions to produce"?</BMB> using several different functions. Above I produced such an object using `mp_tmb_library()`, which reads pre-existing specifications in from the library. If you go to the [directory](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir) that defines this library model you will see [the R script](https://github.com/canmod/macpan2/blob/main/inst/starter_models/sir/tmb.R) that actually produces the specification, using the `mp_tmb_model_spec()` function; you can use this function yourself to produce your own model specifications. Later we will learn about other functions that take a model specification and return a modified version of this specification. This is useful when a model needs to be modified so that it can be compared with data (e.g., account for under-reporting, time-varying transmission rates in response to school closures).<BMB>clarify?</BMB>

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| The `sir` model illustrates the two kinds of expressions that can be used to define model behaviour: (1) Generic math expressions (e.g., `S ~ N - I - R`) and (2) flows among compartments (e.g., `mp_per_capita_flows(from ...)`). In generic math expressions, the tilde, `~`, is used to denote assignment. For example, the expression, `S ~ N - I - R`, says that the result of `N - I - R` is assigned to the quantity, `S`. Any function listed [here](https://canmod.github.io/macpan2/reference/engine_functions) can be used on the right-hand side of these expressions. Each flow expression corresponds to a single flow between compartments. The tilde-based math expressions and flow expressions can be mixed to provide a great deal of flexibility in model specifications.<BMB>delete last sentence?</BMB> Throughout I will discuss the relative advantages and disadvantages of using one or the other. |

### Simulating Dynamics

The main thing that you will do with model specifications like `sir` is
to simulate dynamics under these models. <BMB>comment on why this is useful? Explore counterfactuals, understand dynamics in preparation for calibration, .. ?</BMB> Because specifications come
with default values for parameters and initial states, they can be
simulated from without adding any further information. However, you will almost always need to modify a built-in model specification to apply it to your particular situation; we will discuss many
ways to modify these models.

Typically the first thing to do to understand and validate a new model is to simulate from it, so we will work on this immediately.

| <img src="images/exercise.svg" width="120" />                                                                                                    |
|:-------------------------------------------------------------------------------------------------------------------------------------------------|
| Follow this [quick-start guide](https://canmod.github.io/macpan2/articles/quickstart) to simulate from the `sir` model using its default values. |

| <img src="images/concept.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Simple, Consistent, and General Format for Simulation Output**<br>`macpan2` models always return simulation outputs in the same format, a *long format* data frame where each output variable (e.g. `S`, `I`, `R`) has a separate row for every time. This output format is simple, works well with existing R frameworks (e.g. `tidyverse`/`ggplot2`) and can easily be restructured to other formats (e.g., with `tidyr::pivot_wider()`). [This](https://canmod.github.io/macpan2/articles/quickstart#generating-simulations) article gives a brief but full description of the data format. |

### Relating Model Specifications to Box Diagrams

Model specification objects, such as the `sir` object produced in the
previous section, are essentially textual descriptions of compartmental
model flow diagrams. Below we compare the *during the simulation loop*
portion of the `sir` object with the corresponding flow diagram:

``` r
mp_print_during(sir)
```

    ## ---------------------
    ## At every iteration of the simulation loop (t = 1 to T):
    ## ---------------------
    ## 1: mp_per_capita_flow(from = "S", to = "I", rate = "beta * I / N", 
    ##      abs_rate = "infection")
    ## 2: mp_per_capita_flow(from = "I", to = "R", rate = "gamma", abs_rate = "recovery")

![](figures/sir-diagram-1.png)<!-- -->

| <img src="images/exercise.svg" width="120" />                                                                                                                                                                                                                |
|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identify how each aspect of the diagram (compartments, arrows, rates) are represented in the printout of the model specification.<br><br>Inspect the model specification again. What questions do you have about each part of the specification? |

### Relating Model Specifications to Explicit Dynamics

One possibly unclear aspect of the previous model printout
is the specific mathematical meaning of the `mp_per_capita_flow`
functions, and their arguments.

``` r
mp_print_during(sir)
```

    ## ---------------------
    ## At every iteration of the simulation loop (t = 1 to T):
    ## ---------------------
    ## 1: mp_per_capita_flow(from = "S", to = "I", rate = "beta * I / N", 
    ##      abs_rate = "infection")
    ## 2: mp_per_capita_flow(from = "I", to = "R", rate = "gamma", abs_rate = "recovery")

You can find the technical documentation for this function (and related functions)
[here](https://canmod.github.io/macpan2/reference/mp_per_capita_flow),
but the following set of definitions summarizes the terminology.

| <img src="images/definition.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Absolute flow rate:** Total number of individuals flowing from one compartment to another in a single time-step. We call absolute flows things like `infection` and `recovery`. These names are specified using the `abs_rate` argument in the [mp_per_capita_flow()](https://canmod.github.io/macpan2/reference/mp_per_capita_flow) function. <BMB>I may have commented before that I find this terminology confusing: an argument name `abs_rate` suggests to me that we're going to be *defining* an absolute rate, not just giving it a name ...</BMB> The numerical value of the absolute flow rate depends on the *per-capita flow rate*, but in a way that depends on the *state update method*.<br><br>**Per-capita flow rate**: The rate at which individuals flow from one compartment to another in a single time-step, per individual in the source compartment. For example, the per-capita infection rate is often given by `beta * I / N` and for recovery is often `gamma`. These expressions are specified using the `rate` argument of the `mp_per_capita_rate` function.<br><br>**State update method**: Mathematical framework for computing *absolute flow rates* from *per-capita flow rates*.<BMB>There are two qualititatively different things going on here: (1) instantaneous to period rates (Euler, RK, etc.) (2) per capita to absolute. You might want to clarify that this is about the first ...</BMB> For example, the difference equation (a.k.a. Euler ODE solver, [mp_euler()](https://canmod.github.io/macpan2/reference/state_updates)) state update method results in `infection = S * (beta * I / N)`, with the *per-capita flow rate* in parentheses. For the state update method using the Runge-Kutta 4 ODE solver ([mp_rk4()](https://canmod.github.io/macpan2/reference/state_updates)), the relationship between *absolute flow rates* and *per-capita flow rates* is more complex because it accounts for the fact that the state variables change continuously through a single time step. |

Now that these definitions are in place, let’s look at an example. One
can use the
[mp_expand()](https://canmod.github.io/macpan2/reference/mp_expand)
function to expand the `mp_per_capita_flow()` notation to an explicit
form that is passed to the simulation engine.

``` r
sir |> mp_print_during()
```

    ## ---------------------
    ## At every iteration of the simulation loop (t = 1 to T):
    ## ---------------------
    ## 1: mp_per_capita_flow(from = "S", to = "I", rate = "beta * I / N", 
    ##      abs_rate = "infection")
    ## 2: mp_per_capita_flow(from = "I", to = "R", rate = "gamma", abs_rate = "recovery")

``` r
sir |> mp_expand() |> mp_print_during()
```

    ## ---------------------
    ## At every iteration of the simulation loop (t = 1 to T):
    ## ---------------------
    ## 1: infection ~ S * (beta * I/N)
    ## 2: recovery ~ I * (gamma)
    ## 3: S ~ S - infection
    ## 4: I ~ I + infection - recovery
    ## 5: R ~ R + recovery

This output illustrates that the (default) simulation framework is a
discrete time system of difference equations. In the first two
expressions in the expanded version the absolute flow rates are
computed, which are used in the last three expressions to update the
state variables.

For another example, suppose you are more comfortable with treating
compartmental models as ODEs. Indeed many modellers read the SIR flow
diagram as representing this specific set of ODEs.

$$
\begin{align}
\frac{dS}{dt} & = -S \times \beta I / N \\
\frac{dI}{dt} & = -I \times \gamma + S \times \beta I / N \\
\frac{dR}{dt} & = I \times \gamma \\
\end{align}
$$

We can easily use the `sir` compartmental model object to simulate it as
an ODE using the `mp_rk4()` function, which uses the [Runge Kutta 4 ODE
solver](https://en.wikipedia.org/wiki/Runge%E2%80%93Kutta_methods).
<BMB>I'm not convinced that this is worth showing. It seems overwhelming. (I would guess that very few people actually care about/would gain a lot of insight from seeing the steps of the RK4 algorithm written out explicitly ... ??</BMB>
``` r
sir |> mp_rk4() |> mp_expand() |> mp_print_during()
```

    ## ---------------------
    ## At every iteration of the simulation loop (t = 1 to T):
    ## ---------------------
    ##  1: k1_infection ~ S * (beta * I/N)
    ##  2: k1_recovery ~ I * (gamma)
    ##  3: k1_S ~ -k1_infection
    ##  4: k1_I ~ +k1_infection - k1_recovery
    ##  5: k1_R ~ +k1_recovery
    ##  6: k2_infection ~ (S + (k1_S/2)) * (beta * (I + (k1_I/2))/N)
    ##  7: k2_recovery ~ (I + (k1_I/2)) * (gamma)
    ##  8: k2_S ~ -k2_infection
    ##  9: k2_I ~ +k2_infection - k2_recovery
    ## 10: k2_R ~ +k2_recovery
    ## 11: k3_infection ~ (S + (k2_S/2)) * (beta * (I + (k2_I/2))/N)
    ## 12: k3_recovery ~ (I + (k2_I/2)) * (gamma)
    ## 13: k3_S ~ -k3_infection
    ## 14: k3_I ~ +k3_infection - k3_recovery
    ## 15: k3_R ~ +k3_recovery
    ## 16: k4_infection ~ (S + k3_S) * (beta * (I + k3_I)/N)
    ## 17: k4_recovery ~ (I + k3_I) * (gamma)
    ## 18: k4_S ~ -k4_infection
    ## 19: k4_I ~ +k4_infection - k4_recovery
    ## 20: k4_R ~ +k4_recovery
    ## 21: infection ~ (k1_infection + 2 * k2_infection + 2 * k3_infection + k4_infection)/6
    ## 22: recovery ~ (k1_recovery + 2 * k2_recovery + 2 * k3_recovery + k4_recovery)/6
    ## 23: S ~ S - infection
    ## 24: I ~ I + infection - recovery
    ## 25: R ~ R + recovery

| <img src="images/concept.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Mix and Match Compartmental Models with State Update Methods**<br>Difference equations and ODEs are the two most common [state update methods](https://canmod.github.io/macpan2/reference/state_updates) for simulating a compartmental model. The design of `macpan2` allows different state update methods to be mixed and matched with different compartmental model specifications. This flexible modularity is a key part of the power of `macpan2` – the model specification language is agnostic about the state update method. Therefore each compartmental model can be simulated in any number of ways. The full list of currently available state update methods is [here](https://canmod.github.io/macpan2/reference/state_updates). |

Here is one more example of an SIR model expansion that uses the
[Euler-multinomial
distribution](https://kingaa.github.io/manuals/pomp/html/eulermultinom.html)
to simulate a compartmental model with process error. <BMB>Euler-multinomial is going to be unfamiliar to most people; maybe mention that is a generalization of (and in this case exactly equal to) binomial sampling?</BMB>

``` r
sir |> mp_euler_multinomial() |> mp_expand() |> mp_print_during()
```

    ## ---------------------
    ## At every iteration of the simulation loop (t = 1 to T):
    ## ---------------------
    ## 1: infection ~ reulermultinom(S, (beta * I/N))
    ## 2: recovery ~ reulermultinom(I, gamma)
    ## 3: S ~ S - infection
    ## 4: I ~ I + infection - recovery
    ## 5: R ~ R + recovery

Note that all [state update
methods](https://canmod.github.io/macpan2/reference/state_updates)
generate the same last three expressions for this `sir` model object.

``` r
S ~ S - infection
I ~ I + infection - recovery
R ~ R + recovery
```

This means that all state update methods can be thought of as discrete
time difference equations, but with different approaches to calculating
the absolute flow rates per time step.

| <img src="images/concept.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Embrace Difference Equations**<br>The original [McMasterPandemic](https://github.com/mac-theobio/McMasterPandemic) project was based entirely on discrete time models. Simulations from discrete time difference equations require little math to implement and understand. This simplicity is nice in applied work where the barriers to real-world impact do not usually include mathematical novelty and elegance. Further, when modelling a real system it is natural to compare simulations with observed data, which are measured at discrete times anyways.<br><br>Difference equations are not without their drawbacks however. Many results in mathematical epidemiology come from ODEs and various stochastic processes, and so when using difference equations epidemiologists might be concerned that their intuition from ODEs will not hold. Difference equations can also be less stable (e.g., state variables going below zero) than ODEs, which can become a real problem when calibrating models using trajectory matching. Therefore we make it easy to check these concerns by using a [state update method](https://canmod.github.io/macpan2/reference/state_updates) like `mp_rk4()` (for a simple ODE solver) or `mp_euler_multinomial()` (for a simple process error model) to overcome these concerns whenever necessary.<br><br>When using these alternative state update methods keep in mind that they too can be thought of as difference equation model. Carefully inspect the last three lines of any of the expanded `sir` models, which constitute the same set of difference equations no matter what state update method is used. This similarity indicates that all state update methods boil down to discrete-time difference equations, but with different approaches to calculating the absolute flow rates (e.g., `infection` and `recovery`). These absolute rate variables describe the changes in the state variables (e.g., `S`, `I`, `R`) due to different processes over a single time-step, allowing for continuous change within a time-step. This means, for example, that the time-series of the `infection` variable will contain the incidence over each time-step. So, for example, if each time-step represents one week, the `infection` variable will contain weekly incidence values.<br><br>In summary, the choice of state update method will often not matter in applied work, but when it does the [state update methods](https://canmod.github.io/macpan2/reference/state_updates) make it simple to explore options. <BMB>I'm not convinced of the value of this digression ... there's no logical connection between the scale on which the underlying dynamics are defined and the timing of the observations (which might be integrated values such as incidence over a time period, or instantaneous measures, or some kind of average (or delayed/convolved thingy) ... </BMB>

| <img src="images/exercise.svg" width="120" />                                                                                                                                                                                                                                                                   |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Write out by hand the last four lines of an expanded SEIR model.<br><br>[Read](https://canmod.github.io/macpan2/articles/example_models#using-examples) the [SEIR model](https://github.com/canmod/macpan2/tree/main/inst/starter_models/seir) from the model library and test if you were correct. <BMB>This should specify an *Euler-mode* SEIR model ... </BMB>|

TODO: Simulate dynamics with different approaches.

### Modifying Model Specifications

The models in the model library are intended to provide a place to
start, so let’s get started doing this. <BMB>What does this sentence mean?</BMB>

| <img src="images/exercise.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Follow the advice [here](https://canmod.github.io/macpan2/articles/example_models.html#modifying-examples) to create two copies of the [SIR](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir) model: one called SI and one called SEIR. Place both of these models in a directory on your computer called `macpan-workshop-library`. Or just do one model as time permits.<br><br>Simplify the model called SI so that it is an SI model (check your work [here](https://github.com/canmod/macpan2/tree/main/inst/starter_models/si)).<br><br>Add flows to the model called SEIR so that it becomes an SEIR model (check your work [here](https://github.com/canmod/macpan2/tree/main/inst/starter_models/seir)).<br><br>Simulate a time-series of incidence from one of these models and plot it. |

The previous exercise described one approach to modifying existing
models, which involves copying a directory and editing the files in that
directory. Another approach uses the [`mp_tmb_update()`,
`mp_tmb_insert()`,
`mp_tmb_delete()`](https://canmod.github.io/macpan2/reference/mp_tmb_insert)
functions, which take a loaded model specification object (like the
`sir` object above) and returns a modified version of that object.

| <img src="images/exercise.svg" width="120" />                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Read this help page and be ready to discuss the differences between these functions: <https://canmod.github.io/macpan2/reference/mp_tmb_insert>.<br><br>Use these functions to repeat the previous exercise by modifying the `sir` object within an R session instead of editing a model definition file. |

### Compare Simulated and Observed Incidence

<BMB>Don't assume that the previous isn't interesting!</BMB>
Now we are getting to the interesting stuff in this first `Exploration`
section of the `Exploration-Parameterization-Inference-Stratification`
methodology: using data (and other empirical information) and
simulations together to understand a system. This step is critical in
epidemiological modelling for applied public health work. No matter how
fancy you get with your techniques, your model must always be relevant
to a specific place, time, and public health concern.<BMB>do we need the last sentence? Feels lecture-y</BMB>

| <img src="images/concept.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Data Prep and Model Modifications Are Two Sides of the Same Coin**<br>To confront a model with data one needs to do two general classes of things: (1) access and prepare data and (2) modify the model so that it is relevant to those data. Typically there is an interplay between how you prepare the data and how you set up (or modify) the model. For example, you might choose to deal with under-reporting by dividing the incidence time-series by a known underreporting rate. In this case you wouldn't need to modify the model, because scaling the data would account for underreporting. On the other hand, you might choose to transform the modelled incidence (number of new cases per time period) into a modelled *observed* incidence by multiplying the incidence by the reporting probability, and then comparing these simulations with the raw observed data. In this section I will explore a specific path to handling these issues. <BMB>is making the likelihood a binomial draw from the true incidence a third option?</BMB>

Let’s get started by loading in some familiar Ontario COVID-19 data.

``` r
release = "https://github.com/canmod/macpan2/releases/download/macpan1.5_data"
covid_on = (release
  |> file.path("covid_on.RDS")
  |> url() 
  |> readRDS()
)
covid_on |> head() |> print()
```

    ## # A tibble: 6 × 4
    ##   province date       var             value
    ##   <chr>    <date>     <chr>           <dbl>
    ## 1 ON       2020-02-04 Hospitalization    NA
    ## 2 ON       2020-02-04 ICU                NA
    ## 3 ON       2020-02-04 Ventilator         NA
    ## 4 ON       2020-02-04 report             NA
    ## 5 ON       2020-02-04 newTests           NA
    ## 6 ON       2020-02-04 death              NA

``` r
covid_on |> summary()
```

    ##    province              date                var                value          
    ##  Length:11481       Min.   :2020-01-16   Length:11481       Min.   :-26834981  
    ##  Class :character   1st Qu.:2021-01-27   Class :character   1st Qu.:        1  
    ##  Mode  :character   Median :2022-03-10   Mode  :character   Median :       95  
    ##                     Mean   :2022-03-14                      Mean   :      638  
    ##                     3rd Qu.:2023-05-26                      3rd Qu.:      694  
    ##                     Max.   :2024-04-16                      Max.   : 26834981  
    ##                                                             NA's   :1793

Hmmm … lots of suspicious things to investigate … but let’s stay focused
on case reports, which I will compare with simulated incidence.<BMB>Maybe a little more detail? I do wonder if you want to make your own clean version of these data for the workshop? I feel like if you're going to admit that messy data exist, you might need to spend more time discussing checking/cleaning ...</BMB>

``` r
reports = (covid_on
    |> filter(var == "report")
    |> filter(abs(value) < 10^4) ## do not believe numbers higher than 10^4
)
(reports
    |> ggplot()
    + geom_line(aes(date, value))
    + theme_bw()  
)
```
<BMB>I would prefer to run `theme_set(theme_bw())` at the beginning of the lesson (if people ask you can explain why their plots look different). Also, I have a preference for including the basic/shared mappings in the `ggplot()` call so they can be shared by several downstream geoms (i.e. `ggplot(aes(date, value)) + geom_point()`)</BMB>

![](figures/exploratory-plot-reports-1.png)<!-- -->

Let’s focus on a single wave so we don't need to worry
about variation over time in the epidemic parameters and other complications arising from multiple epidemic waves.

``` r
early_reports = filter(reports, date < as.Date("2020-07-01"))
(early_reports
    |> ggplot()
    + geom_line(aes(date, value))
    + theme_bw()
)
```

![](figures/early-reports-1.png)<!-- -->

Before comparing these data with simulations, we update the `sir` model
to make it slightly more realistic.  Our goal is to make an initial plot containing both a reasonable simulation run and the  observed data, to serve as a foundation for
further model modifications.

``` r
sir_covid = mp_tmb_insert(sir

    ## Modify the the part of the model that
    ## is executed once "during" each iteration
    ## of the simulation loop.
    , phase = "during"

    ## Insert the new expressions at the 
    ## end of each iteration.
    ## (Inf ~ infinity ~ end)
    , at = Inf

    ## Specify the model for under-reporting as
    ## a simple fraction of the number of new
    ## cases every time-step.
    , expressions = list(reports ~ report_prob * infection)

    ## Update defaults to something a little more 
    ## reasonable for early covid in Ontario.
    , default = list(
          gamma = 1/14      ## 2-week recovery
        , beta = 2.5/14     ## R0 = 2.5
        , report_prob = 0.1 ## 10% of cases get reported
        , N = 1.4e7         ## Ontario population  
        , I = 50            ## start with undetected infections
    )
)
print(sir_covid)
```

    ## ---------------------
    ## Default values:
    ## ---------------------
    ##     quantity        value
    ##         beta 1.785714e-01
    ##        gamma 7.142857e-02
    ##            N 1.400000e+07
    ##            I 5.000000e+01
    ##            R 0.000000e+00
    ##  report_prob 1.000000e-01
    ## 
    ## ---------------------
    ## Before the simulation loop (t = 0):
    ## ---------------------
    ## 1: S ~ N - I - R
    ## 
    ## ---------------------
    ## At every iteration of the simulation loop (t = 1 to T):
    ## ---------------------
    ## 1: mp_per_capita_flow(from = "S", to = "I", rate = "beta * I / N", 
    ##      abs_rate = "infection")
    ## 2: mp_per_capita_flow(from = "I", to = "R", rate = "gamma", abs_rate = "recovery")
    ## 3: reports ~ report_prob * infection

| <img src="images/exercise.svg" width="120" />                                                                                                                                                                                                                                                               |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Take a moment to reconcile the code that produced this model update, with the printout of the updated model.<br><br>Did anything surprise you?<br><br>Would you have made different modelling choices?<br><br>If so, what questions do you have about how to code them? |

Now we [simulate](#simulating-dynamics) the trajectory of the `reports`
variable in the model, and plot it along with the observed data.<BMB>: plotting "against" observed data would suggest a predicted-vs-observed scatter plot to me ...</BMB>

<BMB>Do you discuss handling time anywhere? (Even vs uneven spacing, missing data, aggregating daily to weekly cases, time steps vs dates ...</BMB>
``` r
sim_early_reports = (sir_covid 
    |> mp_simulator(
          time_steps = nrow(early_reports)
        , outputs = "reports"
    )
    |> mp_trajectory()
    |> mutate(
        date = min(early_reports$date) + days(time)
    )
)
comparison_early_reports = bind_rows(
      list(
          simulated = sim_early_reports
        , observed = early_reports
      )
    , .id = "source"
)
(comparison_early_reports
    |> ggplot()
    + geom_line(aes(date, value, colour = source))
    
    ## constrain y-axis to the range of the
    ## observed data, otherwise the simulations
    ## will make it impossible to see the data
    + coord_cartesian(ylim = c(0, max(early_reports$value, na.rm = TRUE)))
    + theme_bw() + theme(legend.position="bottom")
)
```
<BMB>would `ylim(0, ...)` be simpler than `coord_cartesian()` ?</BMB>

![](figures/early-covid-comparison-1.png)<!-- -->

It took me a few iterations of model adjustments to make this plot,
<BMB>What are you hiding? It's good to admit this didn't work on the first try,
but would also make me wonder what your process was and how you knew what adjustments to make ...</BMB>
which illustrates a reasonably good fit before the peak of the wave and
quite poor fit after it. Still, the next two concepts argue that this is
quite a bit of progress and sets us up nicely for next steps.
<BMB>last sentence is unclear to me. Do we need it?</BMB>

| <img src="images/concept.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                 |
|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Goodness of Fit Can Come Later**<br>Being able to generate simulations that are comparable to observed data is an important step in and of itself in applied public health modelling, even if the comparison shows that the model is inconsistent with the data. Just having this comparison pipeline sets us up to assess whether subsequent model modifications help explain the system. |

| <img src="images/concept.svg" width="120" />                                                                                                                               
<BMB>Note that you have to get the parameters in a reasonable ballpark, as well as having an adequate model structure? It can be easier to work backward from more meaningful epidemiological parameters (such as $R_0$) than trying to figure out $\beta$ from scratch</BMB>

|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Bad Fits Can Be Due to Bad Models and/or Bad Data**<br>It can be tempting to assume that lack of fit can mean that the model is bad, but in applied work this is not good enough. What we want is to make the most appropriate recommendations to public health policy makers, and this goal can be jeopardized by bad data as well as bad modelling. Sometimes the exploration step can involve producing various simulations to build up hypotheses, and these hypotheses can be more useful to policy makers than a model that fits extremely well to a dataset that we believe has low quality. Unfortunately there is no free lunch here, and we need to be wise.<BMB>I don't quite get this point, or maybe it's not expressed clearly. The heading is certainly plausible, but what does "not good enough" mean? What does "we need to be wise" mean? What is your take-home message here?</BMB>|

### Reporting Delays

When cases are reported after some delay, we cannot directly compare
the simulated incidence values from our model with observed case
reports from the same time period. The following toy example
shows how the apparent peak in the data follows the simulations.

![](figures/apparent-peak-1.png)<!-- -->

So before comparing the simulations with observed data, we use
*convolution* to transform the simulated incidence values into a time
series of reported cases that is expected by the model <BMB>or, "to model
the process of reporting delay"</BMB>. Keep reading to
find out specifically how this is done.

![](figures/convolution-series-1.png)<!-- -->

Now we can now compare the observed and expected reported cases in a
manner that accounts for reporting delays.

![](figures/report-example-1.png)<!-- -->

In this method we assume that each new case waits a Gamma-distributed random
amount of time before it is observed.<BMB>"that the elapsed time between a case
occurring (e.g., an infection event) and the time it is observed (a case report)
follows a Gamma distribution</BMB> The average delay, and the variation in
delay time (the mean and coefficient of variation of the Gamma distribution), can
either be based on outside knowledge, or (in principle) can be estimated from the data. During the
COVID-19 pandemic the McMaster group found that a Gamma distribution
with mean delay of 11 days and coefficient of variation of 0.25 was a
reasonable model. <BMB>Delay parameters would be very hard to estimate without some
independent data stream ... don't overstate our confidence in these parameters - we didn't calibrate,
just picked some numbers we thought were reasonable ...</BMB>

The following figure illustrates how a single expected case report is
computed, in this case at time 26. The Gamma distribution of delay times
is represented as bars giving the probability that a new case at a
particular time is reported at time 26, which is given as the dashed
horizontal line. Note how the reported number of cases is roughly near
(but not identical) to the simulated number of cases at the peak of the
delay distribution - we would expect most of our
reports now to come from the highest probability time in the past.
<BMB>this isn't entirely clear to me, but I'm not looking at the picture.</BMB>

![](figures/explain-convolution-1.png)<!-- -->

Most of the simple models provided in the `macpan2` model library don't account for 
reporting delays. We can use the `mp_tmb_insert_reports()` function to add 
reporting delays to the `si` model:

``` r
si_with_delays = (si
 |> mp_tmb_insert_reports(
      incidence_name = "infection"
    , mean_delay = 11
    , cv_delay = 0.25
    , report_prob = 1
    , reports_name = "reports"
 )
)
(si_with_delays
  |> mp_simulator(
      time_steps = 50L
    , outputs = c("infection", "reports")
  )
  |> mp_trajectory()
  |> ggplot()
  + geom_line(aes(time, value, colour = matrix))
  + theme_bw()
)
```

![](figures/sir-with-delays-1.png)<!-- -->

Notice how the `11`-day reporting delay specified using the `mean_delay`
argument of the `mp_tmb_insert_reports` function has produced a
`reports` variable that peaks about `9` or `10` days after the new
`infection` peak. <BMB>why are 9, 10, 11 typeset in monospace font?</BMB> [Non-linear
averaging](https://en.wikipedia.org/wiki/Jensen's_inequality) has
shifted the individual-level delay of `11` days to something a little
lower at the population level. Still, the `mp_tmb_insert_reports`
function has had the desired qualitative effect of shifting the
`reports` peak to the right of the `infection` peak.
<BMB>Nonlinear averaging stuff too much detail?</BMB>

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Read the documentation on [mp_tmb_insert_reports](https://canmod.github.io/macpan2/reference/mp_tmb_insert_reports), and have a look at the following [model library](https://canmod.github.io/macpan2/articles/examples) examples that use this delay modelling functionality: [shiver](https://github.com/canmod/macpan2/tree/main/inst/starter_models/shiver), [macpan_base](https://github.com/canmod/macpan2/tree/main/inst/starter_models/macpan_base), [wastewater](https://github.com/canmod/macpan2/tree/main/inst/starter_models/ww). |

### Hospital Admissions and Occupancy

<BMB>awkward sentence</BMB> When these data are available and of high quality it is typically
straightforward to compare them with simulations. One must obviously
have a model containing hospital compartments (or modify an existing
model to contain such compartments). One must also make sure that
admissions are compared with *flows into* a hospital compartment and
that occupancy is compared with a *state variable* of a hospital
compartment.
<BMB>In general I think 'one' is awkward for this format, I would generally use "you" or "we" (I have probably made some edits along these lines</BMB>
<BMB>Do you want to make the incidence vs prevalence point somewhere more generally (in a box?)</BMB>

| <img src="images/tip.svg" width="120" />                                                                                                 |
|:-----------------------------------------------------------------------------------------------------------------------------------------|
| The [shiver](https://github.com/canmod/macpan2/tree/main/inst/starter_models/shiver) model gives an example that uses hospital data. |

### Symptom Status

The simple compartmental models (i.e.,
[si](https://github.com/canmod/macpan2/tree/main/inst/starter_models/si),
[sir](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir),
[sir_waning](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir_waning),
[seir](https://github.com/canmod/macpan2/tree/main/inst/starter_models/seir))
do not account for variation in symptoms amongst infected individuals.
However, the severity of symptoms can have a big impact on what cases
get reported.

For some applications we want to distinguish between different
symptom statuses; we do this by splitting up the infected compartment. This
splitting can be done by replacing calls to
`mp_per_capita_flows()` that involve `I` with flows that involve several
`I` boxes (e.g., `I_mild`, `I_severe`) that group together individuals
with similar symptom status.

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                             |
|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| The [macpan_base](https://github.com/canmod/macpan2/tree/main/inst/starter_models/macpan_base) model uses this technique. A discussion of this technique is also given in the [examples on the mp_per_capita_flow() help page](https://canmod.github.io/macpan2/reference/mp_per_capita_flow.html#ref-examples). |

| <img src="images/exercise.svg" width="120" />                                                                                                        |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Modify the [si](https://github.com/canmod/macpan2/tree/main/inst/starter_models/si) model so that it has three boxes: `S`, `I_mild`, and `I_severe`. |

In cases where report data are broken down by symptom status, this
technique of splitting up `I` into several boxes might be sufficient.
If cases with different symptom statuses are combined in
reports, we need to an add an additional model component that computes
a weighted sum of the incidence (or prevalence) of cases with different symptom
status, based on the relative likelihood that infections different symptom statuses will be reported.
This additional component is related to the [reporting delay](#reporting-delays) functionality.<BMB>how?</BMB>

### Seroprevalence

[Serological data](https://en.wikipedia.org/wiki/Seroprevalence) can often be used to estimate the
degree of immunity in the population, which can in
turn be used to estimate the number of individuals in recovered,
vaccinated, and otherwise immune (or partially immune) compartments. The
[shiver](https://github.com/canmod/macpan2/tree/main/inst/starter_models/shiver)
model description provides an example of this technique, which involves
either transforming the raw seroprevalence data so that it is comparable
with compartments in the model or adding a variable to the model that
produces a transformation of the variables tracking immune states that
is comparable with the raw seroprevalence data.

| <img src="images/concept.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Multiple Trajectories**<br>Models that can simulate multiple empirically observable variables have a better chance of being reliable. For example, a model that only generates case reports will be less able to learn from data as one that can generate seroprevalence as well. In particular, seroprevalence will give better estimates of recovery parameters (for obvious reasons) but also might do so for transmission parameters as well because of better estimates of the size of the susceptible pool. <BMB>This box is vaguely written: what is a "better chance of being reliable"? Generally a bit unclear</BMB> |

### Vaccination

If you have data on the vaccination schedule in a population (e.g., number of doses
administered per time-step for each time-step in the simulation), one
simple way to model vaccination is to create a [time-varying
parameter](https://canmod.github.io/macpan2/articles/time_varying_parameters)
that moves a known time-dependent number of individuals from a
susceptible compartment to a protected compartment. (An even simpler
variation is to assume that a constant number of doses are administered per day.)

One potential issue with this approach is that in some cases it can reduce the number of
simulated susceptible individuals below zero. This impossible scenario occurs
happen when the simulated number of `S` individuals drops below the
number of doses per day.<BMB>This is an odd way to put it: maybe "when the 
observed number of daily vaccinations is larger than the number of
`S` individuals remaining in the simulation".</BMB> 
You could address this issue setting a ceiling (equal to the current simulated
number of susceptibles) on the number of simulated daily vaccinations . 
This approach causes problems when calibrating, however;
as we will learn in the [Parameterization](#parameterization)
section, `macpan2` depends on the behaviour of the system being a *differentiable* (smooth)
function of the model parameters, which rules out sharp thresholds. 
We can get around this problem by modeling the number of doses as a 
saturating functions of `S`, a technique that is covered [here with the shiver
model](https://github.com/canmod/macpan2/tree/main/inst/starter_models/shiver#variable-vaccination-rate).
This technique is also discussed in the [examples on the mp_per_capita_flow() help
page](https://canmod.github.io/macpan2/reference/mp_per_capita_flow.html#ref-examples).

### Wastewater Pathogen Concentrations

<BMB>Say something general about wastewater?</BMB>
Wastewater models must have two kinds of compartments: those that
represent numbers of people and those that represent the concentration
of a pathogen in wastewater. Flows from a person-box into a
wastewater-box (e.g., shedding from `I` to `W`) requires the
`mp_per_capita_inflow()` function instead of the `mp_per_capita_flow()`
function (see
[here](https://canmod.github.io/macpan2/reference/mp_per_capita_flow)
for details); flowing from `I` to `W` does not reduce `I`, because people are 
*shedding* pathogens, not *becoming* pathogens. The `mp_per_capita_inflow()` function ensures that this shedding process
increases `W` without decreasing `I`. This
technique is illustrated
[here](https://github.com/canmod/macpan2/tree/main/inst/starter_models/ww).

### Vital Dynamics

In many cases birth and death happen at low enough rates relative to
transmission dynamics that we can assume that the population size is
constant, with no birth or death. For long-term modeling of endemic diseases,
we may want to relax this assumption.

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                     |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| The [sir_demog](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir_demog) model includes birth and death. You can use the machinery illustrated in this example to add vital dynamics to another model. Birth and death processes use `mp_per_capita_inflow()` and `mp_per_capita_outflow()` rather than `mp_per_capita_flow()` (see [here](https://canmod.github.io/macpan2/reference/mp_per_capita_flow) for examples and details). |

### Importation and Stochasticity

Large jurisdictions can be assumed to be closed (no immigration or emigration - especially, no importation of disease),
deterministic systems. Small populations
however are more strongly impacted by stochasticity.
<BMB>This is kind of a funny category. Do these belong together? I understand what your example is about, but you
need to be clearer that you're talking about stochastic *importation* - otherwise I would have assumed that you
were talking about process noise (which macpan2 can only handle in simulations, not in calibration)</BMB>

| <img src="images/tip.svg" width="120" />                                                                                                                              |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| The [awareness](https://github.com/canmod/macpan2/tree/main/inst/starter_models/awareness) model in the library illustrates how `macpan2` can be used in these cases. |

## Parameterization

Participants will learn how to parameterize models for making inferences
about a particular population and public health problem.

### Modify Default Values

We have already shown how to modify default values - the default
values provided in the [model
library](https://canmod.github.io/macpan2/articles/example_models) will
almost always need to be adjusted for use with a particular disease system.
See the `sir_covid` model object that we produced above in [this
section](#compare-simulated-and-observed-incidence).

### Express Uncertainty in Model Parameters

[The `default` argument](#modify-default-values) of the
`mp_tmb_model_spec()` and `mp_tmb_update()` functions allows us to
supply prior information on model parameter values, but gives us no
opportunity to express uncertainty about these defaults.
<BMB>This is confusing/vague. By "prior information" you mean "values from the literature"?</BMB>
Later in the
[Optimized Calibration](#optimized-calibration) section we will see how
to combine a model specification with [distributional
specifications](https://canmod.github.io/macpan2/reference/distribution)
for the parameters that we would like to estimate through calibration.

| <img src="images/tip.svg" width="120" />                                                                                                                                                                 |
|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| This [article](https://canmod.github.io/macpan2/articles/likelihood_prior_specs.html#priors-on-model-parameters) explains how priors can be specified in the context of various use-cases.   |

### Optimized Calibration

<BMB>What is "optimized calibration"??</BMB>

We have covered a lot of ground above, but it has all paved the way
towards the topic of formally calibrating a model using data. Before
getting to this point we needed to do the following.

-   [Find a starting model specification](#starter-model-library).
-   [Simulate from this model](#simulating-dynamics)
-   [Modify the model](#basics-of-modifying-model-specifications) to work 
    with different kinds of observed data – for
    example:
    -   [Case reports](#reporting-delays)
    -   [Hospitalization data](#hospital-admissions-and-occupancy)
    -   [Seroprevalence](#seroprevalence)
    -   [Vaccination](#vaccination)
    -   [Wastewater pathogen
        concentrations](#wastewater-pathogen-concentrations)
-   [Express prior uncertainty about model
    parameters](#express-uncertainty-in-model-parameters)

Now we can create a model
[calibrator](https://canmod.github.io/macpan2/reference/mp_tmb_calibrator)
object that combines (1) a model specification, (2) a data set that can
be compared with simulations from this model, and (3) a list of
distributional assumptions about model parameters that we would like to
estimate <BMB>'distributional assumptions' is a little vague, may also be confused with 'distributional specifications' in the priors section above</BMB>. Such a calibrator can be used to fit these parameters to data,
and then simulate and forecast observable variables using this
calibrated model. These calibrators can also be used to generate
confidence intervals for these simulations and forecasts using
(regularized) maximum likelihood theory.

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| This [article](https://canmod.github.io/macpan2/articles/calibration) steps through the process of calibrating a compartmental model specification, using the sanity check of fitting a model to simulations generated from the same model. [The sequel to this article](https://canmod.github.io/macpan2/articles/real_data) illustrates these techniques using real scarlet fever data from the [International Infectious Disease Data Archive](https://github.com/canmod/iidda/blob/main/README.md). |

### MAP Estimation

The recommended approach to model fitting with `macpan2` is [maximum a
posteriori (MAP)
estimation](https://en.wikipedia.org/wiki/Maximum_a_posteriori_estimation),
which finds the values of parameters at the peak of a posterior
distribution. This approach allows us to get approximate Bayesian
uncertainty estimates by approximating the posterior distribution as a
multivariate Gaussian centered at the peak obtained by MAP. One is able
to declare that parameters be considered random effects, which means
that they are integrated out of the posterior distribution using the
[Laplace
approximation](https://en.wikipedia.org/wiki/Laplace%27s_approximation).
Using this approach allows us to reduce the dimensionality of the
posterior distribution used in MAP by only optimizing over the fixed
effect parameters.

This approximate posterior distribution can be used to obtain confidence
/ credible intervals for any continuous function of model variables. The
two main examples of this are as follows.

1.  Confidence intervals on fitted parameters using
    `mp_tmb_coef(., conf.int = TRUE)`.
2.  Confidence intervals on fitted trajectories using
    `mp_trajectory_sd()`

This approach to computing confidence intervals using Gaussian
approximations to the posterior is a special case of the [delta
method](https://en.wikipedia.org/wiki/Delta_method) that is implemented
in [TMB](https://github.com/kaskr/adcomp), which is described
[here](https://kaskr.github.io/adcomp/_book/Appendix.html#theory-underlying-sdreport).

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Example calibrations of varying levels of complexity**<br>[Intro to calibration using toy data](https://canmod.github.io/macpan2/articles/calibration)<br><br>[Intro to calibration using real scarlet fever data](https://canmod.github.io/macpan2/articles/real_data)<br><br>[For hackers only](https://canmod.github.io/macpan2/articles/calibration_advanced.html)<br><br>[McMaster COVID model](https://github.com/canmod/macpan2/tree/main/inst/starter_models/macpan_base#calibration)<br><br>[Wastewater model of COVID](https://github.com/canmod/macpan2/tree/main/inst/starter_models/ww#calibration)<br><br>[SHIVER model to COVID](https://github.com/canmod/macpan2/tree/main/inst/starter_models/shiver#calibration-example)<br><br>[SEIR model using toy data](https://github.com/canmod/macpan2/tree/main/inst/starter_models/seir#calibration) |

### Likelihood and Prior Distribution Assumptions

You are free to declare any value in the model to be a trajectory or
parameter. Trajectories are compared with observed data to produce a
likelihood component (e.g., case reports are distributed with a negative
binomial distribution with dispersion parameter `1`) and parameters are
used to produce a component of the prior distribution (e.g., the
transmission rate is log normally distributed with mean `0.2` and
standard deviation `1`). Available distributional assumptions and
settings are described
[here](https://canmod.github.io/macpan2/articles/likelihood_prior_specs),
but inspecting the examples linked to in the previous FAQ will be more
useful for figuring out how to use this functionality.

### Can I hack `macpan2` to use MCMC esimation?

[Yes](https://canmod.github.io/macpan2/articles/calibration_advanced.html#hamiltonian-mc).

### Calibrating Time-Varying Parameters

A common approach for making simple models (i.e.,
[si](https://github.com/canmod/macpan2/tree/main/inst/starter_models/si),
[sir](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir),
[sir_waning](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir_waning),
[seir](https://github.com/canmod/macpan2/tree/main/inst/starter_models/seir),
[shiver](https://github.com/canmod/macpan2/tree/main/inst/starter_models/shiver))
applicable to complex reality, is to use time-varying parameters. A
common example from the COVID-19 pandemic involved the variation of the
transmission rate through time in response to public health measures.

| <img src="images/tip.svg" width="120" />                                                                                                                        |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| This [article](https://canmod.github.io/macpan2/articles/time_varying_parameters_advanced) describes several approaches to calibrating time-varying parameters. |

## Inference

Participants will learn how to make inferences using realistically
parameterized models.

### Assessing Model Fits

If you have a `model_calibrator` object fitted to a dataset called
`fitted_data`, you can visualize the goodness-of-fit of this model to
the data using `ggplot2` in the following manner.

``` r
fitted_data = mp_trajectory(model_calibrator)
(observed_data
  |> ggplot()
  + geom_point(aes(time, value))
  + geom_line(aes(time, value), data = fitted_data)
  + theme_bw()
)
```

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                                      |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [This section](https://canmod.github.io/macpan2/articles/calibration#step-3-check-the-fit) of the article on model fitting illustrates other techniques, such as checking convergence of the optimizer, getting parameter estimates with confidence intervals, and visualizing the goodness-of-fit with confidence intervals. |

### Forecasts

Forecasts can be made by extending the [simulation
bounds](https://canmod.github.io/macpan2/reference/mp_sim_bounds), and
using the general tools for [assessing model
fits](#assessing-model-fits).

### Counter-Factuals

Counter-factuals can be introduced by using the [model modification
tools](#basics-of-modifying-model-specifications), and using the general
tools for [assessing model fits](#assessing-model-fits).
Counter-factuals commonly involve making parameters time-varying in the
forecast period (e.g., in 30 days the transmission rate will change due
to non-pharmaceutical interventions being considered).

### Reproduction Numbers of Generation Interval Distributions

[This example
model](https://github.com/canmod/macpan2/tree/main/inst/starter_models/macpan_base#computing-mathcalr_0-with-a-cohort-model)
includes a discussion of how to compute reproduction numbers and the
moments of the generation interval distribution for a general
compartmental model.

## Stratification

When one extends one of the simple models (i.e.,
[si](https://github.com/canmod/macpan2/tree/main/inst/starter_models/si),
[sir](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir),
[sir_waning](https://github.com/canmod/macpan2/tree/main/inst/starter_models/sir_waning),
[seir](https://github.com/canmod/macpan2/tree/main/inst/starter_models/seir),
[shiver](https://github.com/canmod/macpan2/tree/main/inst/starter_models/shiver))
to be stratified (e.g., by age), transmission/infection processes
[become more
complex](https://www.sciencedirect.com/science/article/abs/pii/0025556494000658).
In these cases one may specify `S`, `E`, and `I` as vectors, and
infection rates as matrix operations involving these vectors and
matrices of transmission parameters. This approach is desecribed
[here](https://canmod.github.io/macpan2/articles/state_dependent_rates).

<!-- omit from toc -->

## Table of Contents

-   [Preliminaries](#preliminaries)
    -   [Syllabus](#syllabus)
    -   [Philosophy](#philosophy)
    -   [Organization](#organization)
    -   [Code Style](#code-style)
    -   [Dependencies](#dependencies)
-   [Exploration](#exploration)
    -   [Starter Model Library](#starter-model-library)
    -   [Simulating Dynamics](#simulating-dynamics)
    -   [Relating Model Specifications to Box
        Diagrams](#relating-model-specifications-to-box-diagrams)
    -   [Relating Model Specifications to Explicit
        Dynamics](#relating-model-specifications-to-explicit-dynamics)
    -   [Basics of Modifying Model
        Specifications](#basics-of-modifying-model-specifications)
    -   [Compare Simulated and Observed
        Incidence](#compare-simulated-and-observed-incidence)
    -   [Reporting Delays](#reporting-delays)
    -   [Hospital Admissions and
        Occupancy](#hospital-admissions-and-occupancy)
    -   [Symptom Status](#symptom-status)
    -   [Seroprevalence](#seroprevalence)
    -   [Vaccination](#vaccination)
    -   [Wastewater Pathogen
        Concentrations](#wastewater-pathogen-concentrations)
    -   [Vital Dynamics](#vital-dynamics)
    -   [Importation and Stochasticity](#importation-and-stochasticity)
-   [Parameterization](#parameterization)
    -   [Modify Default Values](#modify-default-values)
    -   [Express Uncertainty in Model
        Parameters](#express-uncertainty-in-model-parameters)
    -   [Optimized Calibration](#optimized-calibration)
    -   [MAP Estimation](#map-estimation)
    -   [Likelihood and Prior Distribution
        Assumptions](#likelihood-and-prior-distribution-assumptions)
    -   [Can I hack `macpan2` to use MCMC
        esimation?](#can-i-hack-macpan2-to-use-mcmc-esimation)
    -   [Calibrating Time-Varying
        Parameters](#calibrating-time-varying-parameters)
-   [Inference](#inference)
    -   [Assessing Model Fits](#assessing-model-fits)
    -   [Forecasts](#forecasts)
    -   [Counter-Factuals](#counter-factuals)
    -   [Reproduction Numbers of Generation Interval
        Distributions](#reproduction-numbers-of-generation-interval-distributions)
-   [Stratification](#stratification)

<!-- omit from toc -->

## Footnotes

[^1]: These defaults can be overridden but let’s ignore that for now. We
    will cover the various ways to override defaults later.
