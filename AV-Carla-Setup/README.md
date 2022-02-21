# Autonomous Vehicle Case Study in Carla Simulation

This repo has the code for the Autonomous Vehicle case study setup in Carla simulation. Also, we have developed a data generation framework to generate evidence for the AV operating under different operating conditions. A sample of the anomaly detector's efficiency is available [here](https://github.com/Shreyasramakrishna90/AV-Carla-Setup/blob/main/sample-evidence/detector_efficiency.csv). In the current work, these stats are not used. However, in the future we want to use these statistics for computing the confidence scores for the assurance case. The AV system model is shown below.

<p align="center">
  <img src="https://github.com/Shreyasramakrishna90/AV-Assurance/blob/main/images/sys-model.png" />
</p>

## Different Testing Scenes 

<p align="center">
  <img src="https://github.com/Shreyasramakrishna90/AV-Assurance/blob/main/images/cloudy.gif" />
  <img src="https://github.com/Shreyasramakrishna90/AV-Assurance/blob/main/images/evening.gif" />
  <img src="https://github.com/Shreyasramakrishna90/AV-Assurance/blob/main/images/cloudy-rainy.gif" />
</p>

(Left) Cloudy Scene in an intersection. The AV also has a camera fault (camera occlusion), which makes its driving slightly suceptible. (Center) Sunset scene in a curvy road. There is another vehicle right in front of the AV. (Right) Cloudy Scene with slight rain.  

## Downloads

***Manual Downloads***

1. You will also need to install CARLA 0.9.9, along with the additional maps.
Download CARLA 0.9.9 from [here](https://github.com/carla-simulator/carla/releases/tag/0.9.9) for more instructions. (Our setup works with CARLA 0.9.9 version. Using another version of the simulator will result in a version and API mismatch error.)

2. Download the LEC weights from [here](https://vanderbilt365-my.sharepoint.com/:u:/g/personal/shreyas_ramakrishna_vanderbilt_edu/Eaq1ptU-YJJPrqmEYUK_dx8Bad2KqhVQZJkKwngWnuMWRA?e=U3dtyf). The LEC model architecture was taken from [Learning By Cheating](https://github.com/bradyz/2020_CARLA_challenge)

Save the model.ckpt file to carla-challange/carla_project folder. 

3. Download the trained B-VAE assurance monitor weights from [here](https://vanderbilt365-my.sharepoint.com/:u:/g/personal/shreyas_ramakrishna_vanderbilt_edu/EbB6W8s1XgFJg0Uv762w3v0BuAi7pOrYPZOnbmhHBlEKVQ?e=bOy4Rm)

Unzip and save the weights to carla-challange/leaderboard/team_code/detector_code/ood_detector_weights

***Automated Downloads (Preferred)***

Enter into this repo and execute this script ```./downloads.sh``` to download these three requirements automatically into the required folders.

## Setup Virtual Environment

To run the scene generation workflow with CARLA, clone this repo.

```bash
git clone https://github.com/Shreyasramakrishna90/AV-Assurance.git
```
Then, create a conda environment to run the experiments. 

```
To run this setup first create a virtual environment with python 3.7
conda create -n carla-sampling python=3.7
conda activate carla-sampling
python3 -m pip install -r requirements.txt
```

# Running the Carla setup 

***Create Folders for Docker Volumes***

Create three folders named ```routes```, ```simulation-data``` and ```images``` inside this directory. These folders are the data volumes for the carla client docker. Run the following

```
mkdir routes               #stores the scene information.
mkdir simulation-data      #stores the sensor information
mkdir images               #stores images if chosen by the user
```
Alternately, enter into this repo and execute this script ```./make_volume_folders.sh``` to set up these empty folders.

***Launch Simulation & Sampler***

Next get into the path of this repo and execute the following script inside the carla-challange folder. This script has a few variables that need to be set before execution. 

1. PROJECT_PATH: Set this to the location of this repo.
1. CARLA_ROOT: Set this to the location of the CARLA_0.9.9 simulator folder. 
2. PORT: The simulator port (default:3000)
3. HAS_DISPLAY: 1 = display simulation run, 2 = headless mode (no display)

```
cd carla-challange
./run_agent.sh n    #where n is the number of scenes to be generated. If not selected, 2 scenes will be generated by default.
```
This script launches both the simulator and the carla client. The simulation data is stored in the routes, simulation-data, and images folders.

This should start running the carla setup with the default random sampler. Different samplers and scene variables can be seected from [scene_description.yml](carla-challange/sdl/scene/scene_description.yml)

# Scene Generation & Samplers
We use a scenario description DSML written in [textX](https://textx.github.io/textX/stable/) to generate different temporal scene parameters (weather parameters, time-of-day,traffic density), spatial scene parameters (road segments) and agent sensor faults. These variables and samplers can be selected using the scene specification file ***carla-challange/sdl/scene/scene_description.yml***

# Samplers

One of the following samplers can be used to run the scene generator. 

1. **Manual Sampler** - A sampler in which the user can manually specify the values for the scene variables.
2. **Random Sampler** - A sampler in which the scene variables are sampled uniformly at random from their respective distributions.
3. **Grid Sampler** - A sampler that exhaustively samples all the combinations of the scene variables in a given grid.
4. **Halton Samppler** - A pseudo-random sampler that samples the search space using co-prime as its bases.

## References

The setup in this work is built using the following work.

1. The setup is taken from our previous work "Risk Aware Scene Sampling for Autonomous Cyber-Physical System" [[paper]](https://scholar.google.com/citations?view_op=view_citation&hl=en&user=M6Yu9GEAAAAJ&citation_for_view=M6Yu9GEAAAAJ:hqOjcs7Dif8C) [[GitHub]](https://github.com/Shreyasramakrishna90/Risk-Aware-Scene-Generation/blob/main/README.md)

2. ReSonAte: A Runtime Risk Assessment Framework for Autonomous Systems [paper](https://arxiv.org/abs/2102.09419) - This is our previous work which introduced the ReSonAte risk estimation framework. We use this setup for computing the ReSonAte score in this work. [GitHub](https://github.com/scope-lab-vu/Resonate)

3. Learning By Cheating [paper](https://arxiv.org/abs/1912.12294) - The Learning-Enabled Component (LEC) controller for the AV is borrowed from this work. We also used their autopilot controller to generate scenes in the training and calibration phases. [GitHub](https://github.com/bradyz/2020_CARLA_challenge) 

