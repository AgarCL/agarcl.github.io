---
layout: "contents"
title: Basic Usage
firstpage:
---

# Basic Usage

```{eval-rst}
.. py:currentmodule:: gymnasium
```

## Self-Play setup

```{figure} /_static/videos/self_play_gif.gif
   :alt: AgarCL gif
   :width: 400
```

In order to play the game yourself or enable rendering in the gym environment, you will need to build the game 
client yourself on a system where OpenGL has been installed. Issue the following commands:


```shell
git submodule update --init --recursive
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make -j 2 client agario
```

This will output an executable named client in the directory agario

```shell
agario/client
```

Use your cursor to control the agent

## Interacting with the Environment

### Initializing a random environment

```python
import gymnasium as gym
import gym_agario

config = {
    'arena_size':     500,      # Size of the arena
    'num_pellets':    500,      # Number of pellets
    'pellet_regen':   True,     # Pellet regeneration
    'num_viruses':    25,       # Number of viruses
    'num_bots':       25,       # Number of bots
    'grid_size':      256,      # Size of the grid
    'obs_type':       "grid",   # Observation type
    'num_agents':     1,        # Number of agents (fixed at 1)
    'c_death':        -100,     # Death penalty
  }


env = gym.make("agario-grid-v0", **config)
env.reset()
env.seed(0)

episode_over = False
while not episode_over:
    action_space = gym.spaces.Box(low=-1, high=1, shape=(2,)) # (x, y) - action space for agent's navigation
    action = (action_space.sample(), np.random.randint(0, 3)) # ((x,y), action)
    observation, reward, terminated, truncated, info = env.step(action)

    episode_over = terminated or truncated

env.close()
```

## Recording and Saving Videos

AgarCL provides functionality to record and save videos of the environment's execution. This is useful for visualizing agent behavior or debugging.

### Enabling Video Recording

To enable video recording, set the `record_video` parameter to `True` in the environment configuration. You can also enable video recording programmatically:

```python
env.enable_video_recorder()
```

### Saving the Video

To save the recorded video, use the `generate_video` method:

```python
env.generate_video('path_to_save_video', 'video_name.avi')
```

This will save the video to the specified path with the given file name.

### Disabling Video Recording

To stop recording, use the `disable_video_recorder` method:

```python
env.disable_video_recorder()
```

### Example Usage

Here is an example of how to record and save a video:

```python
import gymnasium as gym

# Initialize the environment
env = gym.make("agario-screen-v0", render_mode="rgb_array")

# Enable video recording
env.enable_video_recorder()

# Reset the environment
env.reset()

# Perform some steps
for _ in range(100):
   action = [(env.action_space.sample(), env.action_space.sample())]
   observation, reward, terminated, truncated, info = env.step(action)
   if terminated or truncated:
      break

# Save the video
env.generate_video('videos', 'example_run.avi')

# Disable video recording
env.disable_video_recorder()

env.close()
```

## Real-Time Render View

Display the environment in a live GUI window for debugging, demos, and visually tracking your agent’s decisions as they happen.

### Example Usage
An example of how to invoke the window:

```python
import gymnasium as gym

# Initialize the environment
env = gym.make("agario-screen-v0", render_mode="human")

# Reset the environment
env.reset()

# Perform some steps
for _ in range(100):
   action = [(env.action_space.sample(), env.action_space.sample())]
   observation, reward, terminated, truncated, info = env.step(action)

   # Update the on-screen display
   env.render()
   if terminated or truncated:
      break

env.close()
```


This functionality allows you to capture and analyze the agent's performance visually.


## More information

* See the Environment Details page
