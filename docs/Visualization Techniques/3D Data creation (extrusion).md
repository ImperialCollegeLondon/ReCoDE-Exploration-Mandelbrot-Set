Likewise, in the multithreaded version of our code, `saveToVTK3D` converts the 2D Mandelbrot set into a 3D representation and saves it in a VTK file format. This file can be used by visualization tools to render a 3D view of the Mandelbrot set. The function takes the `iterations` vector for each point in the 2D Mandelbrot set and "extrudes" (projects) it along a third dimension (Z-axis) based on these iteration values. To do this, we map the iteration count to a height (Z-coordinate) using logarithmic scaling to create a smoother extrusion. The function writes this 3D data to the file using the structured grid format of VTK.

```cpp
void saveToVTK3D(const std::vector<int>& iterations, const std::string& filename) {
    std::ofstream ofs(filename);

    ofs << "# vtk DataFile Version 3.0\n";
    ofs << "3D Mandelbrot Set\n";
    ofs << "ASCII\n";
    ofs << "DATASET STRUCTURED_GRID\n";
    ofs << "DIMENSIONS " << X << " " << Y << " " << Z << "\n";
    ofs << "POINTS " << X * Y * Z << " float\n";

    // Generate 3D points by extruding along the Z-axis, with smoother scaling
    for (int z = 0; z < Z; ++z) {
        for (int y = 0; y < Y; ++y) {
            for (int x = 0; x < X; ++x) {
                double real = X_MIN + (X_MAX - X_MIN) * x / (X - 1);
                double imag = Y_MIN + (Y_MAX - Y_MIN) * y / (Y - 1);

                // Normalize iteration count between 0 and 1
                double normalized_iter = static_cast<double>(iterations[y * X + x]) / MAX_ITER;

                // Logarithmic scaling for smoother extrusion
                double z_coord = SCALING_FACTOR * log(1 + normalized_iter * (Z - 1));

                ofs << real << " " << imag << " " << z_coord << "\n";
            }
        }
    }

    ofs << "\nPOINT_DATA " << X * Y * Z << "\n";
    ofs << "SCALARS mandelbrot_iterations int 1\n";
    ofs << "LOOKUP_TABLE default\n";

    // Fill the scalar values for the 3D points
    for (int z = 0; z < Z; ++z) {
        for (int y = 0; y < Y; ++y) {
            for (int x = 0; x < X; ++x) {
                ofs << iterations[y * X + x] << "\n";
            }
        }
    }

    ofs.close();
}
```



**Function description, input parameters and file opening/closing:**  
Here, with the exception of the name `saveToVTK3D`, everything is similar to the 2D grid version.

We use this time `ofs << "3D Mandelbrot Set\n";`.  
Note that `ofs << "DIMENSIONS " << X << " " << Y << " " << Z << "\n";`, we include this time `Z` to represent the number of points along the z-axis in the 3D grid.  
This new line `ofs << "POINTS " << X * Y * Z << " float\n";` specifies the total number of points in the dataset and indicates that these points are of type `float`. The total number of points is the product of the dimensions \(X \times Y \times Z\). ParaView will know how many entries to read when loading the point data. When handling 3D data, it is recommended to use real-number values that can include decimal points for accurately mapping complex coordinates in a continuous space. 

**3D generation loop by extrusion:**  
We need to generate the 3D grid in order to fill it later with the `iterations` vector. We now have a triple nested loop that generates 3D coordinates for every point in the grid. It iterates through each point in the defined grid based on the dimensions \(X\), \(Y\), and \(Z\). The outer loop in the z-direction represents the extrusion dimension (Z), which refers to the depth or height in the 3D visualization.  


```cpp
    for (int z = 0; z < Z; ++z) {
        for (int y = 0; y < Y; ++y) {
            for (int x = 0; x < X; ++x) {
                double real = X_MIN + (X_MAX - X_MIN) * x / (X - 1);
                double imag = Y_MIN + (Y_MAX - Y_MIN) * y / (Y - 1);

                // Normalize iteration count between 0 and 1
                double normalized_iter = static_cast<double>(iterations[y * X + x]) / MAX_ITER;

                // Logarithmic scaling for smoother extrusion
                double z_coord = SCALING_FACTOR * log(1 + normalized_iter * (Z - 1));

                ofs << real << " " << imag << " " << z_coord << "\n";
            }
        }
    }
```

For each combination of indices \(x\), \(y\), and \(z\), it calculates the corresponding real and imaginary parts of the complex number based on the position of the pixel, mapping pixel coordinates to values in the complex plane.

The code normalizes the iteration count from the iterations vector for each \((x, y)\) point to a range between 0 and 1. To create smoother transitions in the 3D extrusion, logarithmic scaling is applied to the normalized iteration count. This scaled value is then used to determine the \(z\)-coordinate for each point in 3D space.

Note that to use `SCALING_FACTOR`, we need to add this to the definition of constants. We need this scaling; otherwise, the z-direction of the 3D output will be many orders of magnitude higher than the dimensions of the \((X, Y)\), making it very difficult to post-process.

```cpp
constexpr double SCALING\_FACTOR = 0.1;
```

The computed coordinates (real, imag, z) are output to the VTK file. This is the main 3D structure.

**Data specification of 3D points:**  
The following lines are the same as in the 2D grid case, with the only difference being that `POINT_DATA:` gets the number of points with \(X \times Y \times Z\), corresponding to the total number of points in the 3D grid.

```cpp
    ofs << "\nPOINT_DATA " << X * Y * Z << "\n";
    ofs << "SCALARS mandelbrot_iterations int 1\n";
    ofs << "LOOKUP_TABLE default\n";
```

**Loop to fill scalar values for the 3D points:**  
We finally write the iteration counts for each \((x, y)\) point in the 2D grid for every extruded layer along the z-axis.


```cpp
    for (int z = 0; z < Z; ++z) {
        for (int y = 0; y < Y; ++y) {
            for (int x = 0; x < X; ++x) {
                ofs << iterations[y * X + x] << "\n";
            }
        }
    }
```


**Checking the output:**

Likewise, once you have done this a `.vtk` would have been created and when you open the file with VIM.
```cpp
vim mandelbrot_3d_smooth.vtk
```
The follow type of output should come. This is just an extraction of the file.

```
 vtk DataFile Version 3.0
3D Mandelbrot Set
ASCII
DATASET STRUCTURED_GRID
DIMENSIONS 1000 1000 100
POINTS 100000000 float
-2 -1.5 0.00944007
-1.997 -1.5 0.00944007
-1.99399 -1.5 0.00944007
-1.99099 -1.5 0.00944007
-1.98799 -1.5 0.00944007
-1.98498 -1.5 0.00944007
-1.98198 -1.5 0.00944007
-1.97898 -1.5 0.00944007
-1.97598 -1.5 0.00944007
-1.97297 -1.5 0.00944007
-1.96997 -1.5 0.00944007
-1.96697 -1.5 0.00944007
-1.96396 -1.5 0.00944007
-1.96096 -1.5 0.00944007
-1.95796 -1.5 0.00944007
-1.95495 -1.5 0.00944007
// Rest of the file
```
Remember that a 3D output needs the x,y coordinates to fully define the spatial relationships of the points as compared to the 2D output presented earlier. After the `POINTS` section, you would typically have the `POINT_DATA` section followed by the scalar values associated with each point and should look like this. 
```
POINT_DATA 100000000
SCALARS mandelbrot_iterations int 1
LOOKUP_TABLE default
0
1
2
// Rest of the file
```
