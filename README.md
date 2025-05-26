# Student Project
Generating 3D-reaching movements while accounting for muscle dynamics nonlinearities and model redundancy has been a significant challenge. While reinforcement learning combined with the minimum variability principle has tackled this issue for fast movements, it is not well-suited for rehabilitation applications involving impaired patients, who typically exhibit moderate-to-slow movements. **In this student project, we will explore whether slower movements can be induced by incentivating energy-efficient or highly smooth movements**. For this purpose, we will employ MuJoCo physics engine to simulate the muscle dynamics and a human arm model that includes 24 muscle-tendon units and 16 joints. The muscle commands are influenced by both signal-dependent and signal-independent noise.

<p align="center">
    <img src="baseline.gif" width="300"/>
</p>

> **Note:** In case you want additional information:
> - Minimum variance model: states that CNS seek to minimize end-point variability while dealing with noise proportional to motor command amplitude [[link]](https://www.nature.com/articles/29528)
> - The RL algorithm is Soft Actor-Critic (SAC) with automatic temperature adjustment [[link]](https://arxiv.org/abs/1812.05905)
> - MyoSuite repository for the Myoarm [[link]](https://github.com/MyoHub/myo_sim)
> - Fast reaching movements with RL [[link]](https://www.nature.com/articles/s41598-021-93760-1)

<!--
We will use a human arm model that includes 24 muscle-tendon units and 16 joints

To address this limitation, we introduce an energy consumption penalty into the reward function and employ a synergetic action representation to reduce the control dimensionality. Our findings indicate that incentivizing energy-efficient movements reduces peak velocity to three-fourths of the maximum velocity, and that a purely synergetic controller can generate precise and stable movements. We expect that these insights will contribute to more accurate models for predicting movement dynamics in impaired individuals.


Hi all, we will study the influence of the optimality principles (i.e., neuro-control effort, jerk and work) over the position and velocity profiles in point-to-point reaching movements. We will use a human arm model that considers 24 muscle-tendon units and 16 joints

We will try to answer if we can induce slower movements by given more priority to the optimality principles.

The reward fucntion considers
r = r_reaching + r_effort + r_jerk + r_work


We will analyse the influence of the optimality principles (i.e., neuro-control effort, jerk and work) over the arm movement. The project has all the neccesary code to train the RL agents in your local machine.

You just trained an agent with the default configuration: reach the target as fast as possible. This typically results in high muscle activation and potentially jerky movements (check the video and plots in the output). 
-->



<!-- Based on the paper Wochner 2020, We will analize the influence of the optimality principles over the arm movement. The project has all the neccesary code to train the RL agents in your local machine.-->
<!-- show myoarm: after training evaluation-->


## Workspace configuration
We will use docker containers to train/evaluate/compare the RL agents.
1. Install docker desktop (See [docker website](https://docs.docker.com/desktop/)).
2. Clone the repository
```bash
git clone https://github.com/JhonPool4/student_project_myoarm.git
```
3. Create the docker image with all the dependencies
```bash
cd student_project_myoarm
bash docker/local_training init
```
4. Verify that the docker image is created
```bash
docker image list
# Expected prompt:
# myoarm-optimality-principles
# jhonpool4/student-project-myoarm
```

## Train an RL agent
To start the training, you need to run one of the following commands:
```bash
# for mac Mx chip
bash docker/local_training start macMx
# for windows
bash docker/local_training start windows
# for linux
bash docker/local_training start linux
```
You can check the training plots on this [wandb_dashboard](https://wandb.ai/jhoncharaja/student_project_myoarm). It will take around 50 minutes. Then, the training results will be store in **output/student_project_myoarm/run_name**.

## Optimality principles
The default configuration trains a reinforcement learning agent focused solely on minimizing movement time, which results in high velocities and muscle activation. Building on this, we will incorporate additional objetives, such as minimization of muscle energy comsuption (effort) or maximization of movement smoothness (jerk). In **configs/myoarm-v0.yaml**, you can enable and scale the influence of these optimality principles.

For example, you can start a new training using the following configuration (leave all other parameters as default):
```yaml
# configs/myoarm-v0.yaml
reward:
    op_priority: 100 # scale the influence of the OP. Then try: 200, 300, 400, ...
    optimality_principles:
        op_flag:        
            effort: true # enable for minimizing energy consumption
            jerk: false # enable for maximization of smoothness
```
```yaml
# configs/agent.yaml
save:
    run_name: test_1 # or any name you prefer :)
wandb:
    tag: ['my-name', 'a-useful-tag']
```

## Compare agent behavior
Eventually, you will have tried a variety of combinations. To see how each agent behaves differently, modify the **configs/compare_agents.yaml** file with the following parameters:
```yaml
# configs/compare_agent.yaml
agent_names: # the names of the agents you want to compare
    - test_0
    - test_1
    - test_2
```
To create the figures, run one of the following commands:
```bash
# for mac Mx chip
bash docker/local_training compare_agents macMx
# for windows
bash docker/local_training compare_agents windows
# for linux
bash docker/local_training compare_agents linux
```

Have fun!