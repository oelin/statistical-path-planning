# Generative Path Planning

**[Introduction](#introduction) | [Method](#method) | [Evaluation](#evaluation) | [Conclusion](#conclusion)**

We train a generative model to solve path planning problems by mimicing the behaviour of conflict-based search (CBS) in unknown environments. We demonstrate that the features proposed in [[1]](#references) are highly correlated with actions taken by CBS and that this relationship is primarily linear.

## Installation

This project is packaged using [Poetry](https://python-poetry.org/) and can be easily installed with `pip`.

```sh
pip install git+https://github.com/oelin/generative-path-planning
```

We recommend installing to a virtual environment, for example using `venv`. To verify your installation, try running `gpp version`. You should see something like this:

```sh
gpp version 1.0.0
```

Run `gpp help` for information on how to train, test and sample from our models.


## Introduction

Multi-agent path planning (MPP) is the task of finding efficient, collision-free paths for mutliple agents within a shared environment. It has numerous applications in fields such as search and resue [[2]](#references), UAV navigation [[3]](#references), and game design [[4]](#references). Conflict-based search (CBS), propsed by [(Sharon et al., 2015)](#references), is an efficient, optimal MPP algorithm which divides the problem into two levels of abstraction.

While CBS is highly effective, it requires complete knowledge of the target area to find solutions. This is often infeasible for real-world scenarios featuring unknown or dynamic environments. To address this limitation, [(Qingbao et al., 2020)](#references) propose a decentralized variant of CBS using graph neural networks (GNNs) and imitation learning. They train a generative model to approximate the distribution of CBS solutions, *conditional* on each agent's local field of view (FOV).

We take a similar approach in this work however find that GNNs are *not required* to approximate CBS effecitvely. We find that even linear models can achieve above 93% accuracy in predicting the actions of CBS. These models may be useful for applications involving agents with very limited hardware resources.


## Method

Borrowing from [[1]](#references), we cast the problem of mimicing CBS to a supervised learning task in which we wish to predict the action taken by an agent given its local FOV. Note each labelled example is assumed to be i.i.d. Although this assumption may be false, the success of [[1]](#references) suggests that modelling temporal dependencies is not required to achieve high accuracy. 

Using this framework, we compile a large dataset containing CBS solutions to path planning problems on 20x20 maps [[5]](#references). Specifically, we train a three layer CNN, a logistic regression model and a naive bayes classifier (NBC).


## Evaluation

Despite the simplicity of our models, they demonstrate an ability to accurately mimic the behaviour of CBS in unknown environments. 

### Performance


### Visualizations 

Visualizations from our logistic regression model elucidate linear correlations between an agent's FOV, and actions it takes when controlled by CBS. For instance, the first image shows a strong positive correlation between the action `stay` and contexts where the agent's goal is in the center of its FOV. This makes intuitive sense as these are exactly the contexts in which the agent has *reached* its goal. 

**FOV layer:** `goal position`  
**Action:** `stay`

![](https://github.com/oelin/generative-path-planning/blob/main/images/features0.png)

**FOV layer:** `goal position`  
**Action:** `up`

![](https://github.com/oelin/generative-path-planning/blob/main/images/features1.png)

**FOV layer:** `goal position`  
**Action:** `right`

![](https://github.com/oelin/generative-path-planning/blob/main/images/features2.png)

**FOV layer:** `agent position`  
**Action:** `right`

![](https://github.com/oelin/generative-path-planning/blob/main/images/features3.png)



## References
1. qingbao paper
2. http://cs229.stanford.edu/proj2017/final-reports/5241779.pdf
3. https://www.sciencedirect.com/science/article/abs/pii/S0305054814001749

