class: middle, center, title-slide

# Deep Learning

Lecture 10: Deep reinforcement learning

<br><br>
Guest lecture by Matthia Sabatelli<br>
[m.sabatelli@uliege.be](mailto:m.sabatelli@uliege.be )

---

# Today

Understand the field of Reinforcement Learning (RL) and see how it can be combined with neural networks.

- Markov Decision Processes (MDPs)
- Value functions and optimal policies
- Temporal Difference Learning
- Function approximators
- Tutorial
---

class: middle

# Reinforcement Learning

---

# Markov Decision Processes

A classical formalization when it comes to sequential decision making problems. They allow us to mathematically define RL problems for which **precise** and **sound** statements can be made.

An MDP consists of the following elements
- A set of possible states $\mathcal{S}$
- A set of possible actions $\mathcal{A}$
- A reward signal $R(s_t,a_t,s_{t+1})$
- A transition probability distribution $p(s_{t+1}|s_t,a_t)$

---
## Markov Decision Processes

In addition to these elements we have the **Agent-Environment Interface**.

The agent corresponds to the learner, sometimes also defined as the decision maker, which has the ability to **continually** interact with the environment.

Each time an action is performed the environment has the ability to change and will present **new** situations to the agent.

---

.center.width-40[![](figures/lec10/rl.jpg)]
.footnote[]()

---
## Markov Decision Processes

Differently from Supervised-Learning in RL we have to deal with the component of **time**: in case no conditions are specified we could even let an agent interact with an environment forever.

Specifically the agent and the environment interact at each of a sequence of **discrete** time-steps

$$t=0,1,2,3, . . ..$$

At each time step $t$ the agent receives a state representation $s_t$, selects an action $a_t$, and receives a numerical reward $r_t \in \mathcal{R} \subset \mathbb{R}$ after which it will find itself in a new state $s_{t+1}$.

This gives rise to *trajectories*:
$$s_t, a_t, r_t, s_{t+1}, ... $$

---

## Markov Decision Processes

Some important properties of MDPs

- $s$ and $a$ at time-step $t$ give all the **necessary** information that is required for predicting to which state the agent will step next
- The reward that is obtained is only determined by the **previous** action and not by the history of all previously taken actions.

$$p(r_t = R|s_t,a_t) = p(r_t = R| s_t, a_t, ..., s_1, a_1)$$

For predicting the future it does not matter how an agent arrived in a particular current state.

---
## Goals and Returns

So far we have properly defined how an agent can interact with an environment but have not seen **why*+1* this should be done.

- The purpose of an RL agent is formalized by $r_t \in \mathcal{R} \subset \mathbb{R}$, which is a numerical quantity that we want to **maximize**

- We do not want to maximize the immediate reward but rather the cumulative reward

$$G_t = r_t + r_{t+1} + r_{t+2} + ... + r_{T} $$

- Mathematically this can be seen as maximizing the expected value of the cumulative sum of a scalar signal

---
## Goals and Returns

To properly define the concept of return we need one additional component: the discount factor $\gamma$.

The idea of *discounting* allows our agent to select actions which will maximize the sum of **discounted** rewards, therefore maximizing the discounted return:

$$G_t=r_t+\gamma r_{t+1}, \gamma^{2} r_{t+2} + ... $$
$$G_t = \sum_{k-0}^{\infty}\gamma^{k}r_{t+k+1}.$$  

The discount factor $0\leq \gamma \leq 1$ and controls the trade-off between immediate and long-term rewards.

---

## Policies and Value Functions

Basically all RL algorithms involve the concept of **value function**, functions that are able to estimate how *good* or *bad* it is for an agent to be in a particular state.

  - The *goodness* of a state is defined in terms of future rewards that can be expected by being in state $s$.
  - Just being in a *good* state is not enough, since the rewards will depend on which actions will be performed in the future. We need the concept of **policy**:
  $$\pi: \mathcal{S} \rightarrow \mathcal{A} $$


RL methods specify how the agent's policy is changed as a result of its experience.

---

## Policies and Value Functions

When it comes to value functions there are two popular value functions we care about

