Video Resources
================

This document describes videos about
[`macpan2`](https://canmod.github.io/macpan2/) that could be useful for
workshop participants.

-   [Videos to Watch Before the Workshop (with
    exercises)](#videos-to-watch-before-the-workshop-with-exercises)
    -   [Required to Watch Before the
        Workshop](#required-to-watch-before-the-workshop)
    -   [Potentially Useful to Watch Before the
        Workshop](#potentially-useful-to-watch-before-the-workshop)
    -   [Useful Workshop Preparation Exercises (not
        required)](#useful-workshop-preparation-exercises-not-required)
-   [Additional Videos](#additional-videos)

## Videos to Watch Before the Workshop (with exercises)

I’ve described some videos and related pre-workshop resources below. If
you have any questions about these or anything else before the workshop,
please leave a comment
[here](https://github.com/canmod/macpan-workshop/issues/7). Note that
commenting will require that you have a GitHub account, so either feel
free to get one or just [email me](mailto:swalk@mcmaster.ca) directly.

### Required to Watch Before the Workshop

Please watch the following videos to prepare for the workshop (links are
to Google Drive).

1.  5:06 minutes – [Background and motivation for
    `macpan2`](https://drive.google.com/file/d/1GPNZX59sJyfcIvScgnY78BBYlPO1fyOE)
2.  8:46 minutes – [Specifying simple
    models](https://drive.google.com/file/d/1BwAYWPi6e3PDn4AhqVsM14KkHie6Vx26)
3.  8:28 minutes – [Simulating from
    models](https://drive.google.com/file/d/1x3BTWenFmKokCG3IS4JcI5h8kj8J24y6)

| <img src="images/tip.svg" width="120" />                                                                                 |
|:-------------------------------------------------------------------------------------------------------------------------|
| Code that is discussed in the videos is [here](https://github.com/canmod/macpan-workshop/blob/main/code/intro-videos.R). |

### Potentially Useful to Watch Before the Workshop

If you are not familiar with R or the
[tidyverse](https://www.tidyverse.org/) suite of packages, please
consider watching the following videos from [this video
series](https://www.youtube.com/playlist?list=PL9qLtkRkGWLeh9UNptt_734t0u1zXz5wy).

1.  [R Programming: How to get
    started](https://www.youtube.com/watch?v=Ld_JZNNahbQ&list=PL9qLtkRkGWLeh9UNptt_734t0u1zXz5wy&index=3&pp=iAQB)
2.  [How to Install R
    Packages](https://www.youtube.com/watch?v=2bjeUV_ySyM&list=PL9qLtkRkGWLeh9UNptt_734t0u1zXz5wy&index=4)
3.  [Data Science with the Tidyverse in
    R](https://www.youtube.com/watch?v=q0uGggl3RbM&list=PL9qLtkRkGWLeh9UNptt_734t0u1zXz5wy&index=6)
4.  [Introduction to the Pipe
    Operator](https://www.youtube.com/watch?v=Stt3qEuIeso&list=PL9qLtkRkGWLeh9UNptt_734t0u1zXz5wy&index=6&pp=iAQB)

### Useful Workshop Preparation Exercises (not required)

The above videos give you the background to do the following exercises.
It would be great if you did some of these before the workshop, but it
is not required.

| <img src="images/exercise.svg" width="120" />                                                                                       |
|:------------------------------------------------------------------------------------------------------------------------------------|
| Create a model specification of an [SEIR model](https://en.wikipedia.org/wiki/Compartmental_models_in_epidemiology#The_SEIR_model). |

| <img src="images/exercise.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Create a model specification of an elaboration of an SEIR model that has been modified for measles (diagram immediately following this box). This modified SEIR model includes a box for vaccinated individuals, $V$, and isolated infectious individuals, $I_\text{iso}$. The force of infection, $\Lambda$, is given by $\frac{\beta I}{N}$, where $N$ is the total number of individuals in the population. The vaccine efficacy is $\epsilon$ and the proportion of exposed individuals who isolate is $\theta$. |

<img src="diagrams/measles-mike.svg" width="700" />

| <img src="images/exercise.svg" width="120" />      |
|:---------------------------------------------------|
| Specify a model that you use or are interested in. |

| <img src="images/exercise.svg" width="120" />                                                                                                                                       |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Simulate one or more of the state variables in one of the above models. A challenge here will be to choose good default numerical values for the parameters and initial conditions. |

| <img src="images/tip.svg" width="120" />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| In doing these exercises you might find it helpful to use the `macpan2` [website](https://canmod.github.io/macpan2) as a reference. Pages that could be particularly useful are the [quick start guide](https://canmod.github.io/macpan2/articles/quickstart) and documentation for the [`mp_tmb_model_spec`](https://canmod.github.io/macpan2/reference/mp_tmb_model_spec), [`mp_per_capita_flow`](https://canmod.github.io/macpan2/reference/mp_per_capita_flow), [`mp_simulator`](https://canmod.github.io/macpan2/reference/mp_simulator), and [`mp_trajectory`](https://canmod.github.io/macpan2/reference/mp_trajectory). |
