
In this section, we will show how to output our image in `.vtk` format, which will allow us to access powerful options for scientific visualization. For this purpose, we will use our multithreaded version. Due to the modularity of `C++`, the only function we need to modify is `saveToPPM`, which will be replaced by `save_vtk`.

An important reference that I recommend, in addition to the useful information available in the Kitware community forums, is the works by ([William](#William2006)), the founders of Kitware, where you can learn more about the graphics pipeline.

**References**

1. <a id="William2006"></a>William J.. Schroeder, Ken Martin, and Bill Lorensen. The Visualization Toolkit: An Object-Oriented Approach to 3D Graphics. Kitware., 2006.






## Saving to VTK format function in 2D

In the multithreaded version of our code, the `save_vtk` function writes the Mandelbrot set data into a VTK file for visualization. The header includes the metadata such as the file version, description, and format (ASCII in this case). It then defines the data structure (`STRUCTURED_POINTS`), specifying the dimensions of the grid. After that, this function writes the scalar field for the points (`iterations` vector). This enables tools like ParaView to process the image.

```cpp
void save_vtk(const std::vector<int>& iterations, const std::string& filename) {
    std::ofstream ofs(filename);

    ofs << "# vtk DataFile Version 3.0\n";
    ofs << "Mandelbrot Set\n";
    ofs << "ASCII\n";
    ofs << "DATASET STRUCTURED_POINTS\n";
    ofs << "DIMENSIONS " << X << " " << Y << " 1\n";
    ofs << "ORIGIN 0 0 0\n";
    ofs << "SPACING 1 1 1\n";
    ofs << "POINT_DATA " << X * Y << "\n";
    ofs << "SCALARS mandelbrot_iterations int 1\n";
    ofs << "LOOKUP_TABLE default\n";

    for (int i = 0; i < X * Y; ++i) {
        ofs << iterations[i] << "\n";
    }

    ofs.close();
}
\end{lstlisting}
```


**Function description, input parameters and file opening/closing:**  
What this function does is to transfer the Mandelbrot set data (`iterations` vector) to a VTK file. It has exactly the same inputs as `saveToPPM`.

As with the previous PPM format, we need to open the file with `std::ofstream`. This is what enables the program to export the data from memory to a usable file format. Without this, the program would be unable to save or share the results. `std::ofstream ofs(filename);` creates an output file stream (`ofs`) and opens the file named `filename` for writing. Once the function has completed its tasks, it is good practice to include `ofs.close();` to close the file after all the data has been written.

**VTK header and dataset description:**  
Following this, we need to define the basic metadata required by VTK files; otherwise, ParaView would not be able to operate with our output. It is important to know that the VTK file format has a specific order that must be followed so ParaView (or any other compatible VTK-based applications) can read the file correctly.

As we previously mentioned, `ofs` is an output file stream object of type `std::ofstream` used to write data to a file. The `<<` (data is sent in this direction) operator is the stream insertion operator that sends data to the output stream for writing. `<< "\n"` is used to insert a newline.  
`ofs << "\# vtk DataFile Version 3.0\n";` tells ParaView that this is a VTK file version 3.0. Different versions of VTK may use different syntax or handle data differently.  
`ofs << "Mandelbrot Set\n";` is a title that explains that this file contains Mandelbrot set data. It is a helpful to use it as identification.  
`ofs << "ASCII\n";` is needed to store the data in a readable text format (ASCII), not binary. The fact that ASCII is a human-readable format means that the data can be seen, verified, and modified in a text editor like `VIM` or `emacs`.  
The option `ofs << "DATASET STRUCTURED_GRID\n";` enables us to use a structured grid that represents data where points are organized in a regular grid layout (a 2D grid).


**Grid dimensions, origin and spacing:**  
We continue with more specifications.

`ORIGIN 0 0 0:` sets the origin of the grid at the point (0, 0, 0) in 3D space. This is important for the software to know where to place the first point of the dataset.  
`SPACING 1 1 1:` This indicates that each point in the grid is spaced 1 unit apart in the x, y, and z directions. For a 2D grid, the z spacing is not relevant but we keep it to maintain the compatibility of the data format.

**Point data specification:**  
`POINT_DATA X * Y:` indicates that the grid contains \(X \times Y\) points, with each point representing a pixel in the image. This is important for memory allocation. `POINT_DATA` is part of the syntax in ASCII file format in VTK. Other keywords include `SCALARS`, `VECTORS`, `CELL_DATA`, and have different purposes.  
For example, in `SCALARS mandelbrot_iterations int 1:`, each point in the grid has a scalar value for the iterations. The data type is `int` (integer) which is the data type of `iterations`.  
`LOOKUP_TABLE default:` is a standard specification so that the default color lookup table will be used to map the scalar values (iterations) to colors in ParaView.

**Data iteration loop:**  
This loop is necessary to iterate through the `iterations` vector and write each count to the VTK file.

The line `ofs << iterations[i] << "\n";` writes the number of iterations for each pixel to the output file.

**Checking the output:**
Once you have done this a \texttt{.vtk} would have been created and when you open the file with VIM.
```cpp
vim mandelbrot_gradient_VTK.vtk 
```
The follow type of output should come. This is just an extraction of the file.
```cpp
 vtk DataFile Version 3.0
Mandelbrot Set
ASCII
DATASET STRUCTURED_POINTS
DIMENSIONS 5000 5000 1
ORIGIN 0 0 0
SPACING 1 1 1
POINT_DATA 25000000
SCALARS mandelbrot_iterations int 1
LOOKUP_TABLE default
204
204
204
204
204
204
204
// Rest of the file
```

Note that in the output file generated by the function you provided, the x and y coordinates of the points are not explicitly included in the output. This all has to do with the options that we used in This is the file we will use in the next subsection. If you want to include the coordinates you will need an output of this form. In a 2D output like for example image files, each pixel corresponds to a point in the complex plane and the use of x and y is implicit.  We will show you in the next section how to include the x,y coordinates when we use a 3D grid because the visualization requires knowledge of each point's position in a three-dimensional space.
```cpp
 ofs << x << " " << y << " " << iterations[i] << "\n";
```


### ParaView installation:

First we need to install the software if you have not done so. If you are using Ubuntu, you can install ParaView either from the Ubuntu package manager as follows.

Type this commands in your console.
```
sudo apt update
sudo apt install ParaView
```

And open ParaView. Once you open the program, you will be able to see the version you have downloaded.
```
ParaView
```
Alternatively, you can install ParaView by downloading the latest version directly from the official ParaView website: \url{https://www.paraview.org/download/}. Copy the \texttt{.tar.gz} file to the folder where you want to place ParaView. Then change directory to that folder and execute the following commands to uncompress the file.
```
tar -xzf file_name_example
```
Once you do that you can open the program with the following command.
```
./ParaView
```

## 2D Visualization

Lest zoom-in and capture the intricate structures of the set. For this replace in your code the following coordinates:

The Islands region
```cpp
constexpr double X_MIN = -0.88;   // Minimum x-axis value for Islands region
constexpr double X_MAX = -0.84;   // Maximum x-axis value for Islands region
constexpr double Y_MIN = 0.215;   // Minimum y-axis value for Islands region
constexpr double Y_MAX = 0.255;   // Maximum y-axis value for Islands region
```


The Seahorse valley
```cpp
constexpr double X_MIN = -0.74877;    // Minimum x-axis value for Seahorse valley
constexpr double X_MAX = -0.74872;    // Maximum x-axis value for Seahorse valley
constexpr double Y_MIN = 0.06505;     // Minimum y-axis value for Seahorse valley
constexpr double Y_MAX = 0.06510;     // Maximum y-axis value for Seahorse valley
```

The double spiral
```cpp
constexpr double X_MIN = -1.0;    // Minimum x-axis value for double spiral
constexpr double X_MAX = -0.5;    // Maximum x-axis value for double spiral
constexpr double Y_MIN = -0.5;    // Minimum y-axis value for double spiral
constexpr double Y_MAX = 0.0;     // Maximum y-axis value for double spiral
```

Once you run your code you will obtain the following output in PPM or VTK respectiveley.

<p align="center">
  <img src="/Figures/mandelbrot_gradient_psychedelic.png" alt="Seahorse valley in PPM format" width="50%">
  <br>
  <em>Figure: Seahorse valley in PPM format.</em>
</p>

<p align="center">
  <img src="/Figures/mandelbrot_gradient_double_spiral.png" alt="Double spiral region in PPM format" width="50%">
  <br>
  <em>Figure: Double spiral region in PPM format.</em>
</p>

<p align="center">
  <img src="/Figures/Islands_region_VTK_more_blue.png" alt="Island region in VTK format-version 1" width="70%">
  <br>
  <em>Figure: Island region in VTK format-version 1.</em>
</p>

<p align="center">
  <img src="/Figures/Islands_region_VTK_more_red.png" alt="Island region in VTK format-version 1" width="70%">
  <br>
  <em>Figure: Island region in VTK format-version 2.</em>
</p>
