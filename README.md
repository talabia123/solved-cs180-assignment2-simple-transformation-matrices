Download Link: https://assignmentchef.com/product/solved-cs180-assignment2-simple-transformation-matrices
<br>
In the lectures so far, we have learned how to use matrix transformations as a tool to arrange objects in 2D or 3D space. Now it’s time to get some hands-on experience by implementing a few simple transformation matrices. In this assignment, you are to simulate a simplified version of CPU-based rasterization renderer.

The task of is to make a rotation matrix and a perspective projection matrix. Given the 3D world coordinate of three vertices, <em>v</em><sub>0</sub>(2<em>.</em>0<em>,</em>0<em>.</em>0<em>,</em>−2<em>.</em>0), <em>v</em><sub>1</sub>(0<em>.</em>0<em>,</em>2<em>.</em>0<em>,</em>−2<em>.</em>0), <em>v</em><sub>2</sub>(−2<em>.</em>0<em>,</em>0<em>.</em>0<em>,</em>−2<em>.</em>0), you should transform them into screen space coordinate and draw the wireframe triangle on screen. (We provide you the <em>draw </em><em>triangle </em>function, you only need to construct the transformation matrices). In short, you need to do the model, view, projection, viewport transformation to see a triangle on the screen. In the code provided the model and projection transformation parts are left unfinished. If you are unclear about any concept above, review the lecture notes and post questions on Piazza.

You need to modify the following functions in the main.cpp file:

<ul>

 <li><strong>get model matrix(float rotation angle): </strong>Set the model transformation matrix element by element, and return the matrix. Here, you should only implement 3D rotation that’s the result of rotating the triangle around Z axis. You don’t need to handle translation and scale.</li>

 <li><strong>get projection matrix(float eye fov, float aspect ratio, float zNear, float zFar): </strong>Set the perspective projection matrix element by element with the given parameters, and return it.</li>

 <li><strong>[Optional] main(): </strong>Provide other operation you need.</li>

</ul>

Don’t change the signature of any of the functions, and other implemented functions. It is your responsibility that your code compiles and runs without problems.

Inside these functions, create the corresponding model and projection matrices and return it. When you get the transformations correctly, you will see a triangle that’s drawn in wireframe.

The frames are rendered and drawn one by one. When you get the transformations, the rasterizer will create a window showing you the triangle. Then, you can use <strong>A </strong>and <strong>D </strong>keys to rotate the triangle around the Z axis. If you press the <strong>Esc </strong>key, the window will be closed and the program will terminate.

It’s also possible to use the program from the command line. You can run it using the following commands to run and pass the rotation angle to the program.

In this case, there won’t be any windows, but the program will save the output image to the given filename (or as <strong>output.png </strong>if no name is specified from the command line). The location of the new created image will be right next to the executable. So if your executable is in a <strong>build </strong>folder, the image will be inside this folder.

The command line can be used like these:

$ ./Rasterizer                                                                  // Opens a window and renders the image

// in it repeatedly until Esc is hit.

// The triangle can be rotated with

// A and D keys

$ ./Rasterizer -r 20 image.png // Rotates the triangle by 20 degrees

// and the image is saved in image.png

<h1>1           Getting started</h1>

You can work with the accompanying skeleton code in one of the following ways: • Install the project dependencies listed below in your system. Download the code, build and run it locally.

<ul>

 <li>All dependencies are installed on the VM provided to you in the previous assignment. Download the skeleton into it and work there.</li>

</ul>

<strong>2.1      Own system</strong>

The following dependencies need to be installed in your system:

<ul>

 <li><a href="https://cmake.org/install/">CMake</a><a href="https://cmake.org/install/">:</a> This is the build tool needed to build the project.</li>

 <li><a href="https://eigen.tuxfamily.org/dox/GettingStarted.html">Eigen</a><a href="https://eigen.tuxfamily.org/dox/GettingStarted.html">:</a> This library is used in our project for linear algebra operations. Download the latest stable source code, extract it and rename the folder as eigen3</li>

 <li><a href="https://docs.opencv.org/4.1.0/df/d65/tutorial_table_of_content_introduction.html">OpenCV</a><a href="https://docs.opencv.org/4.1.0/df/d65/tutorial_table_of_content_introduction.html">:</a> This library is used for image manipulation and display. On Debian/Ubuntu, this can be easily installed by apt install libopencv-dev.</li>

</ul>

Now you should be able to tell the project how to find these libraries to build. IDEs a way to enter the path of the libraries and they generate CMakeLists.txt accordingly. To do this yourself, if the path of your downloaded eigen3 folder is &lt;some-path&gt;/eigen3, add the following line at the end of CmakeLists.txt target_include_directories(Rasterizer PUBLIC &lt;some-path&gt;)

If OpenCV is installed correctly on your system, the

find_package(OpenCV REQUIRED)

target_link_libraries(Rasterizer ${OpenCV_LIBRARIES}) lines should take care of finding OpenCV. Now the project can be built and run.

<table width="610">

 <tbody>

  <tr>

   <td width="157">mkdir build</td>

   <td width="453">// Create a folder to keep the build artifacts</td>

  </tr>

  <tr>

   <td width="157">cd build</td>

   <td width="453">// Go into the build folder</td>

  </tr>

  <tr>

   <td width="157">cmake ..</td>

   <td width="453">// Run CMake, by giving the path to the// CMakeLists.txt file as argument</td>

  </tr>

  <tr>

   <td width="157">make -j4</td>

   <td width="453">// Compile your code by make, -j4 parallelizes// the compilation through 4 cores</td>

  </tr>

  <tr>

   <td width="157">./Rasterizer</td>

   <td width="453">// Run your code</td>

  </tr>

 </tbody>

</table>

<h1>2           Skeleton code structure</h1>

You don’t need to use the triangle class for this assignment. So the files you need to understand and modify are: rasterizer.hpp and main.cpp. The rasterizer.hpp file provides the interface for the renderer and handles the drawing.

The rasterizer class provides all the necessary functionality in our system. Member variables:

<ul>

 <li>Matrix4f model, view, projection: Three matrices for our transformation.</li>

 <li>vector&lt;Vector3f&gt; frame_buf: Frame buffer object, which stores the color data to be displayed on the screen.</li>

</ul>

Member functions:

<ul>

 <li>set_model(const Eigen::Matrix4f m): Sets the class model matrix to the given parameter.</li>

 <li>set_view(const Eigen::Matrix4f v): Sets the class view matrix to the given parameter.</li>

 <li>set_projection(const Eigen::Matrix4f p): Sets the class projection transformation matrix to the parameter.</li>

 <li>set_pixel(Vector2f point, Vector3f color): Sets the screen pixel at coordinates (x,y) to the color (r,g,b) by writing to the frame buffer.</li>

 <li>draw(…): This method first does the necessary transformations by multiplying with model, view, projection matrices to get the vertices in normalized device coordinates (NDC). These coordinates range over [−1<em>,</em>1]. Then the viewport transformation is done to map to our window size. This is implemented and is important for you to go through and understand.</li>

</ul>

These setter functions are to be used by you to set the model, view and projection transformation matrices of the rasterizer to the correct values.

In the main.cpp file the graphics pipeline is simulated. An instance of the rasterizer class is first defined and other necessary variables are set. Then a triangle is hard coded with three vertices (don’t change them). Above the main function, there are 3 function definitions that each calculate model, view and projection matrices. Each of these functions must return the corresponding matrix, which are then passed to the rasterizer using the set_model(), set_view() and set_projection() functions respectively. Then the rasterizer’s draw method is invoked to draw the image which is then displayed by OpenCV.