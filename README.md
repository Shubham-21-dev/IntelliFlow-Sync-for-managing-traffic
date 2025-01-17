# Smart India Hackathon 2020 
Our cities are expanding and most of the people have to travel more than 2 hours daily to commute between their place of work and stay. These things often lead to work-life imbalance, more stress, impact work performance, etc. We are working for a software solution for smart management of traffic which leads to better travel plans, less stress due to driving, and optimized travel time.

# Acknowledgements
Thanks Andrea Vidali. Your repo really helped us in understanding of traffic system and application of reinforcement learning in our project.

# Solution
# Deep Sync with AI 
## *Sync the flow of city network traffic*
App uses deep reinforcement learning to come up with policy function to identify pairs or groups of traffic signals which can be coordinated to make efficient decisions about when to release traffic, in which direction and for how much time..We used image recognition to detect vehicles positive and velocity.

## *Implementation with Deep Reinforcement Learning*
It will act as a framework where a deep Q-Learning Reinforcement Learning agent selects the correct traffic light phase at an intersection to maximize traffic efficiency.

## *Features*
Vehicles detection enables survelliance and tracking
Coordinate with other transportation services
Create Green Corridors by specifying particular path as high priority(by assigning high reward points for 'Ambulance/Emergency Vehicles')
Cluster of 5 intersections act as base unit and Agent.sync() function is called recursively along X and Y axis for each intersection

## Getting Started

These instructions will get youp and running this project on your local machine for development and testing purposes. You need to follow these steps to be able to run the algorithm starting from scratch. A computer with an NVIDIA GPU is strongly recommended.

