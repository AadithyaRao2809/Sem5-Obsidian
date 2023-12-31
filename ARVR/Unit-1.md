### What is VR
![](../Attachments/Unit-1-20230929.png)

### 3 I's
![](../Attachments/Unit-1-20230929-1.png)

### History
![](../Attachments/Unit-1-20230929-2.png)
![](../Attachments/Unit-1-20230929-3.png)
![](../Attachments/Unit-1-20230929-4.png)
![](../Attachments/Unit-1-20230929-5.png)
![](../Attachments/Unit-1-20230929-6.png)
*VPL - Virtual programming languages*
![](../Attachments/Unit-1-20230929-7.png)

### The five components of VR
![](../Attachments/Unit-1-20230929-8.png)

### AR
![](../Attachments/Unit-1-20230929-9.png)

### Serpenski Gasket
![](../Attachments/Unit-1-20230929-10.png)
![](../Attachments/Unit-1-20230929-11.png)
`generate_a_point(p): picks a random vertex and it halves it with p,returning the coordinate`
2nd type of code can be gpu displayed
![](../Attachments/Unit-1-20230929-12.png)

### OpenGL
#### Categories of API's
1) Primitive functions(lines,polygons,triangles and quads)
2) Attribute fuctions(colors,fills)
3) Viewing functions(Camera View,Orthographic,perspective projections)
4) Transformation functions(rotation,translation,shearing,scaling)
5) Input functions(mouse,keyboard)
6) Control functions(multiprocessing,window environment,error handling)

#### openGL interface
![](../Attachments/Unit-1-20230929-13.png)

#### Primitives
- geometric primitives(points,line segments etc->go through geometric pipeline to determine visibility)
- raster primitives(array of pixels,cannot be manipulated,pass through a parallel pipeline on thir way to frame buffer)
![](../Attachments/Unit-1-20230929-14.png)

#### Polygons
Performance $\propto$ polygons/sec
![](../Attachments/Unit-1-20230929-15.png)

