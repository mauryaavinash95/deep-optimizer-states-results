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
1. **Increasing subgroup sizes**: Plot forward, backward, and update phase times for increasing subgroup sizes for the 7B model (logs in act-output-7B). This would be a horizontally stacked bar-chart (see figure 4 of the HPDC'24 DataStates-LLM paper in this repository for reference- however unlike figure 4, we do not need all bars to touch 100% for plotting this graph)