1. Download Anaconda ([official site](https://www.anaconda.com/distribution/#download-section)) and install.
2. Download SUMO ([official site](https://www.dlr.de/ts/en/desktopdefault.aspx/tabid-9883/16931_read-41000/)) and install.
3. Follow [this](https://towardsdatascience.com/tensorflow-gpu-installation-made-easy-use-conda-instead-of-pip-52e5249374bc) short guide to install tensorflow-gpu correctly and problem-free. Basically, the guide tells you to open Anaconda Prompt, or any terminal, and type the following commands:
```
conda create --name tf_gpu
activate tf_gpu
conda install tensorflow-gpu
```

I've used the following software versions: Python 3.7, SUMO traffic simulator 1.2.0, tensorflow 2.0

## Running the algorithm

- Now you are ready to run the algorithm. To do so, you need to run the file **training_main.py** by executing the following command on the Anaconda prompt or terminal and the agent will start the training:
```
python training_main.py
```

You don't need to open any SUMO software. Everything is loaded and run in the background. If you want to see the training process, set the parameter *gui* to "true" contained in the file **training_settings.ini**. Keep in mind that viewing the simulation is very slow compared to the background training and you also need to close SUMO-GUI every time an episode ends, which is not practical.

The file **training_settings.ini** contains all the different parameters used by the agent in the simulation. The default parameters are not that optimized, so a bit of testing will likely increase the current performance of the agent.

When the training ends, the results will be stored in "*./model/model_x/*" where *x* is an increasing integer starting from 1, generated automatically. Results will include some graphs, the data used to generate the graphs, the trained neural network, and a copy of the ini file where the agent settings are.

Now you can finally test the trained agent. To do so, you will run the file **testing_main.py**. The test involves a single episode of simulation, and the results of the test will be stored in "*./model/model_x/test/*" where *x* is the number of the model that you specified to test. The number of the model to test and other useful parameters are contained in the file **testing_settings.ini**.

**Training time:** ~27 seconds per episode, 45min for 100 episodes, on a computer equipped with i7-3770K, 8GB RAM, NVIDIA GTX 970, SSD.

## The code structure

**training_main.py** is the main file. It handles the main loop that starts an episode on every iteration. It saves the network and 3 graphs: negative reward, cumulative wait time, and average queues. 

The algorithm is divided into classes that handle different parts of the training.
- The **Model** class is used to define everything about the DNN and it also contains some functions used to train the network and predict the outputs in the **model.py** file, two different **model** classes are defined: for training and testing.
- The **Memory** class handle the memorization for the experience replay mechanism. A function is used to add a sample into the memory, while another function retrieves a batch of samples from the memory.
- The **Simulation** class handles the simulation. In particular, the function *run()* allows the simulation of one episode. Also, some other functions are used during *run()* to interact with SUMO, for example retrieving the state of the environment (*get_state*), set the next green light phase (*_set_green_phase*) or preprocess the data to train the neural network (*_replay*). There are two files that contain a slightly different **Simulation** class: **training_simulation.py** and **testing_simulation.py**. Which one is loaded depends if we are doing the training phase or the testing phase.
- The **TrafficGenerator** class contains the function dedicated to defining the route of every vehicle in one episode. The file created is *episode_routes.rou.xml* which is placed in the "intersection" folder.
- The **Visualization** class is used for plotting data.
- In the **utils.py** file contains some directory-related functions, such as automatically handling the creations of new model versions and the loading of existing models for the testing.
- The **movementdetection.py** file contains the code to detect vehicle presence, for semantic segmentation, and creating velocity and position matrices.

In the "intersection" folder there is a file called *environment.net.xml* which defines the structure of the environment, and it was created using SUMO NetEdit. The other file *sumo_config.sumocfg* it is a linker between the environment file and the route file. 

## The settings explained

The settings used during the training and contained in the file **training_settings.ini** are the following:
- **gui**: enable or disable the SUMO interface during the simulation.
- **total_episodes**: the number of episodes that are going to be run.
- **max_steps**: the duration of each episode, with 1 step = 1 second (default duration in SUMO).
- **n_cars_generated**: the number of cars that are generated during a single episode.
- **green_duration**: the duration in seconds of each green phase.
- **yellow_duration**: the duration in seconds of each yellow phase.
- **num_layers**: the number of hidden layers in the neural network.
- **width_layers**: the number of neurons per layer in the neural network.
- **batch_size**: the number of samples retrieved from the memory for each training iteration.
- **training_epochs**: the number of training iterations executed at the end of each episode.
- **learning_rate**: the learning rate defined for the neural network.
- **memory_size_min**: the min number of samples needed into the memory to enable the training of the neural network.
- **memory_size_max**: the max number of samples that the memory can contain.
- **num_states**: the size of the state of the env from the agent perspective (a change here requires also algorithm changes).
- **num_actions**: the number of possible actions (a change here requires also algorithm changes).
- **gamma**: the gamma parameter of the Bellman equation.
- **models_path_name**: the name of the folder that will contain the model versions and so the results. Useful to change when you want to group up some models specifying a recognizable name.
- **sumocfg_file_name**: the name of the .sumocfg file inside the *intersection* folder.

The settings used during the testing and contained in the file **testing_settings.ini** are the following (some of them have to be the same of the ones used in the relative training):
- **gui**: enable or disable the SUMO interface during the simulation.
- **max_steps**: the duration of the episode, with 1 step = 1 second (default duration in SUMO).
- **n_cars_generated**: the number of cars generated during the test episode.
- **episode_seed**: the random seed used for car generation, that should be a seed not used during training.
- **green_duration**: the duration in seconds of each green phase.
- **yellow_duration**: the duration in seconds of each yellow phase.
- **num_states**: the size of the state of the env from the agent perspective (same as training).
- **num_actions**: the number of possible actions (same as training).
- **models_path_name**: The name of the folder where to search for the specified model version to load.
- **sumocfg_file_name**: the name of the .sumocfg file inside the *intersection* folder.
- **model_to_test**: the version of the model to load for the test. 

## *New changes* - 
1. Each training result is stored in a folder structure, with each result being numbered with an increasing integer.
2. Test Mode: test the model versions by running a test episode with comparable results.
3. Enabled a dynamic creation of model by specifying the width and the depth of the feedforward neural network for each training.
4. The code for the neural network is written using Keras and Tensorflow 2.0.
5. Added a settings file (.ini) for both training and testing.
6. Added a minimum number of samples required into the memory to begin training.
7. Image Recognition for vehicles has been implemneted.

## The Deep Q-Learning Agent

**Framework**: Q-Learning with deep neural network.

**Context**: traffic signal control of 1 intersection.

**Environment**: a 4-way intersection with 4 incoming lanes and 4 outgoing lanes per arm. Each arm is 750 meters long. Each incoming lane defines the possible directions that a car can follow: left-most lane dedicated to left-turn only; right-most lane dedicated to right-turn and straight; two middle lanes dedicated to only going straight. The layout of the traffic light system is as follows: the left-most lane has a dedicated traffic-light, while the other three lanes share the same traffic light.

**Traffic generation**: For every episode, 1000 cars are created. The car arrival timing is defined according to a Weibull distribution with shape 2 (a fast increase of arrival until peak just before the mid-episode, then slow decreasing). 75% of vehicles spawned will go straight, 25% will turn left or right. Every vehicle has the same probability to be spawned at the beginning of every arm. On every episode the cars are generated randomly so is not possible to have two equivalent episodes in term of vehicle's arrival layout.

**How Agent make decisions??**:
- **State**: discretization of oncoming lanes into presence cells, which identify the presence or absence of at least 1 vehicle inside them. There are 20 cells per arm. 10 of them are placed along the left-most lane while the other 10 are placed in the other three lanes. 80 cells in the whole intersection.
- **Action**: choice of the traffic light phase from 4 possible predetermined phases, which are described below. Every phase has a duration of 10 seconds. When the phase changes, a yellow phase of 4 seconds is activated.
  - North-South Advance: green for lanes in the north and south arm dedicated to turn right or go straight.
  - North-South Left Advance: green for lanes in the north and south arm dedicated to turn left. 
  - East-West Advance: green for lanes in the east and west arm dedicated to turn right or go straight.
  - East-West Left Advance: green for lanes in the east and west arm dedicated to turn left. 
- **Reward**: change in *cumulative waiting time* between actions, where the waiting time of a car is the number of seconds spent with speed=0 since the spawn; *cumulative* means that every waiting time of every car located in an incoming lane is summed. When a car leaves an oncoming lane (i.e. crossed the intersection), its waiting time is not considered anymore, therefore it is a positive reward for the agent.
- **Learning mechanism**: the agent make use of the Q-learning equation *Q(s,a) = reward + gamma • max Q'(s',a')* to update the action values and a deep neural network to learn the state-action function. The neural network is fully connected with 80 neurons as input (the state), 5 hidden layers of 400 neurons each, and the output layers with 4 neurons representing the 4 possible actions. Also, a mechanism of experience replay is implemented: the experience of the agent is stored in a memory and, at the end of each episode, multiple batches of randomized samples are extracted from the memory and used to train the neural network once the action values have been updated with the Q-learning equation.

# Future Enhancements
Training the NN over network consisting of multiple intersections linked together.
- Time constraints have prevented us from being able to analyze our approach when multiple intersections are present. It would be interesting to see if the same state-action pairs (Q values) would be learned or if the presence of multiple nodes would cause these to change. Permitted more time we could expand this simulation scope and possibly consider implementing other state values apart from just the vehicle position and velocity matrices, for example, allowing an intersection to see the states of its neighbors. Such increases in state-space could prove beneficial to improving traffic flow, but can also greatly increase learning time.

- to deploy this model as API in working application
- develop web app for Admin/ traffic control room
- use deep computer vision to better grasp/understand the environment around,
- feature engineering (what other features might be affecting traffic in the network)
- bring this project from simulation to real world
