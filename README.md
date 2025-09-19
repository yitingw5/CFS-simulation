# Task Scheduler Simulation

- If anybody would like to see my note, [here is](./task-scheduler-note.pdf) 

## Build and Run

### Prerequisites
- C++ compiler, CMake
- Python3

### Build
```
# 1. clone repo
git clone <url>
cd <repo>

# 2. Create python virtual env, and activate it
python3 -m venv myvenv
source myvenv/bin/activate
# you'll see (myvenv) username@Macbook .... % (if you are mac user as well)

# 3. Install python dependencies
pip install matplotlib numpy

# 4. Compile C++ project
mkdir -p build 
cd build
cmake ..
make

# 5. Run scheduler simulation
./cfs_scheduler

# 6. Generate visualization plots
cd .. 
python3 plot.py
```

## Overview
I built a CFS simulator that mimics how Linux schedules tasks fairly across different priority levels. The simulation starts by loading all tasks - both CPU-bound and I/O-bound into a min-heap queue sorted by their virtual runtime (vruntime), with each task getting a random non-zero initial vruntime to simulate a real-world scenario where tasks don't all start at once. The interesting thing happens through a simple weight calculation: tasks with higher priority (lower priority numbers) get bigger weights via `weight = 1024 / (priority + 1)`, which makes their vruntime grow slower that they'll get picked **more often** by the scheduler. When running, the scheduler always grabs the task with the smallest vruntime from the queue. CPU-bound tasks execute for 1ms time slices, and I/O-bound tasks first sleep to simulate waiting for I/O, get penalized with extra vruntime for making the CPU wait, then run before going back in the queue. After each execution, we update the task's vruntime using the formula `vruntime += (time_executed * 1024) / weight`, and toss it back in the queue if it still has work to do. This creates a self-balancing system where high-priority tasks naturally get more CPU time without any complex rules, just ensure every task gets its **fair share** based on its weight. The goal of this project is watching how tasks with different priorities compete for CPU time, with the vruntime mechanism preventing starvation while maintaining proportional fairness.
