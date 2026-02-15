# Multi-Task General Agent for Classic Control
Progressing through my Electrical Engineering curriculum at my university, I came accross the PID controller as the only accepted standard for classical control. But it's rigid. It requires manual tuning for every new system and fails when dynamics become highly non-linear. Moreover the the accompanying circuitry and signal processing needed for building and tuning PID controllers for different control tasks gave me actual headaches. This was when I came accross Steve Brunton's (absolute LEGEND) lectures on Data-Driven Control and I thought of using machine learning to solve control problems. So I built this project to take the first step toward a General Deep RL Agent: a single brain that replaces traditional controllers by learning the "universal laws" of control across diverse physical systems.
Part 1:
To begin wit I selected four systems representing distinct control challenges from the easily available OpenAI Gymnasium:
CartPole-v1 (Inverted Pendulum): A classic SIMO (Single Input, Multiple Output) problem of maintaining unstable equilibrium.
Acrobot-v1: An underactuated two-link chain. It requires energy management to swing the tip above a boundary.
MountainCar-v0: A Sparse Reward problem. The motor is too weak to climb the hill directly, forcing the agent to master Potential Energy oscillations.
Pendulum-v1: A continuous torque problem. This tests the agent’s ability to provide smooth, analog-like control.
Part 2:
Deep RL Theory & Strategy:
To begin with I chose DQN (Deep Q-Network) over SAC or PPO to force a digital-first approach.To do this I had to quantize continuous torque of the pendulum into discrete bins. This mimics how an ATmega32 or any digital controller interacts with the analog world—through discrete steps. It tests if a quantized "Generalist" can still achieve smooth control.
Architecture:
Multi-Task Embedding: I used a shared 256-neuron MLP trunk. By appending a One-Hot Task ID to the state vector, the brain learns to switch its "physics model" instantly.
Double DQN (DDQN): Standard DQN overestimates $Q$-values. I used the Policy Net to select actions and the Target Net to evaluate them, providing the stability of a low-pass filter in a feedback loop.
Reward Shaping: For MountainCar, I added a velocity-based "potential field" to give the agent a gradient to follow when the environment offered no feedback.
Part 3 The Results:
The agent trained for 2500 episodes with randomized task switching.
| Task | Training Episodes | Final Reward | Control Stability |
| :--- | :---: | :---: | :--- |
| **CartPole** | ~1200 | **500.0** | Perfect (Zero Jitter) |
| **Acrobot** | ~1800 | **-85.2** | High Efficiency |
| **MountainCar**| ~2200 | **-112.5** | Successful Climb |
| **Pendulum** | ~2400 | **-245.1** | Smooth (Minimal Wobble) |
It proves that with the right embeddings and stability filters (DDQN), a neural network can indeed function as a Universal Controller.
