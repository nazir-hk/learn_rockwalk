# Aerial Rock-and-Walk

This directory contains code for the aerial experiments, including:

- `rnw_ros` and `rnw_msgs` are specific to rock-and-walk.
- `uwb_transceiver` for transmitting mocap information.
- `djiros` and `n3ctrl` for the underlying quadrotor control



__Hardware Requirements__

- DJI N3 Flight Controller
- DJI Manifold 2-G Onboard Computer
- OptiTrack Motion Capture System
- 2 x Nooploop UWB Transmitter
- Logitech F710 Wireless Gamepad
- Custom end-effector for aerial rock-and-walk



__Run as hardware-in-the-loop (HIL) simulation__

1. Connect DJI N3 Autopilot to a PC/Mac with DJI Assistant installed
2. Enter simulation in DJI Assistant
3. `roslaunch rnw_ros sim.launch`



__Run real experiments__

1. Open OptiTrack
2. `roslaunch rnw_ros ground_station_caging_rl.launch` on ground station i.e. your laptop
3. SSH into the aircraft and `roslaunch rnw_ros flight.launch`



__Pre-Flight Checklist__

Make sure UAV odometry is correct

- Fly it using `flight.launch`, hover and moving around

Make sure `ConeState` is correct

- Calibrate the center point and tip point using `roslaunch rnw_ros mocap_calib.launch`
- Calibrate `ground_z` by placing a marker on the ground
- Run `roslaunch rnw_ros check_cone_state.launch`, check the cone state visually
- Check the estimated radius and the true radius, make sure they mactch.



## Documentation



__Control Flow__

1. `rl_agent.zsh` is the node running the RL agent, it takes the observation and sends out action.
2. `caging_rl_node` receives agent action from  `rl_agent.zsh` and translate it into quadrotor `PositionCommand`
3. `uwb_transceiver_node` on the ground running in master mode, sends `PositionCommand` through UWB.
4. `uwb_transceiver_node` on the air running in slave mode, recieves `PositionCommand` through UWB, and send it to the local ROS.
5. `n3ctrl_node` receives `PositionCommand`, performs position control, sends attitude and thrust commands to `djiros_node`



__Playback__

`rosbag record -a` is called by default, the `.bag` files can be retrieved after flight.

Inspect them using:

- `playback.launch` will replay the experiments in RViz.
- PlotJuggler
- MATLAB
