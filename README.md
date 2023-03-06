# Statistical Path Planning

**[Introduction](#introduction) | [Method](#method) | [Evaluation](#evaluation) | [Conclusion](#conclusion)**

We train a lightweight statistical model to solve path planning problems by mimicking the behaviour of conflict-based search (CBS) in dynamic environments. While [[Qingbiao et al., 2020]](#references) use graph neural networks (GNNs) for this task, we find that far simpler models perform competitively.

![](https://github.com/oelin/statistical-path-planning/blob/main/images/uav.webp)

>  Our 735-parameter logistic regression model correctly predicts over 95% of CBS actions despite only requring partial knowledge of the environment. This makes it a viable option for resource-constrained devices such as UAVs [[1]](#references).

![](https://github.com/oelin/statistical-path-planning/blob/main/images/example.gif)

> An example of statistical path planning using logistic regression. Five agents navigate towards five targets (shown in white), while avoiding obstacles and each other. Agents act independently, however their collective behaviour closely resembles CBS.


## Installation

This project is packaged using [Poetry](https://python-poetry.org/) and can be installed with `pip`.

```sh
pip install git+https://github.com/oelin/statistical-path-planning
```

To verify your installation, try running `spp version`. You should see something like this:

```sh
spp version 1.0.0
```


## Introduction

Multi-agent path planning (MPP) is the task of finding efficient, collision-free paths for mutliple agents within a shared environment. It has numerous applications in fields such as search and resue [[2]](#references), UAV navigation [[3]](#references), and game design [[4]](#references). Conflict-based search (CBS), propsed by [[Sharon et al., 2015]](#references), is an efficient, optimal MPP algorithm which divides the problem into two levels of abstraction.

While CBS is optimal, it requires a complete map of the target area in order to form plans. This can be problematic in scenarios where the environment is unknown or constantly changing. To address this limitation, [[Qingbao et al., 2020]](#references) propose a statistical approximation of CBS using graph neural networks (GNNs) and imitation learning. They train a GNN to mimic the behaviour of CBS by predicting the actions an agent will take given its local field of view (FOV).

We take a similar approach to [[Qingbao et al., 2020]](#references), however find that GNNs are *not required* to mimic CBS effectively. For instance, we train a 735-parameter logistic regression model to correctly predict over 95% of all agent actions. The ability to use such lightweight models makes statistical path planning a viable solution for resource-constrained devices such as UAVs [[5]](#references).


## Method

We cast the problem of mimicking CBS to a supervised learning task where the goal is to predict an agent's action given its local FOV. 


### Data Collection

To create a supervised learning dataset, we randomly generate several thousand MPP problems and find optimal solutions using CBS [[6]](#references). We then extract individiual actions from each solution, amounting to over four million labelled examples [(1)](#footnotes). The large size of this dataset helps to mitigate overfitting during training.


### Feature Extraction 

We represnt an agent's FOV using a 7x7 binary image containing three channels (735 features):

- `State`: encodes the relative position of agents within the FOV.
- `Goal`: encodes the relative position of an agent's goal within the FOV. Clipped to the FOV boundry when out of range.
- `Map`: encodes the relative position of obstacles within the FOV.

For instance, the following `State` channel shows three agents within the FOV. The current agent is always located in the centre.

```
0 0 0 0 0 1 0
0 0 0 0 0 0 0
0 0 0 0 0 0 0
0 0 0 1 0 0 0
0 0 0 0 0 0 0
0 0 0 1 0 0 0
0 0 0 0 0 0 0
```


## Evaluation

Despite the simplicity of our models, they demonstrate an ability to accurately mimic the behaviour of CBS in unknown environments. 


### Performance


### Visualizations 

Visualizations from our logistic regression model elucidate linear correlations between an agent's FOV, and actions it takes when controlled by CBS. For instance, the first image shows a strong positive correlation between the action `stay` and contexts where the agent's goal is in the center of its FOV. This makes intuitive sense as these are exactly the contexts in which the agent has *reached* its goal. 

**FOV channel:** `Goal`  
**Action:** `Stay`

![](https://github.com/oelin/generative-path-planning/blob/main/images/features0.png)

**FOV channel:** `Goal`  
**Action:** `North`

![](https://github.com/oelin/generative-path-planning/blob/main/images/features1.png)

**FOV channel:** `Goal`  
**Action:** `East`

![](https://github.com/oelin/generative-path-planning/blob/main/images/features2.png)

**FOV channel:** `State`  
**Action:** `East`

![](https://github.com/oelin/generative-path-planning/blob/main/images/features3.png)


## Footnotes

1. We make the conventional assumption that each labelled example is i.i.d. While this assumption may be false, the success of [[1]](#reference) suggests temporal dependencies do not play a significant role in determining agent actions.


## References
1. qingbao paper
2. http://cs229.stanford.edu/proj2017/final-reports/5241779.pdf
3. https://www.sciencedirect.com/science/article/abs/pii/S0305054814001749
