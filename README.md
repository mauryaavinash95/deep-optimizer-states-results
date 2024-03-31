# deep-optimizer-states-results
Contains results for Deep Optimizer States project

## General plotting guidelines
1. Prefer using the seaborn library in Python to create plots
2. Keep the aspect ratio of images fixed: (5, 3) using `figsize`
3. Generate plots in both eps and png formats
4. Prefer using jupyter notebooks (keep separate notebook per author) which can be kept in the main directory of this repo.
5. Some nsys log files might be large, in which case, they might be uploaded to git LFS or Google Drive.
6. Create a separate folder called plots which will contain all png and eps files.

## Motivation plots
### Increasing subgroup sizes
Plot forward, backward, and update phase times for increasing subgroup sizes for the 7B model (logs in act-output-7B). This would be a horizontally stacked bar-chart (see figure 4 of the HPDC'24 DataStates-LLM paper in this repository for reference- however unlike figure 4, we do not need all bars to touch 100% for plotting this graph). 

The filenames is structured as follows: `log-{model_size}-tp{tensor_parallel}-dp{data_parallel}-l{num_layers}-h{hidden_dim}-a{attn_heads}-gbs{global_batch_size}-mbs{micro_batch_size}-ratio{percent_offloaded_to_cpu}-subg{subgroup_size}-prefetch{our_approach_on_off}.log`

In each file, we have 5 lines of this format: `fwd: 635.86 | bwd: 2515.72 | bwd_inner: 1948.50 | bwd_allreduce: 567.16 | step: 4019.41`. We need to skip the first line (because it contains initialization overheads), and consider the third or fourth such line to plot the `fwd`, `bwd`, and `step` times for an increasing subgroup size.


### Memory consumption
(logs in act-output-13B-monitor-mem-single-GPU)
#### Step-1 (finding training phase timestamps in NVTX logs)
This contains an nsys file, so we need to generate an SQL file from the nsys report using `nsys stats --force-export=true filename.nsys-rep`. This should generate `filename.sqlite`. 
First, we need to find the global unix timestamp for each NVTX event. So run 
`UPDATE NVTX_EVENTS SET start=start+(SELECT utcEpochNs FROM TARGET_INFO_SESSION_START_TIME), end=end+(SELECT utcEpochNs FROM TARGET_INFO_SESSION_START_TIME);`

Once we have the global unix timestamp for each NVTX event, we can find the forward, backward, and update timestamps of training iterations using `SELECT * FROM NVTX_EVENTS WHERE text="DeepSpeedEngine.forward" ORDER BY end ASC;`; `SELECT * FROM NVTX_EVENTS WHERE text="DeepSpeedEngine.backward" ORDER BY end ASC;`; and `SELECT * FROM NVTX_EVENTS WHERE text="DeepSpeedEngine._take_model_step" ORDER BY end ASC; `

From each of the forward, backward, and update steps, select the third or fourth event (assuming the first ones are warmup for the model) and record the `start` and `end` time (this should be global UTC timesteps).

#### Step-2 (finding memory consumption corresponding to the training phases)
Then goto the CSV file "*-monitor.csv". Here, you can find in last column the amount of memory free in bytes. The objective is to take the timestamps you got from step-1 and plot the amount of free memory in different phases (example plot on slack)

### Increasing data/tensor parallel sizes
We need to plot the foward, backward, and update phase times for 4 subgroup sizes: 100M, 500M, 1B, and 3B for the 7B model, which will run for TP=1,2,4 and DP=1,2,4. Additionally, these plots will have a minor y-axis which will have a dotted red line representing the TFLOPs for each configuration (refer Fig 9 and Fig 10 of HPDC'24 DataStates-LLM paper).