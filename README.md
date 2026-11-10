# AI_PROJECT
Comparative Analysis of BFS, Bidirectional BFS and A* Search on the SNAP Facebook Graph

Course: ICE 4107 – Artificial Intelligence Notebook: AI_PROJECT.ipynb (v2)

This project implements and compares four shortest-path search algorithms on the Stanford SNAP Facebook social network. All algorithms are written from scratch on an adjacency list and are benchmarked on the same 200 random source–target pairs for path correctness, nodes expanded, query time, and memory usage.

Algorithms Compared
Algorithm	Idea
BFS	Standard level-by-level search. Serves as the ground truth for path length.
Bidirectional BFS	Searches from both source and target with balanced frontier expansion. Stops with a best_len rule so the result stays optimal.
A* (ALT, original)	A* with a landmark-based heuristic (`h(n) = max_L
A* (ALT, improved)	Same heuristic, but breaks f-ties toward the deeper node ((f, -g, counter, node)) and looks up the target's landmark distances once per query. Included as an ablation.
Dataset
Source: SNAP facebook_combined (ego-networks merged into one graph)
Undirected, unweighted
4,039 nodes, 88,234 edges, one connected component
Average degree 43.69, max degree 1045, density 0.0108
Sampled average path length 3.64, estimated diameter 8

The notebook downloads the file automatically if it is not found in the working directory. Both .txt.gz and .txt are supported.

Requirements
Python 3.9+ (developed on 3.13)
pandas, numpy, matplotlib, networkx

networkx is used only to compute the 2-D layout for the figures. The searches themselves use plain Python (collections, heapq).

bash
pip install pandas numpy matplotlib networkx
How to Run
Open AI_PROJECT.ipynb in Jupyter or Google Colab.
Run all cells from top to bottom (Run All).
Results are saved to snap_facebook_results/ and zipped as facebook_search_project_deliverables.zip. On Colab the zip downloads automatically.

Notes

The dataset download cell uses !wget, which works in Jupyter and Colab on Linux/macOS. On Windows, download the file manually and place it next to the notebook.
Computing the full-graph layout for the search-process figures takes about 1 minute.
Experimental Setup
Setting	Value
Random seed	42
Test pairs	200 random source–target pairs
Timing	Each query timed 5 times, median used
Memory	Peak per-query memory via tracemalloc (measured in a separate loop so it does not affect timing)
Landmarks	16, chosen with a max-coverage pseudo-farthest strategy

Node expansion is counted when a node is popped from the queue/frontier and its neighbours are processed.

Key Results
Algorithm	Avg Path Length	Avg Nodes Expanded	Avg Time (ms)	Avg Peak Memory (KB)	Correctness
BFS	3.665	1911.4	6.314	318.0	100%
Bidirectional BFS	3.665	240.7	1.507	160.4	100%
A* (ALT, original)	3.665	776.1	18.457	224.1	100%
A* (ALT, improved)	3.665	298.7	9.119	178.6	100%

A* preprocessing is a one-time cost of about 0.31 s and 2.25 MB for the landmark tables.

Main Findings
All four algorithms return optimal paths on all 200 pairs (verified by checking path validity and matching BFS length).
Bidirectional BFS is the best overall. It expands fewer nodes than BFS on every pair and is faster on 98% of pairs.
The landmark heuristic is fairly weak on this graph. The mean h(s)/d(s,t) is 0.524, and 23 of 200 pairs have h(s) = 0. The graph is small-world, so many f-ties occur.
Improved A* is a clear gain over the original. It expands 61.5% fewer nodes and is 2.02× faster, simply from better tie-breaking.
A* expands fewer nodes than BFS but is still slower in wall-clock time. Its per-node cost is higher because of heap operations and heuristic evaluation (0.087 ms vs 0.0036 ms per node for BFS).
Output Files

All generated files are in snap_facebook_results/.

Tables (CSV)

File	Content
dataset_statistics.csv	Table A – graph topology statistics
sample_path_results.csv	Table B – first 10 pairs, per algorithm
algorithm_comparison.csv	Table C – aggregate comparison
correctness_validation.csv	Table D – correctness summary
experimental_configuration.csv	Table E – experiment configuration
theoretical_properties.csv	Table F – completeness, optimality, complexity
effort_by_path_length.csv	Table G – effort grouped by path length
heuristic_quality.csv	Table H – ALT heuristic quality
raw_search_experiments.csv	Raw per-query results for all algorithms

Figures (PNG)

File	Content
performance_comparison_charts.png	Bar charts comparing the four algorithms
effort_vs_path_length.png	Nodes expanded and time vs. path length
sample_path_visualization.png	Subgraph around a sample shortest path
explored_nodes_pair*.png	Which nodes each algorithm expanded (full graph)
bfs_search_tree_pair1.png	BFS search tree for sample pair 1

The zoomed-in search-process figures are also saved here.

Notebook Structure
Imports, seed, and configuration
Dataset download and loading
Graph statistics (Table A)
BFS, Bidirectional BFS, A* (ALT) with landmark preprocessing, Improved A*
Path validation framework
Benchmark on 200 pairs, repeated timing, memory measurement
Results tables B–H and pairwise comparison statistics
Charts, path visualization, search-process visualizations, BFS search tree
Packaging all deliverables into a zip
Reproducibility

Fixed seeds (random, numpy, and the layout) and sorted neighbour lists give deterministic results. Timing and memory figures will vary slightly between machines.

Credits
Dataset: J. McAuley and J. Leskovec, Learning to Discover Social Circles in Ego Networks, NIPS 2012 – SNAP
