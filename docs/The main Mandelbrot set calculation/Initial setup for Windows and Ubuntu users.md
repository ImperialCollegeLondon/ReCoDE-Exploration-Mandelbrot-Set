To run the provided C++ code on both Windows and Ubuntu, you need to set up a C++ development environment with a suitable compiler. The instructions below will help you get everything set up for each operating system. We will also introduce the use of CMake. For convenience, we will use the main Mandelbrot set code. We will describe the functionality of this code in detail in the next chapters.

## Windows Setup (Using Windows WSL)

I recommend using Windows WSL (Windows Subsystem for Linux) for this ReCoDe. This option enables the use of Linux on a Windows machine in a direct and easy manner without the need for Virtual Machines or emulators.

   1. Go to the [Windows WSL website](https://aka.ms/wsl).
   2. Follow the instructions to set up WSL and configure the necessary settings.
   3. Once WSL is installed, you can follow the standard Ubuntu instructions for setting up a C++ development environment as you will see next.


## Ubuntu Setup

### Install GCC (GNU Compiler Collection)

1. Open the terminal and install GCC and necessary build tools:
```
sudo apt update
sudo apt install build-essential
```

### Compiling and Running the Code

1. Navigate to the directory where your `.cpp` file is located using the terminal. Use the command `pwd` if you need to locate your directory.
2. Compile the code with the following command:
```
   g++ -o mandelbrot mandelbrot.cpp -std=c++11
```
3. Run the executable:
```
./mandelbrot
```

You should see the following output on your screen and we are ready to go:

```
Width of the image:5000
Height of the image: 5000
Total scaling time: 0.007769 seconds
Total iteration time: 275.241 seconds
Total time taken to compute the Mandelbrot set (single-threaded version): 288.617 seconds
Number of CPUs used: 12
Total time it takes to generate image: 6.57477 seconds
Mandelbrot set with colormap: mandelbrot_gradient.ppm
```