```
#include <GL/glut.h>
#include <GL/GL.h>
#include <math.h>
#include <stdio.h>
#define PI 3.14159265358979323846

// Global variables for mouse interaction and camera
float angle = 0;
float xrot = 0.0f;
float yrot = 0.0f;
float xdiff = 0.0f;
float ydiff = 0.0f;
bool mouseDown = false;
float tX = 0;
float tY = 0;
float tZ = 0;
double valZoom = 10.0;
double camX = 0, camY = 0, camZ = 10.0;

// Callback for mouse click event
void sphereMouseEvent(int button, int state, int x, int y)
{
    if (button == GLUT_LEFT_BUTTON && state == GLUT_DOWN)
    {
        mouseDown = true;
    }
    else
        mouseDown = false;

    if (mouseDown)
    {
        xdiff = (yrot + x);
        ydiff = -y + xrot;
    }

    if (mouseDown)
    {
        xdiff = (yrot + x);
        ydiff = -y + xrot;
    }
}

// Callback for mouse motion (sphere rotation)
void sphereMouseMotion(int x, int y)
{
    if (mouseDown)
    {
        yrot = -(x + xdiff);
        xrot = (y + ydiff);
        if (xrot > 89) xrot = 89.0f;
        if (xrot < -89) xrot = -89.0f;

        camX = valZoom * (cos(xrot * PI / 180) * sin(yrot * PI / 180));
        camY = valZoom * (sin(xrot * PI / 180));
        camZ = valZoom * (cos(xrot * PI / 180) * cos(yrot * PI / 180));
    }
    glutPostRedisplay();
}

// Callback for rendering the scene
void display()
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
    gluLookAt(
        camX, camY, camZ,
        0.0f, 0.0f, 0.0f,
        0.0f, 1.0f, 0.0f
    );
    glTranslatef(tX, tY, tZ);
    glutWireSphere(1, 50, 50);
    glFlush();
}

// Callback for window reshape
void reshape(int width, int height)
{
    glViewport(0, 0, width, height);
}

// Function to set material properties
void setMaterial(GLfloat ambientR, GLfloat ambientG, GLfloat ambientB,
    GLfloat diffuseR, GLfloat diffuseG, GLfloat diffuseB,
    GLfloat specularR, GLfloat specularG, GLfloat specularB,
    GLfloat shininess)
{
    GLfloat ambient[] = { ambientR, ambientG, ambientB };
    GLfloat diffuse[] = { diffuseR, diffuseG, diffuseB };
    GLfloat specular[] = { specularR, specularG, specularB };

    glMaterialfv(GL_FRONT_AND_BACK, GL_AMBIENT, ambient);
    glMaterialfv(GL_FRONT_AND_BACK, GL_DIFFUSE, diffuse);
    glMaterialfv(GL_FRONT_AND_BACK, GL_SPECULAR, specular);
    glMaterialf(GL_FRONT_AND_BACK, GL_SHININESS, shininess);
}

// Initialize lighting
void initLight()
{
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();

    GLfloat lightpos[] = { 0.0, 0.0, 15.0 };
    GLfloat lightcolor[] = { 0.5, 0.5, 0.5 };
    GLfloat ambcolor[] = { 0.2, 0.2, 0.2 };
    GLfloat specular[] = { 1.0, 1.0, 1.0 };

    glEnable(GL_LIGHTING);
    glEnable(GL_LIGHT0);
    glLightfv(GL_LIGHT0, GL_POSITION, lightpos);
    glLightfv(GL_LIGHT0, GL_AMBIENT, ambcolor);
    glLightfv(GL_LIGHT0, GL_DIFFUSE, lightcolor);
    glLightfv(GL_LIGHT0, GL_SPECULAR, specular);
    glEnable(GL_COLOR_MATERIAL);
}

// Animation callback
void animate()
{
    angle = angle + 0.1;
    glutPostRedisplay();
}

// Keyboard input callback
void keyBoard(unsigned char key, int x, int y)
{
    switch (key)
    {
        case 'A':
        case 'a': tX = tX - 0.1; break;
        case 'D':
        case 'd': tX = tX + 0.1; break;
        case 'W':
        case 'w': tY = tY + 0.1; break;
        case 'S':
        case 's': tY = tY - 0.1; break;
        default:
            break;
    }
    glutPostRedisplay();
}

// Special keyboard input callback
void arrowKey(int key, int x, int y)
{
    switch (key)
    {
        case GLUT_KEY_UP:
            valZoom = valZoom + 0.1; break;
        case GLUT_KEY_DOWN: 
            valZoom = valZoom - 0.1; break;
        default:
            break;
    }
    camX = valZoom * (cos(xrot * PI / 180) * sin(yrot * PI / 180));
    camY = valZoom * (sin(xrot * PI / 180));
    camZ = valZoom * (cos(xrot * PI / 180) * cos(yrot * PI / 180));
    glutPostRedisplay();
}

int main(int argc, char* argv[])
{
    glutInit(&argc, argv);
    glutInitWindowSize(500, 500);
    glutInitWindowPosition(0, 0);
    glutInitDisplayMode(GLUT_RGB | GLUT_DEPTH);
    glutCreateWindow("my first openGL");
    glutDisplayFunc(display);
    glEnable(GL_DEPTH_TEST);
    initLight();
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluPerspective(90, 1, 0.0001, 100);
    glutIdleFunc(animate);
    glutMouseFunc(sphereMouseEvent);
    glutMotionFunc(sphereMouseMotion);
    glutKeyboardFunc(keyBoard);
    glutSpecialFunc(arrowKey);
    glutMainLoop();
}

/*
gluLookAt(0.0, 0.0, 5.0,  // Eye position
          0.0, 0.0, 0.0,  // Target position
          0.0, 1.0, 0.0); // Up direction

*/

```

![[../Attachments/Unit 1 2023-09-26 23.44.33.excalidraw]]

```
void display()
{
    glClear(GL_COLOR_BUFFER_BIT); // Clear the color buffer

    glPointSize(2); // Set the point size for drawing

    // Use GL_POINTS to mark the specified pixels
    glBegin(GL_POINTS);

    for (int i = 0; i < MAX; i++)
    {
        // Change the color every iteration
        glColor3f((float)(rand() % 255) / 255.0,
                  (float)(rand() % 255) / 255.0,
                  (float)(rand() % 255) / 255.0);

        // Draw all the points in the Sierpinski Gasket
        glVertex2dv(array[i]);
    }

    glEnd(); // End drawing

    glFlush(); // Flush the rendering pipeline to display the image
}
```

