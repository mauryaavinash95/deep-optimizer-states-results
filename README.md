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

### Increasing data/tensor parallel sizes
We need to plot the foward, backward, and update phase times for 4 subgroup sizes: 100M, 500M, 1B, and 3B for the 7B model, which will run for TP=1,2,4 and DP=1,2,4. Additionally, these plots will have a minor y-axis which will have a dotted red line representing the TFLOPs for each configuration (refer Fig 9 and Fig 10 of HPDC'24 DataStates-LLM paper).