# OS Laboratory Activity 3
## Multilevel Queue and Real-Time CPU Scheduling

### Student Information
- Name: Princess Dianne Y. Estores
- Course and Section: BSCA - B186
- Instructor: Maria Fe Bahinting
- Date Submitted: November 15, 2026

### I. Introduction
This activity is about simulating CPU scheduling in Python. I implemented Multilevel Queue (MLQ) scheduling, Rate Monotonic Scheduling (RMS), and Earliest Deadline First (EDF), then used Gantt charts and computed results to see how each one works. I also used GitHub to store the code, outputs, and documentation.

### II. Objectives
1. Explain how MLQ, RMS, and EDF scheduling work.
2. Implement MLQ scheduling using Round Robin and FCFS.
3. Implement preemptive RMS and EDF simulations in Python.
4. Generate and interpret Gantt charts.
5. Calculate CPU utilization, waiting time, turnaround time, and deadline compliance.
6. Create and manage a GitHub repository with the source code and documentation.

### III. Scheduling Algorithms
1. **Multilevel Queue Scheduling** – Processes are placed in separate queues, and each queue has its own scheduling algorithm. Queue 1 (interactive) uses Round Robin with a 2 ms quantum and Queue 2 (batch) uses FCFS. Queue 1 has strict priority over Queue 2.
2. **Rate Monotonic Scheduling** – A fixed-priority preemptive algorithm for periodic tasks. The task with the shortest period gets the highest priority.
3. **Earliest Deadline First** – A dynamic-priority preemptive algorithm. The ready task with the earliest absolute deadline runs first.

### IV. Program Implementation
- **mlq.py** – Uses two queues (`deque`). Each 1 ms step, it checks new arrivals, then runs a Queue 1 process (Round Robin, quantum 2 ms) if there is one. Queue 2 only runs when Queue 1 is empty, so a new Queue 1 arrival interrupts it. It records a Gantt chart and computes completion, turnaround, and waiting time.
- **rms.py** – Creates a separate job for every release of each task. Every 1 ms it picks the ready job whose task has the shortest period. It then checks if each job met its deadline.
- **edf.py** – Same job setup as RMS, but every 1 ms it picks the ready job with the earliest absolute deadline (ties go to earlier release, then task ID).

All simulations use 1 ms time steps and ignore context-switching overhead.

### V. Test Data
**MLQ (Quantum = 2 ms)**

| Process | Arrival (ms) | Burst (ms) | Queue |
|---|---|---|---|
| P1 | 0 | 5 | 1 |
| P2 | 1 | 4 | 1 |
| P3 | 0 | 6 | 2 |
| P4 | 2 | 3 | 2 |
| P5 | 3 | 2 | 1 |

**RMS and EDF (simulation time = 20 ms)**

| Task | Execution Time (C) | Period (T) | Deadline (D) |
|---|---|---|---|
| T1 | 1 ms | 4 ms | 4 ms |
| T2 | 1 ms | 5 ms | 5 ms |
| T3 | 2 ms | 10 ms | 10 ms |

### VI. Results and Discussion
**MLQ Gantt chart**
```
| P1 [0-2] | P2 [2-4] | P1 [4-6] | P5 [6-8] | P2 [8-10] | P1 [10-11] | P3 [11-17] | P4 [17-20] |
```

| PID | AT | BT | CT | TAT | WT |
|---|---|---|---|---|---|
| P1 | 0 | 5 | 11 | 11 | 6 |
| P2 | 1 | 4 | 10 | 9 | 5 |
| P3 | 0 | 6 | 17 | 17 | 11 |
| P4 | 2 | 3 | 20 | 18 | 15 |
| P5 | 3 | 2 | 8 | 5 | 3 |

AT = arrival time, BT = burst time, CT = completion time, TAT = turnaround time (CT − AT), WT = waiting time (TAT − BT). For example, P1 arrived at 0 and finished at 11, so its TAT is 11 and its WT is 11 − 5 = 6.

Average Turnaround Time = 12.0 ms, Average Waiting Time = 8.0 ms.
Queue 1 processes (P1, P2, P5) ran first and finished by 11 ms. P3 and P4 in Queue 2 only got the CPU after that, even though P3 arrived at 0 ms. This shows Queue 1's strict priority.

**RMS Gantt chart** (priority: T1 > T2 > T3)
```
| T1 [0-1] | T2 [1-2] | T3 [2-4] | T1 [4-5] | T2 [5-6] | IDLE [6-8] | T1 [8-9] | IDLE [9-10] | T2 [10-11] | T3 [11-12] | T1 [12-13] | T3 [13-14] | IDLE [14-15] | T2 [15-16] | T1 [16-17] | IDLE [17-20] |
```
CPU utilization = 1/4 + 1/5 + 2/10 = 65%. The RMS bound for 3 tasks is about 77.98%, so it passes. All 11 task instances met their deadlines (0 misses).

