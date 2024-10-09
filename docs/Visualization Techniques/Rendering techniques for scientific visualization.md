Our starting point is working with VTK in 3D. The development of this code was done on a workstation with 64GB of RAM. This allows us to post-processes with a high level of fidelity. If you are using a laptop, it is recommended to limit the grid dimensions to 500x500.

**Opening the file and view settings:**
Start ParaView and load your `.vtk` file via `File > Open`. You will see the following graphical user interface.

The graphical user interface (GUI) of ParaView is designed to facilitate visualization and analysis of scientific data. Here’s an overview of the key components, focusing on three main areas: **Pipeline Browser**, **Color Map Editor**, and **Properties Panel**.

1.  The Pipeline Browser is a crucial part of interface that displays the data pipeline, showing all the loaded datasets and the filters applied to them. It presents a hierarchical view of all sources (datasets) and filters applied, allowing users to visualize the flow of data processing. You can toggle the visibility (the eye icon) of individual elements in the pipeline, allowing you to focus on specific datasets or results.

2.  The Color Map Editor is used to customize how data is visually represented through different color mappings.

3.  The Properties Panel is where users configure the settings and parameters for the selected dataset or filter. This is the place where you can tune the different parameters to obtain a nice visualization. We will use this in the rendering.

To obtain the following figure, select `Surface with edges` in the toolbar. We have applied here a colormap (coming next).
<p align="center">
  <img src="/Figures/Paraview_Graphical_User_Interface.png" alt="ParaView GUI" width="100%">
  <br>
  <em>Figure: ParaView GUI.</em>
</p>

**Extract surface:**  
A very important step. For certain analysis, like rendering only the outer surface is important. By extracting the surface we reduce the data size, making rendering faster and more efficient. 

   - Make sure the vtk file is selected in the pipeline browser. Go to `Filters > Alphabetical > Extract Surface` or search for `Extract Surface` in the filters toolbar.
   - Apply the filter by clicking the **Apply** button in the **Properties** panel.

<p align="center">
  <img src="/Figures/Extract_surface.png" alt="Extract surface option in ParaView" width="60%">
  <br>
  <em>Figure: Extract surface option in ParaView.</em>
</p>


**Color settings:**
In the colormap editor, to open the color options select the `Choose prest` (option number 6 going downwards).

<p align="center">
  <img src="/Figures/color_options.png" alt="Colormap options" width="30%">
  <br>
  <em>Figure: Colormap options.</em>
</p>

You will be able to choose the most adequate colormap as shown below.

<p align="center">
  <img src="/Figures/color_palete.png" alt="Colormap palette" width="30%">
  <br>
  <em>Figure: Colormap palette.</em>
</p>

An important option is the `Rescale to custom range` (option number 2 going downwards). Particularly in visualizations with a vast range of values when you want to focus on small section you can select the values of range of values and the colormap will be adapted accordingly.

In our case, eventhough the iterations go beyond 50, we chose this limit as it shows better the nuances in the locations near the bulb edges.

<p align="center">
  <img src="/Figures/Range_of_colors.png" alt="Rescale to custom range" width="30%">
  <br>
  <em>Figure: Rescale to custom range.</em>
</p>


**Export as a high quality figure:**
ParaView offers the option to save a screenshot in very good resolution. To do this go to `File > Save Screenshot`. Once you click you will see the following window.

<p align="center">
  <img src="/Figures/Export_image.png" alt="Save screenshot options" width="30%">
  <br>
  <em>Figure: Save screenshot options.</em>
</p>

You can increase the resolution however it is very easy to increase the size by doing so. 

**Rendering options:**  
In this section, we are going to use now a high resolution 3D file obtained with 1000x1000 pixels.
The following figure was generated with the following configuration that you can apply to your 3D file and explore further:


<p align="center">
  <img src="/Figures/main_settings_rendering.png" alt="Settings used after extracting the surface" width="30%">
  <br>
  <em>Figure: Settings used after extracting the surface.</em>
</p>


We are using a slight level of transparency and this enable us to see inside the black region. Note that we see in full display the finer details of the set. 

<p align="center">
  <img src="/Figures/test_3D_main.png" alt="Mandelbrot set in ParaView" width="100%">
  <br>
  <em>Figure: Mandelbrot set in ParaView.</em>
</p>


Another feature is the level of specular used. Due to the high resolution is not visible however in the following figure at a lower resolution you can see the "brightness".
<p align="center">
  <img src="/Figures/showing_specular.png" alt="Specular level" width="80%">
  <br>
  <em>Figure: A higher specular level.</em>
</p>

The Smooth filter in ParaView is used to smooth the surface of a mesh or geometry. This option reducing sharp edges, noise, or roughness in the data. It is particular useful in data set with lower resolution. The following figure was generate with the following configuration:

<p align="center">
  <img src="/Figures/smooth_settings.png" alt="Settings used inside the smooth filter" width="30%">
  <br>
  <em>Figure: Settings used inside the smooth filter.</em>
</p>

Note the use of transparency (opacity option) and that the finer details are now a bit blurry.

<p align="center">
  <img src="/Figures/test_3D_v12_specular.png" alt="Mandelbrot set in ParaView  where the smooth filter was applied" width="100%">
  <br>
  <em>Figure: Mandelbrot set in ParaView where the smooth filter was applied.</em>
</p>





