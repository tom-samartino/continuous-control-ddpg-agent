
# Project 2: Continuous Control

### Introduction

This report describes the reinforcement learning agent designed to solve the 20 agent version of the Unity Reacher environment. Solving the environment entails averaging a score of 30 or more across all agents for at least 100 consecutive episodes. The environment dynamics are described by Markov Descision Process with states, actions, and rewards detailed in the ***Environment*** section. The reinforcement learning algorithm used to solve the environment is described the ***RL Agent*** section. The model architectures that define the Actor/Critic networks are explained in the ***Model Architecture*** section.

### Environment

In this environment, a double-jointed arm can move to target locations. A reward of +0.1 is provided for each step that the agent's hand is in the goal location. Thus, the goal of your agent is to maintain its position at the target location for as many time steps as possible.

The observation space consists of 33 variables corresponding to position, rotation, velocity, and angular velocities of the arm. Each action is a vector with four numbers, corresponding to torque applicable to two joints. Every entry in the action vector should be a number between -1 and 1.

A set of state, action, reward, and next state values together comprise an experience tuple

### RL Agent

A Deep Deterministic Policy Gradient (DDPG) reinforcement learning agent was chosen to solve this environment for its applicability to continous control problems. A DDPG agent features an Actor/Critic framework in which the Actor deep neural network, which approximates the optimal policy, determines the actions through which the agent interacts with the environment and the Critic deep neural network, which approximates the optimal action-value function, judges the quality of the selected actions.

There are two instances of each Actor/Critic network - a local and a target network - for a total of four networks internal to the agent. The target networks serve as the reference for the loss signal and maintain a minimally changing set of weights throughtout the learning process. The local networks perform learning through error backpropagation and update weights more frequently. At a user-specified interval, the target network weights are updated to more closely match the local network to move the target reference in the direction of learning. Initially, the local and target network weights start with the same weights.

At each time step of an episode, the agent performs an action to generate an experience tuple that it adds to a memory buffer. After accumulating enough experiences, the agent randomly samples a batch of experiences from the memory buffer to generate a learning signal by comparing expected and target Q values that are output from forward passes through the local and target networks. The backpropagated loss signal is derived from the difference between these two estimates. Throughout the learning steps, the agent also performs update steps to copy the new local network weights to the target network. The agent converges on the optimal policy that maximizes the expected cumulative reward by repeating this process over many episodes.

The agent's reward dynamics are driven by a set of user-defined hyperparameters:
- BUFFER_SIZE: the maximum number of experience tuples the agent can store
- BATCH_SIZE: the number of experiences the agent sample each learning step
- GAMMA: discount factor of future rewards
- TAU: amount that target networks update to match local networks
- LR_ACTOR: learning rate of the actor network
- LR_CRITIC: learning rate of critic network
- WEIGHT_DECAY: factor that drives how the optimization algorithm changes weights
- LEARN_EVERY: number of time steps for every learning step
- NUM_LEARNS: how many times to learn at each learn step
- UPDATE_EVERY: number of learning steps for every target network update step
- NUM_UPDATES: number of target network updates for every update step

  
### Model Architecture

All four deep neural networks are comprised of two fully connected hidden layers and an output layer. The Actor networks' output layer size corresponds to the dimensionality of the action space. The Critic networks' output layer size is 1, which represents the scalar state-action value. Each network also utilized a one-dimensional batch normalization layer after the first fully connected layer to stabalize the network statistics. All hidden layers used rectified linear unit activation functions. The Actor network applied a hyperbolic tangent function to the output layer logits to ensure that values remained within a +/- 1 domain. The Critic network output layer returned the logits without any applied activation function.
