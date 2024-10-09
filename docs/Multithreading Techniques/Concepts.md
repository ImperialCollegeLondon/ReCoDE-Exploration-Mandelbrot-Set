

As the image resolution increases (e.g., 1000 $\times$ 1000 pixels results in 1,000,000 iterations), the amount of computation becomes significant. The use of nested loops means that for each pixel, there are potentially up to `MAX_ITER` iterations, leading to a time complexity of \( O(X \times Y \times \texttt{MAX_ITER}) \). The nested loops for calculating the Mandelbrot set are run sequentially. Each pixel must be calculated before the next one starts, meaning that the larger the image, the longer it takes to compute.

In terms of **I/O** operations, we have seen that the time required to write the output image to disk is negligible and is not a bottleneck (at least at the scale we are computing). This suggests that one of the main limitations lies in the operations inside the nested loops.

The line `int num_cpus = std::thread::hardware_concurrency();` showed the number of concurrent threads supported by the hardware (12 CPU cores, which is standard in modern workstations). However, since the code runs in a single thread, during the execution of the nested loops, only one CPU core is fully utilized. The remaining cores remain idle unless other processes are running on the system. This leads to inefficient use of CPU resources, which could otherwise be leveraged by executing multiple threads simultaneously to speed up the overall computation.

Regarding **memory access**, the nested loops can also be inefficient. The outer loop iterates over rows (`y`), while the inner loop iterates over columns (`x`). This pattern can lead to cache misses if data is not stored optimally in memory. Cache misses occur when the CPU searches for data in its cache but cannot find it, forcing it to retrieve data from the slower main memory (RAM). If the program utilized multiple threads, data access could be better arranged to make use of the cache, potentially improving performance.


<p align="center">
  <img src="/docs/Figures/Single_thread_diagram.png" alt="Single-threaded execution" width="60%">
  <br>
  <em>Figure: Diagram showing a single-threaded execution.</em>
</p>
