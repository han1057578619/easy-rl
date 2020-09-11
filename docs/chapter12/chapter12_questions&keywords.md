## Chapter12 DDPG

#### 1 关键词

- **DDPG(Deep Deterministic Policy Gradient)：** 在连续控制领域经典的RL算法，是DQN在处理连续动作空间的一个扩充。具体地，从命名就可以看出，Deep是使用了神经网络；Deterministic 表示 DDPG 输出的是一个确定性的动作，可以用于连续动作的一个环境；Policy Gradient 代表的是它用到的是策略网络，并且每个 step 都会更新一次 policy 网络，也就是说它是一个单步更新的 policy 网络。其与DQN都有目标网络和经验回放的技巧，在经验回放部分是一致的，在目标网络的更新有些许不同。

#### 2 思考题

- 请解释随机性策略和确定性策略。

  答：

  - 对于随机性的策略 $\pi_\theta(a_t|s_t)$ ，我们输入某一个状态 s，采取某一个 action 的可能性并不是百分之百，而是有一个概率 P 的，就好像抽奖一样，根据概率随机抽取一个动作。
  - 对于确定性的策略 $\mu_{\theta}(s_t)$ ，其没有概率的影响。当神经网络的参数固定下来了之后，输入同样的state，必然输出同样的 action，这就是确定性的策略。

- 对于连续动作的控制空间和离散动作的控制空间，如果我们都采取使用Policy网络的话，分别应该如何操作？

  答：首先需要说明的是，对于连续的动作控制空间，Q-learning、DQN等算法是没有办法处理的，所以我们需要使用神经网络进行处理，因为其可以既输出概率值 $\pi_\theta(a_t|s_t)$ ，也可以输出确定的策略 $\mu_{\theta}(s_t)$ 。

  - 要输出离散动作的话，最后的output的激活函数使用 softmax 就可以实现。其可以保证输出是的动作概率，而且所有的动作概率加和为 1。
  - 要输出连续的动作的话，可以在输出层这里加一层 tanh激活函数。其作用可以把输出限制到 [-1,1] 之间。我们拿到这个输出后，就可以根据实际动作的一个范围再做一下缩放，然后再输出给环境。比如神经网络输出一个浮点数是 2.8，然后经过 tanh 之后，它就可以被限制在 [-1,1] 之间，它输出 0.99。然后假设说小车的一个速度的那个动作范围是 [-2,2] 之间，那我们就按比例从 [-1,1] 扩放到 [-2,2]，0.99 乘 2，最终输出的就是1.98，作为小车的速度或者说推小车的力输出给环境。