- The **state-value** function:  
  $$V^{\pi}(s)=\mathbb{E}\bigg[\sum_{k=0}^{\infty}\gamma^{k}r_{t+k}\bigg| s_t = s, \pi \bigg]$$
- The **state-action** value function:
$$ Q^{\pi}(s,a)=\mathbb{E}\bigg[\sum_{k=0}^{\infty}\gamma^{k}r_{t+k} \bigg| s_t = s, a_t=a, \pi\bigg].$$

---
## Policies and Value Functions

Both value functions can compute the desirability of being in a specific state. The goal of a RL agent is to find
an **optimal policy** that realizes the optimal expected return

$$V^* (s)=\underset{\pi}{\max}\:V^{\pi}(s), \ \text{for all} \ s\in\mathcal{S}$$

and the optimal state-action value function
$$ Q^* (s,a)= \underset{\pi}{\max}\:Q^{\pi}(s,a) \ \text{for all} \ s\in\mathcal{S} \ \text{and} \ a \in\mathcal{A}$$

Both value functions satisfy the **Bellman optimality equation**, and can be learned via Monte-Carlo or Temporal-Difference learning methods.

---
## Policies and Value Functions

We have seen how to define value functions mathematically but what do they represent in practice?

- The environment is **unknown** so we do not have any prior which can help us out
- We can see value functions as some sort of **knowledge** representation of an agent
- If a value functions is accurate an agent will know everything he needs to know for interacting with an environment
- We can only interact with the environment and update our knowledge

---
## Monte Carlo (MC) Methods

- The first method than can be used for learning a value function without assuming complete knowledge of the environment: **model-free RL**

- The only requirement of MC methods is *experience*: sampling sequences of states, actions and rewards from an environment which can be even simulated

- We assume that RL episodes **eventually** terminate, no matter which actions are selected. Only at the end of an episode our value estimates and therefore a policy can get updated.

---
## Monte Carlo (MC) Methods

The idea is to compute the **real return** once an episode terminates

$$G_t = \sum_{k=0}^{\infty}\gamma^{k}r_{t+k+1}$$
and keep track of the value of a single state based on the amount of times this state has been visited
$$V(s_t)=\frac{\sum_{i=1}^{k}G_t(s)}{N(s)}$$
This estimate can then get updated as follows:
$$V(s_t):= V(s_t)+\alpha[G_t -V(S_t)].$$

---
## Monte Carlo (MC) Methods

There are some important **drawbacks** when it comes to MC methods:

- We need to wait until an episode is terminated because only then $G_t$ will be known
- Convergence is slowed down

A nice advantage however is that these kind of methods are **unbiased** when compared to their alternatives.

---
## Temporal Difference (TD)-Learning

*"The most central and novel idea of entire Reinforcement Learning"*

- TD-Learning is a combination of Monte Carlo ideas and dynamic programming ideas.

- Just like MC methods TD-Learning approaches can simply learn from raw experiences without the need for a model of the environment. Like DP techniques, the update estimates are based (in part) on other learned estimates.

- This means we do not have to wait until the end of an episode anymore but instead rely on **bootstrapping**.

$$V(S_t):= V(S_t)+\alpha[r_t+\gamma V(S_{t+1}) - V(s_t)]$$
---
## Temporal Difference (TD)-Learning

The core idea of TD-Learning is the concept of **TD-error**, or sometimes called target

$$\delta_t = r_t+\gamma V(s_{t+1})$$

- It corresponds to the only information that is needed in order to update our value estimates, remember that we **do not** have access to $V^* (s)$ and we want to overcome waiting for $G_t$!

- We are learning $V^* (s)$ by guessing the value estimates that the exact same function provides at $s_{t+1}$

---
## Temporal Difference (TD)-Learning

We have seen the simplest form of how to update an estimate based on another estimate, but is this really a **good idea**?

- TD-Learning methods do not require a model of the environment, its rewards and the $s_{t+1}$ probability distributions
- They are implemented in an online, fully incremental fashion
- We only need to wait one-step before starting learning (might also be a drawback!)
- TD methods are also **sound** and convergence of any fixed policy $\pi$ to $V^\pi$ is guaranteed.
This policy might however not be the optimal one!

