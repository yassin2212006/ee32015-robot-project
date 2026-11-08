# Autonomous Disaster-Response Robot (CoppeliaSim)

EE32015 coursework, University of Bath. A six-wheel skid-steer robot with a lift-and-grip arm that collects 10 biohazard containers in a simulated disaster scene and delivers them upright to a safe zone, with no input after pressing Play. I designed the robot and wrote the full Lua control script.

**Result:** in four full runs, all 10 containers were delivered upright inside the green area, with no person hit and no other container knocked over (about 37 simulated minutes per run).

## The task
- 50 m x 50 m disaster scene in CoppeliaSim Edu (Bullet 2.78, 50 ms time step). I could only change the robot, not the scene.
- The robot starts on a three-step pyramid (0.2 m steps) and must deliver 10 containers to a 5 m x 5 m safe zone at (20, 20).
- Hard cases: a container in a closed room behind a sensor-operated sliding door, one next to a 7.8 m building, one inside the loop of a walking man (0.56 m/s), and five stationary people treated as casualties.

## Design
**Robotics and mechatronics**
- Six wheels (0.6 m diameter), skid steering, so it can climb down the 0.2 m steps and turn on the spot.
- Lift-and-grip arm for 0.4 m containers.
- Python models to check step torque and battery size.

**Sensors**
- 18 ray proximity sensors in high and low pairs, giving all-round coverage and a way to tell a lying person (low sensor sees it, high sensor does not) from a wall.
- Casualties are logged once each with their position.
- A flat trigger box under the robot so the door's floor sensors can see it and open the door.
- Python models for stopping distance and sensor bus load.

**Control software (Lua)**
- State machine that picks the nearest container, leaving the building and room containers until last.
- Map-based route planning around building blocks, plus three levels of obstacle avoidance (bypass, avoid, make room).
- Tracking of the walking man: waits until he is clear and never turns or reverses next to him.
- Proportional steering, a 16-spot filling plan for the safe zone, and a door sequence with a timeout.

## Testing (V-model)
Requirements R1 to R12 were written first, split between subsystems, and linked to tests T1 to T9 (steps and grip, obstacle stop, door, building approach, casualty finding, walking man, two deliberate fail tests, and the full mission).

## What went wrong and how I fixed it
- Picking the nearest container sent the robot at the room container first, so I left the room and building containers until last.
- The walking man hit the robot during turns, so I added tracking and waiting rules. A deliberate fail test (T7) shows the safety code is needed.
- The first casualty rule logged 17 false entries, so it now counts only people and logs each one once.

## Limitations (honest notes)
- The robot gets container and people positions from the simulator instead of finding them with its own sensors.
- All full runs start from the same scene, so they show repeatability, not robustness to different starting conditions.
- The walking man came within 0.84 m of the robot, closer than my 2.5 m target. This is not fixed. A fix would predict his path from his speed and direction.

## Next steps
Predict the walking man's path, and replace known positions with perception from the robot's own sensors.

## AI use
Per the coursework rules, I used AI for grammar and spelling correction and for code debugging.

## Repository contents
- `Final_code.pdf`: final Lua control script
- `robot_*.png` and `coppeliasim_scene_hierarchy.png`: screenshots of the robot, its sensor rays and the CoppeliaSim scene hierarchy

Yassin Elhady, BEng Integrated Mechanical & Electrical Engineering, University of Bath
