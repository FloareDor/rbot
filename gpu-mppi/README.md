# gpu mppi

side project while the real stack isn't ready yet.

nav2 has a built-in MPPI controller (`nav2_mppi_controller`) that runs on CPU.
it samples a bunch of random trajectories, scores them, picks the best one, every
control cycle. that's a lot of parallel math that CPUs aren't great at.

nav2 doesn't support GPU MPPI natively, that's the whole reason we're doing this.

goal here: make it run on GPU instead. more rollouts, faster planning,
basically lets our robot react faster.

## why rbot is in here

`rbot/` (cloned from https://github.com/rlxai/rbot) is just a ready-made sim
to test against. it's ROS 2 Jazzy + Gazebo Harmonic, already has Nav2 wired up
with the stock CPU MPPI controller. we're not touching its code, just using it
as a place to launch and drive a robot around while we swap the controller.

## existing GPU MPPI work worth looking at

`mppi_controller_cuda` — CUDA-accelerated MPPI local planner, built on the
MPPI-Generic framework, based on nav2_mppi_controller. has a `jazzy` branch
that's a drop-in controller_server plugin for Nav2 (matches rbot's ROS
distro). benchmarks show it cuts controller_server CPU load roughly in half
vs the stock CPU MPPI. worth starting from/reading before writing anything
from scratch.
https://discourse.openrobotics.org/t/mppi-controller-cuda-cuda-accelerated-mppi-local-planner-for-ros1-ros2/57260

## rough plan

- start with libtorch (pytorch c++) for the rollout sampling + cost scoring,
  since that's basically batched tensor math anyway
- profile it, only drop to raw CUDA if something specific is actually slow
- target is Jetson Orin (onboard compute), so keep an eye on power/memory the
  whole time, not just raw speed
- once it works, wrap it as its own nav2 controller plugin so it can swap in
  wherever the stock MPPI controller is used

## relevant files

- `src/navigation/rlai_navigation/config/nav2_params.yaml` — where the stock
  MPPI controller is configured, this is what we're replacing
- `src/navigation/rlai_navigation/launch/navigation.launch.py` — nav2 launch,
  where the new controller plugin gets wired in once it exists

## status

just getting set up. nothing built yet.