- A convergence proof on the speed of TD methods vs MC methods is still missing!

---
## Eligibility Traces

So far we have seen that we can update a value function from the real final return $G_t$ obtained at the end of an episode, or based on an immediate future estimate $V(s_{t+1})$. There is however an intermediate approach between MC and TD-Learning called **TD $(\lambda)$**

$$G^{n}_{t}=r_t+\gamma r_{t+1}+\gamma^2 r_{t+2} + ... + \gamma^{n} V_t(s_{t+n})$$

With setting $0\leq\gamma\leq1$ we are able to compute updates based on n-step returns.

- The problem is that we would have to wait indefinitely for computing $G_t^{\infty}$. This is also known as *the problem of the forward view of* TD($\lambda$)

---
## Eligibility Traces

We could however partially overcome this problem by **incrementally** updating the eligibility trace of a state, which nicely allows us to perform n-step backups in an elegant way.

- For each state $s\in\mathcal{S}$ a trace $e_t(s)$ is kept in memory and initialized at 0
- At each state we update $e_t(s)$ as

$$e_t(s) = \begin{cases}
\gamma\lambda e_{t-1}(s) & \text{if} & s \neq s_t \\
\gamma\lambda e_{t-1}(s)+ 1 & \text{if} & s = s_t\\
\end{cases} $$

---
## Eligibility Traces

The trace of each state is increased every time that particular state is visited and decreases exponentially otherwise due to $\lambda$.

If we again consider the TD-error as:
$$\delta_t = r_t+\gamma V(s_{t+1})-V(s_t)$$

On every step we want to update a state in proportion to its eligibility trace, which results in the following update:

$$V(s_t):= V(s_t) + \alpha \delta_t e_t(s_t)$$

* A general mechanism for learning from n-step returns
* For $\lambda=1$ we have MC-Learning
* For $\lambda=0$ we have TD-Learning

---
## Exploration vs Exploitation

* We know that if a complete model of an environment is given it is easy to compute an optimal policy (like Dynamic-Programming).

* As we have seen so far in the general RL setting this is unfortunately not the case, therefore learning an optimal policy becomes as process of *trial and error*.

* During this process the only feedback that is available to the agent is the reward that is obtained at the end of an
action

---
## Exploration vs Exploitation

So why is the exploration-exploitation dilemma so **challenging?**

* In RL the amount of feedback that the agent gets compared to e.g. SL is much less. There is no direct relationship between a learning sample and its output which allows us to evaluate a **general** performance

* In SL we usually deal with **static** datasets, similarly in Unsupervised Learning we learn a hopefully useful partition of an unlabeled dataset. In RL we have to deal with **time**

* The RL "dataset" is considered as a **moving target** which makes it hard to quantify how well e.g. an objective function is minimized

---
## Exploration vs Exploitation

Let us assume that we have learned the **optimal** $Q$ function:

$$ Q^* (s,a)= \underset{\pi}{\text{max}}\:Q^{\pi}(s,a) \ \text{for all} \ s\in\mathcal{S} \ \text{and} \ a \in\mathcal{A}$$

We know that this equation satisfies the Bellman optimality equation as given by:

$$ Q^* (s_t,a_t)=\sum_{s_{t+1}}p(s_{t+1} | s_{t}, a_{t})  \bigg[\Re (s_{t}, a_{t}, s_{t+1}) + \gamma \: \underset{a}{\max} \: Q^* (s_{t+1}, a) \bigg]$$

If such a function is learned it is straightforward to derive and **optimal policy** which does not require exploration

$$\pi^* (s_t)= \underset{a \in \cal A}{argmax} \; Q^{\pi}(s_t, a_t).$$

Unfortunately we first need to learn $Q$ :P

---
## Exploration vs Exploitation

- An agent which always learns from the same experience will learn fast but will never increase its knowledge and performance

- But once the agent has learned enough we do not want it to make sub-optimal decisions anymore since deviating from a greedy policy can cause some loss

- The most popular way of dealing with this dilemma is the $\epsilon$ greedy approach

