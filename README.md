# Student Project
We will train an RL agent to generate arm reaching movements. The arm model considers 24 muscle-tendon units and 16 joints. The RL algorithm is Soft Actor Critic (SAC). Based on the paper Wochner 2020, We will analize the influence of the optimality principles over the arm movement. The project has all the neccesary code to train the RL agents in your local machine.

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
You can check the training plots on [wandb_dashboard](https://wandb.ai/jhoncharaja/student-project-myoarm). It will take around 50 minutes. Then, the training results will be store in **output/agent_name/**.

## Optimality principles
You just trained an agent with the default configuration: reach the target as fast as possible. This typically results in high muscle activation and potentially jerky movements (check the video and plots in the output). To modify the agent’s behavior, go to **configs/myoarm-v0.yaml**. Here, you can enable/disable and scale the influence of different optimality principles during movement generation.

For example, you can start a new training using the following configuration (leave all other parameters as default):
```yaml
# configs/myoarm-v0.yaml
reward:
    op_priority: 100 # scale the influence of the OP. Then try: 200, 300, 400, 500
    optimality_principles:
        op_flag:        # consider only the effort (muscle activation)
            effort: true
            jerk: false
            work: false            
```
```yaml
# configs/agent.yaml
save:
    run_name: test_1 # or any name you prefer :)
wandb:
    tag: ['my-name', 'a-useful-tag']
```


## Evaluate the agent performance
Eventually, you will have tried a variety of combinations. To see how each agent behaves differently, modify the **configs/compare_agents.yaml** file with the following parameters:
```yaml
# configs/compare_agent.yaml
agent_names: # the names of the agents you want to compare
    - test_0
    - test_1
```
To create the figures, run one of the following commands:
```bash
# for mac Mx chip
bash docker/local_training evaluate_agent macMx
# for windows
bash docker/local_training evaluate_agent windows
# for linux
bash docker/local_training evaluate_agent linux
```

Have fun!