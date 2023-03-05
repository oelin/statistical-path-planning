# Generative Path Planning

**[Introduction](#introduction) | [Models](#models) | [Training](#training) | [Evaluation](#evaluation) | [Conclusion](#conclusion)**

We train a generative model to solve path finding problems by mimicing the behaviour of conflict-based search (CBS) in unknown environments. We demonstrate that the features proposed in [[1]](#) are highly informative towards predicting the actions of CBS and that this relationship is mostly linear in nature.


## Installation

This project is packaged using [Poetry](https://python-poetry.org/) and can be easily installed with `pip`.

```sh
pip install git+https://github.com/oelin/generative-path-finding
```

We recommend installing to a virtual environment, for example using `venv`:

```sh
python -m venv venv

source venv/bin/activate
```

To verify that the installation was successful, try running `gpf version`. You should see something like this:

```sh
gpf version 1.0.0
```

Then run `gpf help` for more information on how to train, test and sample from our models.


## Introduction

Multi-agent path planning (MPP) is the task of finding efficient, collision-free paths for mutliple agents within a shared environment. It has numerous applications to fields such as search and resue [[2]](#), UAV navigation [[3]](#), and game design [[4]](#). Conflict-based search (CBS), propsed by [(Sharon et al., 2015)](#), is an efficient, optimal MAPF algorithm which divides the problem into two levels of abstraction.

While CBS is highly effective, it requires complete knowledge of the target area to find solutions. This is often infeasible for real-world scenarios featuring unknown or dynamic environments. To address this limitation, [(Qingbao et al., 2020)](#) propose a decentralized variant of CBS using graph neural networks (GNNs) and imitation learning. They train a generative model to approximate the distribution of CBS solutions, *conditional* on each agent's local field of view (FOV).

We take a similar approach in this work however find that GNNs are *not required* to approximate CBS effecitvely. We find that even linear models can achieve above 93% accuracy in predicting the actions of CBS. Such models are useful for scenarios involving agents with limited hardware.



## References
1. qingbao paper
2. http://cs229.stanford.edu/proj2017/final-reports/5241779.pdf
3. https://www.sciencedirect.com/science/article/abs/pii/S0305054814001749