**EDF Gantt chart**
```
| T1 [0-1] | T2 [1-2] | T3 [2-4] | T1 [4-5] | T2 [5-6] | IDLE [6-8] | T1 [8-9] | IDLE [9-10] | T2 [10-11] | T3 [11-12] | T1 [12-13] | T3 [13-14] | IDLE [14-15] | T2 [15-16] | T1 [16-17] | IDLE [17-20] |
```
CPU utilization is also 65%, which is below 100%, so it passes the EDF test. All 11 task instances met their deadlines (0 misses). The chart is the same as RMS because for this task set the earliest deadlines happen to follow the same order as the shortest periods.

**Deadline compliance (RMS and EDF, same results)**

| Task | Release | Deadline | Completion | Status |
|---|---|---|---|---|
| T1 | 0 | 4 | 1 | MET |
| T2 | 0 | 5 | 2 | MET |
| T3 | 0 | 10 | 4 | MET |
| T1 | 4 | 8 | 5 | MET |
| T2 | 5 | 10 | 6 | MET |
| T1 | 8 | 12 | 9 | MET |
| T2 | 10 | 15 | 11 | MET |
| T3 | 10 | 20 | 14 | MET |
| T1 | 12 | 16 | 13 | MET |
| T2 | 15 | 20 | 16 | MET |
| T1 | 16 | 20 | 17 | MET |

Deadline misses: 0 for both algorithms.

**Output files:** `outputs/mlq_output.txt`, `outputs/rms_output.txt`, `outputs/edf_output.txt`

**Screenshots of program execution**

The screenshots are also in the `screenshots/` folder of this repository. Google Drive copy: [View screenshots](https://drive.google.com/drive/folders/11hbb8rpzbmq9FocXcGX0oe68ouM8fSgV?usp=sharing)

### VII. Algorithm Comparison

| Criteria | MLQ | RMS | EDF |
|---|---|---|---|
| Scheduling basis | Queue priority | Task period | Absolute deadline |
| Priority assignment | Fixed queue priority | Fixed task priority | Dynamic task priority |
| Scheduling type | Depends on queue policy | Preemptive | Preemptive |
| Main application | Process classification | Periodic real-time tasks | Deadline-based real-time tasks |
| Deadline monitoring | Not inherent | Yes | Yes |

**Guide Questions**

37. **What is the main difference between Multilevel Queue and Multilevel Feedback Queue scheduling?**
In MLQ, a process stays in the same queue the whole time. In Multilevel Feedback Queue, processes can move between queues depending on how they behave, like being moved down if they use too much CPU time.

38. **Why does MLQ use different scheduling algorithms for different process queues?**
Different processes need different things. Interactive processes need quick responses, so Round Robin fits. Batch processes just need to finish, so simple FCFS works.

39. **How does RMS determine which task receives the CPU first?**
It gives the CPU to the ready task with the shortest period.

40. **Why is RMS considered a fixed-priority scheduling algorithm?**
Priorities are set once from the task periods and never change while the program runs.

41. **How does EDF determine which task should execute next?**
It chooses the ready task with the earliest absolute deadline (release time + relative deadline).

42. **Why is EDF considered a dynamic-priority scheduling algorithm?**
Priority depends on absolute deadlines, which change every time a new job is released. So the same task can have a high or low priority at different times.

43. **What happens when a newly released task has an earlier deadline than the currently running task under EDF?**
The new task preempts the running one. The old task goes back to the ready queue with its remaining time and continues later.

44. **Why is CPU utilization important in real-time scheduling?**
It shows how much of the CPU the tasks need. If it is too high, the tasks may miss deadlines, so it is used to check if a task set can be scheduled.

45. **Can a task set with CPU utilization below 100% still miss a deadline? Explain the conditions under which this may happen.**
Yes. With RMS, utilization above the bound (about 77.98% for 3 tasks) is not guaranteed to be schedulable, so a deadline can still be missed even below 100%. Overhead like context switching, tasks that can't be preempted, and tasks waiting on shared resources can also cause misses.

46. **Which algorithm would you recommend for periodic real-time tasks, RMS or EDF? Justify your answer.**
I recommend EDF because it can use the CPU up to 100% and still meet deadlines, while RMS is only guaranteed up to about 77.98% for 3 tasks. RMS is simpler and more predictable, but EDF is better for using the CPU fully.

### VIII. Conclusion
In this activity I learned how MLQ, RMS, and EDF schedule tasks and how to read Gantt charts. I also learned how to compute turnaround time, waiting time, and CPU utilization. In the results, MLQ made Queue 2 wait for Queue 1, while RMS and EDF both met every deadline for the given task set. I also practiced using GitHub to organize and submit the code and documentation.