$$a_{t} = \begin{cases}
  max_{a} Q(s_{t}, a_{t}) & \text{with prob 1-}\epsilon \\
  \text{random action with prob } \epsilon
\end{cases}$$

We anneal $\epsilon$ linearly over time to encourage exploration in the early training stages.

---

## Exploration vs Exploitation

If in practice the $\epsilon$ greedy approach is almost always used, it is important to mention that it treats all negative actions equally.

- We can overcome this with Boltzmann Exploration
$$P(a) = \frac{e^{\frac{Q(a)}{\tau}}}{\sum_{i=1}^{K}e^\frac{Q(i)}{\tau}}$$

which causes a lot of exploration for states where $Q$ values are similar and little exploration in states where $Q$ values are very different.  

---
## On-policy vs Off-policy learning

- We have seen how important it is to learn a policy and how this governs the behavior of an agent

- Policies also define the underlying **RL algorithm** which we use when learning a value function

- Specifically they are of interest when we need to compute a TD-error

$$\delta_t = r_t+\gamma V(s_{t+1})$$

$$\delta_t =  r_{t} + \gamma \: \underset{a\in \mathcal{A}}{\max}\: Q(s_{t+1}, a)$$

---
## On-policy vs Off-policy learning

- The first TD-error defines an **on-policy** RL algorithm since the estimate at $V(s_{t+1})$ will always be defined by the current policy the agent is following

- The second TD-error defines an **off-policy** RL algorithm since the TD-error $r_{t} + \gamma \: \underset{a\in \mathcal{A}}{\max}\: Q(s_{t+1}, a)$ is always greedy because it is defined by the **$\max$** operator. Remember that because of the exploration-exploitation trade-off the agent might not follow this greedy policy in practice

- Overall we can see off-policy algorithms as methods which learn **many** policies whereas on-policy ones only learn **one** policy. Both methods come with their pros and cons and the choice of a particular algorithm depends on the problem at hand.

- This difference starts to play a significant role when neural networks are used.

---

## Q-Learning

$$Q(s_{t}, a_{t}) := Q(s_{t}, a_{t}) + \alpha \big[r_{t} + \gamma \max_{a \in \cal A} Q(s_{t+1}, a) - Q(s_{t}, a_{t})\big]$$

- The most popular RL algorithm
- Based on a variation of the simplest form of TD-Learning
- Learns the $Q$ function in an off-policy learning setting
- (In the limit) Converges to the optimal policy regardless of the exploration strategy used
- Suffers from numerous biases  

---

## SARSA


$$Q(s_{t}, a_{t}) := Q(s_{t}, a_{t}) + \alpha \big[r_{t} + \gamma Q(s_{t+1}, a_{t+1}) - Q(s_{t}, a_{t})\big]$$

- An on-policy variation of Q-Learning
- The TD-error is given by the estimate at $s_{t+1}$ which is based on the current policy
- When function approximators are used it diverges less when compared to Q-Learning

---

## QV-Learning

Jointly learns the state-value function $V$ and the state-action value function $Q$

$$V(s_t) := V(s_t) + \alpha \big[r_{t} + \gamma V(s_{t+1}) - V(s_t))]e_t(s)$$

$$Q(s_t, a_t) :=  Q(s_{t}, a_{t}) + \alpha \big[r_{t} + \gamma V(s_{t+1}) - Q(s_{t}, a_{t})\big] $$

- Learns on-policy
- Learning two value functions might accelerate learning
- Uses the same TD-error to learn two value functions

---

## Actor-Critic Learning

A branch of TD methods which keep the policy **(Actor)**
from a learned value function **(Critic)**

The TD error
$$\delta_t = r_t+\gamma V(s_{t+1})-V(s_t)$$
is used by the critic to evaluate the action which was taken by the actor.

With this error we can strengthen or weaken the selection of an action by modifying the preference of selecting an action

$$p(s_t, a_t):= p(s_t, a_t)+\beta\delta_t$$


---
class: middle

# Deep Reinforcement Learning

---

Coucou

---

class: end-slide, center
count: false

The end.

---

count: false

# References