```
#include<gl/glut.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>
#include <stdio.h>


//Function: draw Colored Cube
void coloredCube()
{

    glColor3f(1.0f, 0.0f, 0.0f);

    glBegin(GL_POLYGON);
    glVertex3f(-1.0, -1.0, 1.0);
    glVertex3f(1.0, -1.0, 1.0);
    glVertex3f(1.0, 1.0, 1.0);
    glVertex3f(-1.0, 1.0, 1.0);
    glEnd();

    //right face
    glColor3f(0.0f, 1.0f, 0.0f);
    glBegin(GL_POLYGON);
    glVertex3f(1, -1, 1);
    glVertex3f(1, 1, 1);
    glVertex3f(1, 1, -1);
    glVertex3f(1, -1, -1);
    glEnd();

    //back face
    glColor3f(0.0f, 0.0f, 1.0f);
    glBegin(GL_POLYGON);
    glVertex3f(-1, -1, -1);
    glVertex3f(1, -1, -1);
    glVertex3f(1, 1, -1);
    glVertex3f(-1, 1, -1);
    glEnd();


    glColor3f(1.0f, 1.0f, 0.0f);
    glBegin(GL_POLYGON);
    glNormal3f(-1, 0, 0);
    glVertex3f(-1, -1, 1);
    glVertex3f(-1, 1, 1);
    glVertex3f(-1, 1, -1);
    glVertex3f(-1, -1, -1);
    glEnd();

    //topface
    glColor3f(0.0f, 1.0f, 1.0f);
    glBegin(GL_POLYGON);
    glVertex3f(1, 1, 1);
    glVertex3f(-1, 1, 1);
    glVertex3f(-1, 1, -1);
    glVertex3f(1, 1, -1);
    glEnd();

    //bottom face
    glColor3f(1.0f, 0.0f, 1.0f);
    glBegin(GL_POLYGON);
    glVertex3f(1, -1, 1);
    glVertex3f(-1, -1, 1);
    glVertex3f(-1, -1, -1);
    glVertex3f(1, -1, -1);
    glEnd();
    /* flush drawing routines to the opengl window*/
    glFlush();
}

//Function: Display Call back - used to draw the required visualization
//return type: void
//parameters: void

void display()
{
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    gluLookAt(0, 0, 10, 0, 0, 0, 0, 1, 0);
    //glScalef(0.8, 0.8, 0.8);
    coloredCube();
    glTranslatef(3, 0, 6);
    coloredCube();
	glFlush();
}

int main(int argc, char* argv[])
{
	//Initialize glut
	glutInit(&argc, argv);

	//configure opengl window
	glutInitWindowSize(500, 500);
	glutInitWindowPosition(0, 0);
	glutInitDisplayMode(GLUT_RGB | GLUT_DEPTH);

	//create the Opengl window with a specific name
	glutCreateWindow("My First OpenGL Window");

    /* define the projection transformation */
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    glOrtho(-10, 10, -10, 10, 10, -10);
    gluPerspective(90, 1, 0.001, 1000);
	//register the callback for displaying
	glutDisplayFunc(display);

	//star the opengl loop 
	glutMainLoop();
}
```
#### `glOrtho(-10, 10, -10, 10, 10, -10)`

`glOrtho` is used to set up an orthographic projection, commonly used in 2D games and engineering applications. It eliminates perspective distortion, where objects do not appear smaller with distance. The provided parameters represent:

- `left`, `right`: The coordinates of the left and right vertical clipping planes. Objects outside the range of -10 to 10 on the X-axis will be clipped.
- `bottom`, `top`: The coordinates of the bottom and top horizontal clipping planes. Objects outside the range of -10 to 10 on the Y-axis will be clipped.
- `near`, `far`: The distances to the near and far clipping planes. Objects closer than 10 units or farther than 10 units from the camera will be clipped.

`glOrtho` establishes an orthographic projection matrix, flattening the 3D scene onto a 2D plane.

#### `gluPerspective(90, 1, 0.001, 1000)`

`gluPerspective` sets up a perspective projection, replicating the effect of objects appearing smaller with distance. The provided parameters represent:

- `fovy`: The field of view angle in degrees, vertically. Here, it's set to 90 degrees, creating a wide-angle view.
- `aspect`: The aspect ratio, usually the width of the viewport divided by the height. It's set to 1, indicating a square viewport.
- `zNear`, `zFar`: The distances to the near and far clipping planes. Objects closer than 0.001 units or farther than 1000 units from the camera will be clipped.
-  `fovy/fovx`=`w/h`=aspect ratio

This function creates a perspective projection matrix, mimicking real-world perspective effects.

`Clip` is wrt to camera frame
![](../Attachments/Unit%201-20230927.png)
![](../Attachments/Unit%201-20230927-1.png)

[Unit-2](Unit-2.md)

