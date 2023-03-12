# Statistical Path Planning

**[Introduction](#introduction) | [Dataset](#dataset) | [Training](#training) | [Evaluation](#evaluation) | [Conclusion](#conclusion)**

We train a lightweight statistical model to solve path planning problems by mimicking the behaviour of conflict-based search (CBS) in dynamic environments. While [[Qingbiao et al., 2020]](#references) use graph neural networks (GNNs) for this task, we find that far simpler models are also effective.

![](https://github.com/oelin/statistical-path-planning/blob/main/images/uav.webp)

>  Our 740-parameter logistic regression model achieves over 95% accuracy in predicting CBS actions using *partial* knowledge of the environment. Its small size comes in handy for resource-constrained devices such as UAVs. 

![](https://github.com/oelin/statistical-path-planning/blob/main/images/example.gif)

> An example of statistical path planning using logistic regression. Five agents navigate towards five targets (shown in white), while avoiding obstacles and each other. Agents act independently however collectively approximate CBS.


## Introduction

Multi-agent path planning (MAPP) is the task of finding efficient, collision-free paths for mutliple agents within a shared environment. It has numerous applications, from search and resue operations [[2]](#references) to game design. Conflict-based search (CBS) propsed by [[Sharon et al., 2015]](#references), is an optimal MAPP algorithm which uses a divide-and-conquer approach to achieve high efficiency. 

While CBS *is optimal*, it requires *complete* knowledge of the envrionemnt prior to planning. This can be problematic in scenarios where the environment is dynamic or unpredictable. To address this limitation, [[Qingbao et al., 2020]](#references) propose a statistical approximation of CBS using graph neural networks (GNNs) and imitation learning. They train a GNN to mimic the behaviour of CBS by predicting the actions agents will take, given their local field of views (FOVs).

In this project we use a similar approach, however find that GNNs are *not required* to mimic CBS effectively. We train a lightweight, 740-parameter logistic regression model to perform the same task and find that it achieves over 95% accuracy. We also train a multi-layer perceptron (MLP) and small convolutional neural network (CNN), which achieve over 96% accuracy. 


## Dataset

We cast the problem of mimicking CBS to a supervised learning task where the goal is to predict an agent's action given its local field of view (FOV). To create a supervised learning dataset, we randomly generate several thousand MAPP problems with optimal solutions found using CBS [[4]](#references). We then extract individiual actions from each solution, resulting in approximately five million labelled examples [(1)](#footnotes). Our dataset's large size helps to mitigate the effects of overfitting during training.


### Features 

We represnt an agent's FOV using a 7x7 binary image containing three channels (147 features):

- `State`: encodes the relative position of agents within the FOV.
- `Goal`: encodes the relative position of an agent's goal within the FOV. Clipped to the FOV boundry when out of range.
- `Map`: encodes the relative position of obstacles within the FOV.

For instance, the following `State` channel shows three agents in different locations. Note that the *current* agent is always located in the centre.


```
0 0 0 0 0 1 0
0 0 0 0 0 0 0
0 0 0 0 0 0 0
0 0 0 1 0 0 0
0 0 0 0 0 0 0
0 0 0 1 0 0 0
0 0 0 0 0 0 0
```


### Storage

Due to the large size of our dataset, we store each example in compressed form and use chunking to support procedural download. The complete dataset is hosted on GitHub at [https://github.com/oelin/cbs-5](https://github.com/oelin/cbs-5).


## Training

We train three models on our dataset; a lightweight logistic regression model, a multi-layer perceptron, and a small convolutional neural network (CNN). All three models are trained using minibatch gradient descent with the Adam optimizer. We find that all three models perform comparably. 


## Evaluation


### Performance

Comparison of three models trained on CBS-5 **v0** for two epochs, and tested on CBS-5 **v1**.

| Model                 | # Parameters | Accuracy (Train) | Accuracy (Test) |
|-----------------------|--------------|------------------|-----------------|
| `logistic_regression` | 740          | 95.80%           | 95.78%          |
| `mlp`                 | 79,365       | 96.86%           | 96.83%          |
| `cnn`                 | 21,701       | 96.90%           | 96.90%          |

Overall, `cnn` achieves highest train and test accuracy, however not *sigificantly* higher than `logistic_regression`. Given that `logistic_regression` is almost 30 times smaller, the additional parameters offer relatively little benefit. 


### Explainability

Compared to deep neural networks, linear models tend to be far more explainable. We demonstrate this by visualizing some weights from our 740-parameter model.

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

In these visualizations, light pixels represent strong positive weights, whereas dark pixels represent strong negative weights. The first image shows a strong positive correlation between an agent's decision to `Stay` and the presence of a goal within the center of their FOV. This makes intuitive sense as agents should never move after reaching their goal. Similarly, the second image shows a strong correlation between an agent's decision to move `North` and the presence of a goal above them. These visualizations can be created for any channel-action pair to understand *why* the model makes certain decisions.


## Conclusion

In this project we demonstrate the capability of simple machine learning models to approximate optimal path planning algorithms through imitation learning. In comparison to traditional conflict-based search (CBS), our approach allows agents to operate in a decentralized manner which only requires partial knowledge of the environment. The marginal improvements made by increasing model complexity suggest the relationship between an agent's FOV and the action they take is reasonably simple. To improve predictive accuracy further, additonal factors could be taken into account such as inter-agent communication. 


## Footnotes

1. We make the conventional assumption that each labelled example is i.i.d. While this assumption may be false, the success of [[1]](#reference) suggests temporal dependencies do not play a significant role in determining agent actions.


## References

1. Li, Qingbiao. "Graph Neural Networks for Decentralized Multi-Robot Path Planning." ArXiv.org, 14 July 2020, https://arxiv.org/abs/1912.06095. 

2. Berger, Jean, et al. “An Innovative Multi-Agent Search-and-Rescue Path Planning Approach.” ScienceDirect, Pergamon, 23 June 2014, https://www.sciencedirect.com/science/article/abs/pii/S0305054814001749. 

3. Sharon, Guni. Conflict-Based Search for Optimal Multi-Agent Pathfinding. https://www.researchgate.net/publication/278400742_Conflict-based_search_for_optimal_multi-agent_pathfinding. 

4. Champagnie, Kale. “Oelin/CBS-5: A Large Dataset Containing Optimal Actions Taken by Conflict-Based Search (CBS) with Five Agents on Random 20x20 Maps.” GitHub, https://github.com/oelin/cbs-5. 
