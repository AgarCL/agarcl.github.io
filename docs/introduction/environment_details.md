---
layout: "contents"
title: Environment Details
---

# Environment Details

```{figure} /_static/img/figure_2-1.png
   :alt: AgarCL gif
   :width: 800
```

## Environment Overview

Agar.io is a multiplayer online game in which each player controls one or more circular cells. The game draws an analogy to a Petri dish containing interacting cells, food sources, and viruses. Players aim to grow by absorbing smaller entities, such as static pellets and other players’ cells, while avoiding larger opponents and strategically interacting with viruses, which can either fragment or shield cells depending on their size. Players can also split their cells for tactical reasons, enabling simultaneous control of multiple cells. The game’s rules are simple, but complex dynamics emerge from these interactions. 

### Entities

Three types of entities populate the arena: 
- static pellets (randomly scattered, maintain a fixed size, and grant 1 mass when consumed), 
- player-controlled cells (the primary agents of interaction, can consume pellets, viruses, and smaller cells; when consuming another cell, the player gains the mass of the absorbed cell), 
- viruses (have a mass of 100; depending on the player’s size, a virus can either be absorbed or cause the cell to split). 

### Player Lifecycle & Respawning  

```{figure} /_static/img/figure_3-1.png
   :alt: AgarCL gif
   :width: 800
```

Players start as a small cell of mass 25. If the player's cell(s) is consumed by a larger cell, the player's cell is eaten and is eliminated from the game. The player then respawns with the same initial mass. While this could be perceived as an episodic task due to the repeated nature of the interaction, more successful agents are expected to live much longer and not see such resets. Additionally, the cell that absorbed the player's cell maintains its new mass after the player has respawned, such that actions from a previous episode impact the new one. Currently, our implementation supports the single-player setting; the other players, called bots, follow heuristic-based behavior. We made this choice to focus on the continual aspect of the problem. 

### Environment Dynamics & Mechanics  


```{figure} /_static/img/figure_4-1.png
   :alt: AgarCL gif
   :width: 800
```

The ever-changing nature of the game has many sources. New pellets are generated at every time step while there are fewer than 500 pellets in the arena. Additionally, every cell belonging to an agent loses 0.2% of its mass each second. As cells grow, they move more slowly.  Thus, smaller opponents must continually evade larger players, steer clear of corners, or use viruses for defense. Players can feed viruses until they are large enough in order to split them toward larger players. Typically, a split occurs if a player feeds a virus seven times. The player's field of view also varies according to its mass, as the game needs to depict all of its cells.

## Environment types: screen and grid

### screen: pixel-based observations

We represent the game screen as a four‐dimensional tensor with shape `[N, N, 4, F]`, where `N x N` is the spatial resolution of the game screen (by default, 128). The third dimension corresponds to separate channels for pellets, viruses, enemies, and the agent (including gridlines). `F` is the number of consecutive frames captured per time‐step (by default, F=1). 

### grid: grid-like observations

Observation is divided into two main parts: the global state and the player state. The global state captures information such as the map size, the number of frames in the game, and the number of frames that have
passed. These features provide temporal and spatial context for the agent. In the player state, we focus
solely on the current agent’s information, including its field of view, visible entities in that space, the
agent’s score, and its available actions. The overlap field is critical, as it captures details about nearby
pellets, viruses, and cells (each with associated positions, velocities, and other necessary attributes).


## Action and observation spaces


The action space is hybrid, and an agent can perform two types of actions simultaneously. The agent controls its cells by positioning the cursor on the screen, which determines the direction in which all of its cells move. The range of these continuous actions is between `[-1, 1]` in two dimensions, `(x, y)`. Simultaneously, at each time step, the agent needs to make a discrete choice between splitting, ejecting pellets, or simply moving, with no further discrete action.

The split action divides a cell into two equal parts, each with half the original mass, provided the produced cell has at least a mass of 25, requiring the agent's mass to be at least 50 for it to work. One of the newly split cells is propelled toward the cursor with significant momentum. After splitting, the player must manage multiple cells simultaneously, using the cursor to navigate each cell effectively. The eject action ejects a small blob of mass (called a pellet) from each cell. The pellets are also ejected toward the cursor. They can be consumed by cells or viruses. Every action taken is repeated four times, and the observation received by the agent is generated by the environment after the execution of the selected action(s) four times; we call this value frame skip. 

Finally, AgarCL has stochastic dynamics. Noise sampled from a normal distribution N(0, 1) is added to the continuous actions the agent sends to the environment.


## Initializing Environments

Initializing environments follows the Gymnasium interface, and can be done via the `make()` function:

```{eval-rst}
.. py:currentmodule:: gymnasium
```

```python
import gymnasium as gym
env = gym.make("agario-grid-v0")
```

This function will return an `Env()` for users to interact with. Furthermore, `make()` provides a number of additional arguments for specifying keywords to the environment.

## Configuring Environments

```python
default_config = {
    'ticks_per_step':      4,            # Number of physics ticks simulated for each env.step()
    'arena_size':          550,          # Width and height of the square arena in game units
    'num_pellets':         350,          # Initial number of static food pellets
    'num_viruses':         10,           # Number of virus entities in the arena
    'num_bots':            8,            # Number of bot-controlled cells
    'pellet_regen':        True,         # Whether pellets respawn until num_pellets is reached
    'grid_size':           128,          # Resolution (width/height) of grid observations
    'screen_len':          128,          # Resolution (width/height) of rendered screen observations
    'observe_cells':       False,        # Include own cell positions in the observation
    'observe_others':      False,        # Include other players’ cell positions
    'observe_viruses':     False,        # Include virus positions
    'observe_pellets':     False,        # Include pellet positions
    'obs_type':            "screen",     # Observation mode: "screen" for pixel-based or "grid" for grid-like or "gobigger"
    'reward_type':         diff(),       # Reward function: diff() for change in mass ("diff = reward=mass(t)-mass(t-1)"); alternative: mass() ("mass:reward=mass")
    'render_mode':         "rgb_array",  # Rendering: "human" for live window, "rgb_array" for frame buffers
    'num_agents':          1,            # Number of agents to control (fixed at 1)
    'c_death':             0,            # Death penalty: subtract this value when agent is eaten
    'agent_view':          True,         # Restrict observation to local agent’s field of view
    'add_noise':           True,         # Add random noise to observations for robustness
    'number_steps':        500,          # Maximum number of steps per episode or rollout
    'env_type':            0,            # 0 = episodic, 1 = continuing
    'mode':                0,            # 0 - main game
    'load_env_snapshot':   0,            # Do you want to load a snapshot of the environment?
    'record_video':        True,         # Do you want to record a video of the environment?
    'save_env_snapshot':   True,         # Do you want to save a snapshot of the environment?
}
```