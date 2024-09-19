#include <windows.h>
#include <GL/glut.h>
#include <cstdio>
#include <GL/gl.h>
#include <math.h>


// Global variable to track the current view
int currentView = 0; // 0 = City, 1 = Village

//city
int lampState = 0;  // 0 for deep yellow, 1 for light yellow
float carPosX = -30.0f;  // Starting position of the car
float leafShakeOffset = 0.0f;  // Offset for leaf shaking

// Village
float sunPosY = 20.0f; // Initial position of the sun
float _moveTruck = 0.0f;  // Truck position
float _moveCar = 0.0f;    // Car position
float _wheelAngle = 0.0f; // Wheel rotation angle
float _moveBoat = 0.0f;  // Boat position
float _windMill = 0.0f;  // Global variable for the rotation angle

float _moveCloud1 = -30.0f;  // Initial position of cloud1
float _moveCloud2 = 30.0f;   // Initial position of cloud2
int _directionCloud1 = 1;    // Direction flag for cloud1 (1 = moving right, -1 = moving left)
int _directionCloud2 = -1;   // Direction flag for cloud2 (1 = moving right, -1 = moving left)

GLfloat carPosition = 0.0f;  // Car's position along the X-axis
GLfloat carSpeed = 0.1f;     // Car's speed
GLfloat windowColor[3] = {1.0f, 1.0f, 1.0f}; // Default to white
GLfloat cloudOffsetX = 0.0f; // Initial x-axis position for clouds
GLfloat moonY = 25.0f; // Initial y-axis position for the moon

void line1()
{
    glLineWidth(5);
    glColor3f(0.0f, 0.0f, 0.0f);
    glBegin(GL_POLYGON); //ABCD
    glVertex2f(-30.0f, 5.0f);
    glVertex2f(0.0f, 5.0f);
    glVertex2f(30.0f, 5.0f);
    glEnd();

}

void semi_circle_brush(float r,float x,float y)
{
      glBegin(GL_POLYGON);

    glColor3ub(50, 205, 50);

    // Define semi-circle
    float radius = r;
    float centerX = x; // X coordinate of the center
    float centerY = y; // Y coordinate of the center
    float pi = 3.14159265359;

    for (int i = 0; i <= 180; i++) {
        float angle = i * pi / 180.0;
        glVertex2f(centerX + cos(angle) * radius, centerY + sin(angle) * radius);
    }

    glEnd();

}

void hillBorder()
{
    glColor3f(0.0f, 0.0f, 0.0f); // Border color
    glLineWidth(2);
    glBegin(GL_LINE_LOOP); //ABCD
    glVertex2f(-30.0f, 5.0f);
    glVertex2f(-25.0f, 10.0f);
    glVertex2f(-20.0f, 15.0f);
    glVertex2f(-15.0f, 20.0f);
    glVertex2f(-10.0f, 15.0f);
    glVertex2f(-5.0f, 20.0f);
    glVertex2f(0.0f, 25.0f);
    glVertex2f(5.0f, 20.0f);
    glVertex2f(10.0f, 15.0f);
    glVertex2f(15.0f, 20.0f);
    glVertex2f(20.0f, 15.0f);
    glVertex2f(25.0f, 10.0f);
    glVertex2f(30.0f, 5.0f);
    glEnd();


}

void hill_left_Part()
{
    glBegin(GL_POLYGON);
    glColor3ub(111, 78, 55);
    glVertex2f(-30.0f, 5.0f);
    glVertex2f(-30.0f, 10.0f);
    glVertex2f(-25.0f, 10.0f);
    glEnd();
    glColor3ub(1,1,1);
    glLineWidth(2);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-30.0f, 5.0f);
    glVertex2f(-30.0f, 10.0f);
    glVertex2f(-25.0f, 10.0f);
    glEnd();
    semi_circle_brush(2.6,-27.5,10);
}
void hill_right_Part()
{
    glBegin(GL_POLYGON);
    glColor3ub(111, 78, 55);
    glVertex2f(25.0f, 10.0f);
    glVertex2f(30.0f, 10.0f);
    glVertex2f(30.0f, 5.0f);
    glEnd();
    glColor3ub(1,1,1);
    glLineWidth(2);
    glBegin(GL_LINE_LOOP);
    glVertex2f(25.0f, 10.0f);
    glVertex2f(30.0f, 10.0f);
    glVertex2f(30.0f, 5.0f);
    glEnd();
    semi_circle_brush(2.6,27.5,10);
}

void hill1()
{
    glBegin(GL_POLYGON);
    glColor3ub(111, 78, 55);
    glVertex2f(-30.0f, 5.0f);
    glVertex2f(-25.0f, 10.0f);
    glVertex2f(-20.0f, 15.0f);
    glVertex2f(-15.0f, 20.0f);
    glVertex2f(-10.0f, 15.0f);
    glVertex2f(-5.0f, 10.0f);
    glVertex2f(0.0f, 5.0f);
    glEnd();
}

void hill2()
{
    glBegin(GL_POLYGON);
    glColor3ub(111, 78, 55);
    glVertex2f(-10.0f, 15.0f);
    glVertex2f(-5.0f, 20.0f);
    glVertex2f(0.0f, 25.0f);
    glVertex2f(5.0f, 20.0f);
    glVertex2f(10.0f, 15.0f);
    glVertex2f(5.0f, 10.0f);
    glVertex2f(0.0f, 5.0f);
    glVertex2f(-5.0f, 10.0f);
    glVertex2f(-10.0f, 15.0f);
    glEnd();
}

void hill3()
{
    glBegin(GL_POLYGON);
    glColor3ub(111, 78, 55);
    glVertex2f(0.0f, 5.0f);
    glVertex2f(5.0f, 10.0f);
    glVertex2f(10.0f, 15.0f);
    glVertex2f(15.0f, 20.0f);
    glVertex2f(20.0f, 15.0f);
    glVertex2f(25.0f, 10.0f);
    glVertex2f(30.0f, 5.0f);

    glEnd();
}

void sky()
{
     glBegin(GL_POLYGON);
    glColor3ub(255, 165, 79);
    glVertex2f(-30.0f, 5.0f);
    glVertex2f(-30.0f, 35.0f);
    glVertex2f(0.0f, 35.0f);
    glVertex2f(30.0f, 35.0f);
    glVertex2f(30.0f, 5.0f);
    glEnd();
}


// For Sun

   void sun(float radius, float cX, float cY)
   {
    glBegin(GL_POLYGON);
    for(int i=0;i<200;i++)
        {
            glColor3ub(255, 223, 0);
            float pi=3.1416;
            float A=(i*2*pi)/200;
            float r=radius;
            float x = r * cos(A);
            float y = r * sin(A);
            glVertex2f(x+cX,y+cY);

        }
        glEnd();
   }

      void tree_circle(float radius, float cX, float cY)
   {
    glBegin(GL_POLYGON);
    for(int i=0;i<200;i++)
        {
            glColor3ub(50, 205, 50);
            float pi=3.1416;
            float A=(i*2*pi)/200;
            float r=radius;
            float x = r * cos(A);
            float y = r * sin(A);
            glVertex2f(x+cX,y+cY);

        }
        glEnd();
   }


 void cloud_circle(float radius, float cX, float cY)
   {
    glBegin(GL_POLYGON);
    for(int i=0;i<200;i++)
        {
            glColor3ub(255, 255, 240);
            float pi=3.1416;
            float A=(i*2*pi)/200;
            float r=radius;
            float x = r * cos(A);
            float y = r * sin(A);
            glVertex2f(x+cX,y+cY);

        }
        glEnd();
   }
void main_street()
{
    glBegin(GL_POLYGON);
    glColor3ub(54, 69, 79);
    glVertex2f(-30.0f, -5.0f);
    glVertex2f(-30.0f, 5.0f);
    glVertex2f(30.0f, 5.0f);
    glVertex2f(30.0f, -5.0f);
    glEnd();
      // White Border
     glLineWidth(3);
    glColor3ub(255,255,255);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-30.0f, 0.0f);
    glVertex2f(0.0f, 0.0f);
    glVertex2f(30.0f, 0.0f);
    glEnd();
     // Black Border
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-30.0f, -5.0f);
    glVertex2f(-30.0f, 5.0f);
    glVertex2f(30.0f, 5.0f);
    glVertex2f(30.0f, -5.0f);
    glEnd();
}

void cloud1()
{
    glPushMatrix();
    glTranslatef(_moveCloud1, 0.0f, 0.0f);  // Move cloud1 along the X-axis
    cloud_circle(1.5,-25.0,25.0);
    cloud_circle(1.5,-26.8,25.0);
    cloud_circle(1.5,-26.0,26.3);
    glPopMatrix();
}

void clude2()
{
    glPushMatrix();
    glTranslatef(_moveCloud2, 0.0f, 0.0f);  // Move cloud2 along the X-axis
    cloud_circle(1.5,20.0,25.0);
    cloud_circle(1.5,21.8,25.0);
    cloud_circle(1.5,21.0,26.3);
    glPopMatrix();
}

void street_tree()
{
    glLineWidth(4);
    glColor3ub(160, 82, 4);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-25.0f, -5.0f);
    glVertex2f(-25.0f, -2.0f);
    glEnd();
    tree_circle(1.5,-25.0, -2.0);
    tree_circle(1.5,-25.0, -1.0);

}
void trees()
{
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(0.0,0.0,0);
    street_tree();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(10.0,0.0,0);
    street_tree();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(20.0,0.0,0);
    street_tree();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(30.0,0.0,0);
    street_tree();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(40.0,0.0,0);
    street_tree();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(50.0,0.0,0);
    street_tree();
    glPopMatrix();

}


void truck_wheel(float radius, float cX, float cY)
{
    glPushMatrix();
    glTranslatef(cX, cY, 0);
    glRotatef(_wheelAngle, 0.0f, 0.0f, 1.0f);  // Apply rotation based on _wheelAngle
    glTranslatef(-cX, -cY, 0);  // Translate back after rotation

    glBegin(GL_POLYGON);
    for (int i = 0; i < 200; i++)
    {
        glColor3ub(20, 20, 20);
        float pi = 3.1416;
        float A = (i * 2 * pi) / 200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();

    glPopMatrix();
}

void truck_box()
{
    glBegin(GL_POLYGON);
    glColor3ub(178, 34, 34);
    glVertex2f(-13.0f, 1.0f);
    glVertex2f(-13.0f, 4.0f);
    glVertex2f(-9.0f, 4.0f);
    glVertex2f(-9.0f, 1.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1, 1, 1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-13.0f, 1.0f);
    glVertex2f(-13.0f, 4.0f);
    glVertex2f(-9.0f, 4.0f);
    glVertex2f(-9.0f, 1.0f);
    glEnd();
}

void truck_front()
{
    glBegin(GL_POLYGON);
    glColor3ub(220, 20, 60);
    glVertex2f(-13.0f, 1.0f);
    glVertex2f(-13.0f, 2.0f);
    glVertex2f(-16.0f, 2.0f);
    glVertex2f(-16.0f, 1.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1, 1, 1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-13.0f, 1.0f);
    glVertex2f(-13.0f, 2.0f);
    glVertex2f(-16.0f, 2.0f);
    glVertex2f(-16.0f, 1.0f);
    glEnd();
}

void truck_glass()
{
    glBegin(GL_POLYGON);
    glColor3ub(173, 216, 230);
    glVertex2f(-13.0f, 2.0f);
    glVertex2f(-13.0f, 4.0f);
    glVertex2f(-14.0f, 4.0f);
    glVertex2f(-16.0f, 2.0f);
    glVertex2f(-13.0f, 2.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1, 1, 1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-13.0f, 2.0f);
    glVertex2f(-13.0f, 4.0f);
    glVertex2f(-14.0f, 4.0f);
    glVertex2f(-16.0f, 2.0f);
    glVertex2f(-13.0f, 2.0f);
    glEnd();
}

void truck()
{
    glPushMatrix();
    glTranslatef(_moveTruck, 0.0f, 0.0f);  // Move truck based on _moveTruck variable
    truck_glass();
    truck_front();
    truck_box();
    truck_wheel(0.8, -14.0, 1.0);
    truck_wheel(0.8, -11.0, 1.0);
    glPopMatrix();
}

void car_wheel(float radius, float cX, float cY)
{
    glPushMatrix();
    glTranslatef(cX, cY, 0);
    glRotatef(_wheelAngle, 0.0f, 0.0f, 1.0f);  // Apply rotation based on _wheelAngle
    glTranslatef(-cX, -cY, 0);  // Translate back after rotation

    glBegin(GL_POLYGON);
    for (int i = 0; i < 200; i++)
    {
        glColor3ub(20, 20, 20);
        float pi = 3.1416;
        float A = (i * 2 * pi) / 200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();

    glPopMatrix();
}

void car_body()
{
    glBegin(GL_POLYGON);
    glColor3ub(105, 105, 105);
    glVertex2f(7.0f, -4.0f);
    glVertex2f(7.0f, -3.0f);
    glVertex2f(13.0f, -3.0f);
    glVertex2f(13.0f, -4.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1, 1, 1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(7.0f, -4.0f);
    glVertex2f(7.0f, -3.0f);
    glVertex2f(13.0f, -3.0f);
    glVertex2f(13.0f, -4.0f);
    glEnd();
}

void car_glass()
{
    glBegin(GL_POLYGON);
    glColor3ub(173, 216, 230);
    glVertex2f(8.0f, -3.0f);
    glVertex2f(9.0f, -2.0f);
    glVertex2f(11.0f, -2.0f);
    glVertex2f(12.0f, -3.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1, 1, 1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(8.0f, -3.0f);
    glVertex2f(9.0f, -2.0f);
    glVertex2f(11.0f, -2.0f);
    glVertex2f(12.0f, -3.0f);
    glEnd();
}

void car()
{
    glPushMatrix();
    glTranslatef(_moveCar, 0.0f, 0.0f);  // Move car based on _moveCar variable
    car_body();
    car_glass();
    car_wheel(0.8, 9.0, -4.0);
    car_wheel(0.8, 11.0, -4.0);
    glPopMatrix();
}

void road2()
{
     glBegin(GL_POLYGON);
    glColor3ub(54, 69, 79);
    glVertex2f(0.0f, -5.0f);
    glVertex2f(0.0f, -25.0f);
    glVertex2f(4.0f, -25.0f);
    glVertex2f(4.0f, -5.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(54, 69, 79);
    glBegin(GL_LINE_LOOP);
    glVertex2f(0.0f, -5.0f);
    glVertex2f(0.0f, -25.0f);
    glVertex2f(4.0f, -25.0f);
    glVertex2f(4.0f, -5.0f);
    glEnd();
}
void green_land()
{
     glBegin(GL_POLYGON);
    glColor3ub(144, 238, 144);
    glVertex2f(-30.0f, -25.0f);
    glVertex2f(-30.0f, -5.0f);
    glVertex2f(0.0f, -5.0f);
    glVertex2f(0.0f, -25.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-30.0f, -25.0f);
    glVertex2f(-30.0f, -5.0f);
    glVertex2f(0.0f, -5.0f);
    glVertex2f(0.0f, -25.0f);
    glEnd();
}

void road3()
{
      glBegin(GL_POLYGON);
    glColor3ub(54, 69, 79);
    glVertex2f(-30.0f, -17.0f);
    glVertex2f(0.0f, -17.0f);
    glVertex2f(0.0f, -20.0f);
    glVertex2f(-30.0f, -20.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(54, 69, 79);
    glBegin(GL_LINE_LOOP);
      glVertex2f(-30.0f, -17.0f);
    glVertex2f(0.0f, -17.0f);
    glVertex2f(0.0f, -20.0f);
    glVertex2f(-30.0f, -20.0f);
    glEnd();
}
void small_tree()
{
    glLineWidth(2);
    glColor3ub(160, 82, 4);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-27.0f, -25.0f);
    glVertex2f(-27.0f, -23.0f);
    glEnd();

    glBegin(GL_POLYGON);
    glColor3ub(34, 139, 34);
    glVertex2f(-28.0f, -23.0f);
    glVertex2f(-27.0f, -22.0f);
    glVertex2f(-26.0f, -23.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-28.0f, -23.0f);
    glVertex2f(-27.0f, -22.0f);
    glVertex2f(-26.0f, -23.0f);
    glEnd();

     glBegin(GL_POLYGON);
    glColor3ub(34, 139, 34);
    glVertex2f(-28.0f, -22.0f);
    glVertex2f(-27.0f, -21.0f);
    glVertex2f(-26.0f, -22.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
      glVertex2f(-28.0f, -22.0f);
    glVertex2f(-27.0f, -21.0f);
    glVertex2f(-26.0f, -22.0f);
    glEnd();

}
void small_trees()
{
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(0.0,0.0,0);
    small_tree();
    glPopMatrix();

     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(3.0,0.0,0);
    small_tree();
    glPopMatrix();

     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(6.0,0.0,0);
    small_tree();
    glPopMatrix();

     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(9.0,0.0,0);
    small_tree();
    glPopMatrix();

     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(12.0,0.0,0);
    small_tree();
    glPopMatrix();

     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(15.0,0.0,0);
    small_tree();
    glPopMatrix();

      glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(18.0,0.0,0);
    small_tree();
    glPopMatrix();

      glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(21.0,0.0,0);
    small_tree();
    glPopMatrix();

      glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(24.0,0.0,0);
    small_tree();
    glPopMatrix();

}

void river()
{
      glBegin(GL_POLYGON);
    glColor3ub(176, 196, 222);
    glVertex2f(4.0f, -5.0f);
    glVertex2f(4.0f, -25.0f);
    glVertex2f(30.0f, -25.0f);
     glVertex2f(30.0f, -5.0f);
    glEnd();
      glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(4.0f, -5.0f);
    glVertex2f(4.0f, -25.0f);
    glVertex2f(30.0f, -25.0f);
     glVertex2f(30.0f, -5.0f);
    glEnd();
}
void boat_body()
{
     glBegin(GL_POLYGON);
    glColor3ub(139, 69, 19);
    glVertex2f(10.0f, -15.0f);
    glVertex2f(11.0f, -16.0f);
    glVertex2f(14.0f, -16.0f);
     glVertex2f(15.0f, -15.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(10.0f, -15.0f);
    glVertex2f(11.0f, -16.0f);
    glVertex2f(14.0f, -16.0f);
     glVertex2f(15.0f, -15.0f);
    glEnd();
}

void boat_semi_circle(float r,float x,float y)
{
      glBegin(GL_POLYGON);

    glColor3ub(211, 211, 211);

    // Define semi-circle
    float radius = r;
    float centerX = x; // X coordinate of the center
    float centerY = y; // Y coordinate of the center
    float pi = 3.14159265359;

    for (int i = 0; i <= 180; i++) {
        float angle = i * pi / 180.0;
        glVertex2f(centerX + cos(angle) * radius, centerY + sin(angle) * radius);
    }

    glEnd();

}

void boat1()
{
     glPushMatrix();
    glTranslatef(_moveBoat, 0.0f, 0.0f);  // Move truck based on _moveBoat variable
    boat_semi_circle(1.5,12.5,-15.0);
    boat_body();
    glPopMatrix();
}
void big_tree()
{
    glBegin(GL_POLYGON);
    glColor3ub(139, 69, 19);
    glVertex2f(-27.0f, -17.0f);
    glVertex2f(-27.0f, -11.0f);
    glVertex2f(-26.0f, -11.0f);
     glVertex2f(-26.0f, -17.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-27.0f, -17.0f);
    glVertex2f(-27.0f, -11.0f);
    glVertex2f(-26.0f, -11.0f);
     glVertex2f(-26.0f, -17.0f);
    glEnd();

    tree_circle(2.0,-27.5f, -11.0f);
    tree_circle(2.0,-25.5f, -11.0f);
    tree_circle(2.0,-26.5f, -9.0f);
}
void house1_body()
{
    glBegin(GL_POLYGON);
    glColor3ub(245, 245, 220);
    glVertex2f(-24.0f, -17.0f);
    glVertex2f(-24.0f, -15.0f);
    glVertex2f(-17.0f, -15.0f);
    glVertex2f(-17.0f, -17.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-24.0f, -17.0f);
    glVertex2f(-24.0f, -15.0f);
    glVertex2f(-17.0f, -15.0f);
    glVertex2f(-17.0f, -17.0f);
    glEnd();
}
void house1_roof()
{
    glBegin(GL_POLYGON);
    glColor3ub(178, 34, 34);
    glVertex2f(-24.0f, -15.0f);
    glVertex2f(-24.0f, -14.0f);
    glVertex2f(-17.0f, -13.0f);
    glVertex2f(-17.0f, -15.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-24.0f, -15.0f);
    glVertex2f(-24.0f, -14.0f);
    glVertex2f(-17.0f, -13.0f);
    glVertex2f(-17.0f, -15.0f);
    glEnd();
}
void house1()
{
    house1_roof();
    house1_body();
      glBegin(GL_POLYGON);
    glColor3ub(1,1,1);
    glVertex2f(-22.0f, -17.0f);
    glVertex2f(-22.0f, -16.0f);
    glVertex2f(-19.0f, -16.0f);
    glVertex2f(-19.0f, -17.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-22.0f, -17.0f);
    glVertex2f(-22.0f, -16.0f);
    glVertex2f(-19.0f, -16.0f);
    glVertex2f(-19.0f, -17.0f);
    glEnd();
}
void house2()
{
    //body
     glBegin(GL_POLYGON);
    glColor3ub(245, 245, 220);
    glVertex2f(-17.0f, -17.0f);
    glVertex2f(-17.0f, -15.0f);
    glVertex2f(-12.0f, -15.0f);
    glVertex2f(-12.0f, -17.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-17.0f, -17.0f);
    glVertex2f(-17.0f, -15.0f);
    glVertex2f(-12.0f, -15.0f);
    glVertex2f(-12.0f, -17.0f);
    glEnd();

    //roof
     glBegin(GL_POLYGON);
    glColor3ub(178, 34, 34);
    glVertex2f(-17.0f, -15.0f);
    glVertex2f(-17.0f, -13.5f);
    glVertex2f(-12.0f, -13.5f);
    glVertex2f(-12.0f, -15.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-17.0f, -15.0f);
    glVertex2f(-17.0f, -13.5f);
    glVertex2f(-12.0f, -13.5f);
    glVertex2f(-12.0f, -15.0f);
    glEnd();

    // Gate
     glBegin(GL_POLYGON);
    glColor3ub(255,255,255);
    glVertex2f(-16.0f, -17.0f);
    glVertex2f(-16.0f, -15.5f);
    glVertex2f(-14.0f, -15.5f);
    glVertex2f(-14.0f, -17.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-16.0f, -17.0f);
    glVertex2f(-16.0f, -15.5f);
    glVertex2f(-14.0f, -15.5f);
    glVertex2f(-14.0f, -17.0f);
    glEnd();
}
void grass_brush(float r,float x,float y)
{
      glBegin(GL_POLYGON);

    glColor3ub(218, 165, 32);

    // Define semi-circle
    float radius = r;
    float centerX = x; // X coordinate of the center
    float centerY = y; // Y coordinate of the center
    float pi = 3.14159265359;

    for (int i = 0; i <= 180; i++) {
        float angle = i * pi / 180.0;
        glVertex2f(centerX + cos(angle) * radius, centerY + sin(angle) * radius);
    }

    glEnd();

}
void wind_mill_body()
{
     glBegin(GL_POLYGON);
    glColor3ub(211, 211, 211);
    glVertex2f(-8.0f, -17.0f);
    glVertex2f(-8.0f, -10.5f);
    glVertex2f(-7.0f, -10.5f);
    glVertex2f(-7.0f, -17.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
   glVertex2f(-8.0f, -17.0f);
    glVertex2f(-8.0f, -10.5f);
    glVertex2f(-7.0f, -10.5f);
    glVertex2f(-7.0f, -17.0f);
    glEnd();
}

void windmill_circle(float radius, float cX, float cY)
{
    glPushMatrix();
    glTranslatef(cX, cY, 0);
    glRotatef(_windMill, 0.0f, 0.0f, 1.0f);  // Apply rotation based on _wheelAngle
    glTranslatef(-cX, -cY, 0);  // Translate back after rotation

    glBegin(GL_POLYGON);
    for (int i = 0; i < 200; i++)
    {
        glColor3ub(153, 0, 0);
        float pi = 3.1416;
        float A = (i * 2 * pi) / 200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();

    glPopMatrix();
}
void wind_mill()
{
   wind_mill_body();

    // Apply rotation to the windmill fans
    glPushMatrix();
    glTranslatef(-7.5, -9.5, 0);  // Move to the windmill center
    glRotatef(_windMill, 0.0f, 0.0f, 1.0f);  // Rotate around the center
    glTranslatef(7.5, 9.5, 0);  // Move back

    // Fan1
    glLineWidth(5.0);
    glColor3ub(133, 94, 66);
    glBegin(GL_LINES);
    glVertex2f(-11.0, -13.0);
    glVertex2f(-4.0, -6.0);
    glEnd();

    // Fan2
    glLineWidth(5.0);
    glColor3ub(133, 94, 66);
    glBegin(GL_LINES);
    glVertex2f(-4.0, -13.0);
    glVertex2f(-11.0, -6.0);
    glEnd();

    glPopMatrix();

    // Windmill circle at the center
    windmill_circle(1.2, -7.5, -9.5);

}
void house3_gate(float r,float x,float y)
{
      glBegin(GL_POLYGON);

    glColor3ub(1,1,1);

    // Define semi-circle
    float radius = r;
    float centerX = x; // X coordinate of the center
    float centerY = y; // Y coordinate of the center
    float pi = 3.14159265359;

    for (int i = 0; i <= 180; i++) {
        float angle = i * pi / 180.0;
        glVertex2f(centerX + cos(angle) * radius, centerY + sin(angle) * radius);
    }

    glEnd();

}
void house3()
{
    //body
    glBegin(GL_POLYGON);
    glColor3ub(245, 245, 220);
    glVertex2f(-5.0f, -17.0f);
    glVertex2f(-5.0f, -15.0f);
    glVertex2f(-1.0f, -15.0f);
    glVertex2f(-1.0f, -17.0f);
    glEnd();
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-5.0f, -17.0f);
    glVertex2f(-5.0f, -15.0f);
    glVertex2f(-1.0f, -15.0f);
    glVertex2f(-1.0f, -17.0f);
    glEnd();

    //Roof
    glBegin(GL_POLYGON);
    glColor3ub(178, 34, 34);
    glVertex2f(-6.0f, -15.0f);
    glVertex2f(-3.0f, -12.0f);
    glVertex2f(-0.0f, -15.0f);
    glEnd();
     glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-6.0f, -15.0f);
    glVertex2f(-3.0f, -12.0f);
    glVertex2f(-0.0f, -15.0f);
    glEnd();

    house3_gate(1.0,-3.0,-17.0);
}

void muddy_space_river()
{
    glBegin(GL_POLYGON);
    glColor3ub(102, 76, 51);
   glVertex2f(4.0f, -5.0f);
   glVertex2f(30.0f, -5.0f);
   glVertex2f(30.0f, -6.0f);
   glVertex2f(4.0f, -6.0f);
   glEnd();
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
   glVertex2f(4.0f, -5.0f);
   glVertex2f(30.0f, -5.0f);
   glVertex2f(30.0f, -6.0f);
   glVertex2f(4.0f, -6.0f);
   glEnd();
    glBegin(GL_POLYGON);
    glColor3ub(102, 76, 51);
   glVertex2f(4.0f, -25.0f);
   glVertex2f(4.0f, -5.0f);
   glVertex2f(5.0f, -5.0f);
   glVertex2f(5.0f, -25.0f);
   glEnd();
    glLineWidth(1);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
   glVertex2f(4.0f, -25.0f);
   glVertex2f(4.0f, -5.0f);
   glVertex2f(5.0f, -5.0f);
   glVertex2f(5.0f, -25.0f);
   glEnd();
}

// Village_view function (extracted from village_view.txt)
void Village_view() {
    // Place village drawing code here
    // Example from your village_view.txt
    glClear(GL_COLOR_BUFFER_BIT);
     // Draw sky, road, hills, etc.
    sky();
    // Green Land
    green_land();
    //River
    river();
    // Mudd Space
    muddy_space_river();
    line1();
    main_street();
    hill1();
    hill2();
    hill3();
    hillBorder();
    hill_left_Part();
    hill_right_Part();

    // Draw sun, clouds, and trees
     sun(2.0, -10.0, sunPosY);
    cloud1();
    clude2();
    trees();

    // Draw moving truck and car
    truck();
    car();

    // Road2
    road2();
    road3();

    // SMall Trees
    small_trees();

    //Big Tree
    big_tree();

    // House_01
    house1();
    house2();
    house3();

    grass_brush(2,-10.0,-17.0);

    //Wind Mill
    wind_mill();

    //boat
    boat1();
    // Add other drawing functions
    glFlush();
}

// Road Function
void Road() {
    glLineWidth(5);
    glColor3ub(96, 96, 96);
    glBegin(GL_POLYGON);
    glVertex2f(-30.0f, 7.0f);
    glVertex2f(0.0f, 7.0f);
    glVertex2f(30.0f, 7.0f);
    glVertex2f(30.0f, 0.0f);
    glVertex2f(30.0f, -7.0f);
    glVertex2f(0.0f, -7.0f);
    glVertex2f(-30.0f, -7.0f);
    glVertex2f(-30.0f, 0.0f);
    glEnd();

    glLineWidth(1.5);
    glColor3ub(0, 0, 0);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-30.0f, 7.0f);
    glVertex2f(0.0f, 7.0f);
    glVertex2f(30.0f, 7.0f);
    glVertex2f(30.0f, 0.0f);
    glVertex2f(30.0f, -7.0f);
    glVertex2f(0.0f, -7.0f);
    glVertex2f(-30.0f, -7.0f);
    glVertex2f(-30.0f, 0.0f);
    glEnd();
}

void c_sky() {
    glColor3ub(25, 25, 112);
    glBegin(GL_POLYGON);
    glVertex2f(-30.0f, 10.0f);
    glVertex2f(-30.0f, 35.0f);
    glVertex2f(30.0f, 35.0f);
    glVertex2f(30.0f, 0.0f);
    glEnd();
}

void moon(float radius, float cX, float cY) {
    glBegin(GL_POLYGON);
    for(int i=0; i<200; i++) {
        glColor3ub(220, 220, 220);
        float pi = 3.1416;
        float A = (i*2*pi)/200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();
}

void c_cloud_circle(float radius, float cX, float cY) {
    glBegin(GL_POLYGON);
    for(int i=0; i<200; i++) {
        glColor3ub(220, 220, 220);
        float pi = 3.1416;
        float A = (i*2*pi)/200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();
}

void RoadDevider() {
    glLineWidth(5);
    glColor3ub(255, 255, 255);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-30.0f, 0.0f);
    glVertex2f(0.0f, 0.0f);
    glVertex2f(30.0f, 0.0f);
    glEnd();
}

void side_walk1() {
    glColor3ub(128, 128, 128);
    glBegin(GL_POLYGON);
    glVertex2f(-30.0f, 7.0f);
    glVertex2f(-30.0f, 10.0f);
    glVertex2f(30.0f, 10.0f);
    glVertex2f(30.0f, 7.0f);
    glEnd();
    // Border
    glLineWidth(0.5);
    glColor3ub(255, 255, 255);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-30.0f, 7.0f);
    glVertex2f(-30.0f, 10.0f);
    glVertex2f(30.0f, 10.0f);
    glVertex2f(30.0f, 7.0f);
    glEnd();
}

void side_walk2() {
    glColor3ub(128, 128, 128);
    glBegin(GL_POLYGON);
    glVertex2f(-30.0f, -7.0f);
    glVertex2f(-30.0f, -10.0f);
    glVertex2f(30.0f, -10.0f);
    glVertex2f(30.0f, -7.0f);
    glEnd();
    // Border
    glLineWidth(0.5);
    glColor3ub(255, 255, 255);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-30.0f, -7.0f);
    glVertex2f(-30.0f, -10.0f);
    glVertex2f(30.0f, -10.0f);
    glVertex2f(30.0f, -7.0f);
    glEnd();
}

void c_car_wheel(float radius, float cX, float cY) {
    glBegin(GL_POLYGON);
    for (int i=0; i<200; i++) {
        glColor3ub(10, 10, 10);
        float pi = 3.1416;
        float A = (i*2*pi)/200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();
}

void car_body1() {
    glColor3ub(20, 20, 20);
    glBegin(GL_POLYGON);
    glVertex2f(-12.0f, 3.0f);
    glVertex2f(-12.0f, 5.0f);
    glVertex2f(-5.0f, 5.0f);
    glVertex2f(-5.0f, 3.0f);
    glEnd();

    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-12.0f, 3.0f);
    glVertex2f(-12.0f, 5.0f);
    glVertex2f(-5.0f, 5.0f);
    glVertex2f(-5.0f, 3.0f);
    glEnd();
}

void car_roof1() {
    glColor3ub(25, 25, 112);
    glBegin(GL_POLYGON);
    glVertex2f(-11.0f, 5.0f);
    glVertex2f(-10.0f, 6.0f);
    glVertex2f(-7.0f, 6.0f);
    glVertex2f(-6.0f, 5.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-11.0f, 5.0f);
    glVertex2f(-10.0f, 6.0f);
    glVertex2f(-7.0f, 6.0f);
    glVertex2f(-6.0f, 5.0f);
    glEnd();
}

void car1() {
    glPushMatrix();
    glTranslatef(carPosX, 0.0f, 0.0f);  // Move the car along the x-axis
    car_roof1();
    car_body1();
    c_car_wheel(0.9, -10.0, 3.0);
    car_wheel(0.9, -7.0, 3.0);
    glPopMatrix();
}

void car_roof2()
{
    glColor3ub(100, 149, 237);
    glBegin(GL_POLYGON);
    glVertex2f(4.0f, -3.0f);
    glVertex2f(5.0f, -2.0f);
    glVertex2f(8.0f,-2.0f);
    glVertex2f(9.0f, -3.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(4.0f, -3.0f);
    glVertex2f(5.0f, -2.0f);
    glVertex2f(8.0f,-2.0f);
    glVertex2f(9.0f, -3.0f);
    glEnd();
}
void car_body2()
{
    glColor3ub(255, 165, 0);
    glBegin(GL_POLYGON);
    glVertex2f(3.0f, -5.0f);
    glVertex2f(3.0f, -3.0f);
    glVertex2f(10.0f, -3.0f);
    glVertex2f(10.0f, -5.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(3.0f, -5.0f);
    glVertex2f(3.0f, -3.0f);
    glVertex2f(10.0f, -3.0f);
    glVertex2f(10.0f, -5.0f);
    glEnd();

}
void car2()
{
    car_roof2();
    car_body2();
    c_car_wheel(0.9,5.0, -5.0);
    c_car_wheel(0.9, 8.0, -5.0);
}
void lamp_body()
{
    glLineWidth(4.0);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-25.0f, -9.0f);
    glVertex2f(-25.0f, -4.0f);
    glEnd();
}
void lamp_light()
{

    glLineWidth(4);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-27.0f, -4.0f);
    glVertex2f(-23.0f, -4.0f);
    glEnd();
}

void lamp_Light(float radius, float cX, float cY) {
    glBegin(GL_POLYGON);
    for (int i = 0; i < 200; i++) {
        if (lampState == 0) {
            glColor3ub(255, 223, 99);  // Deep yellow
        } else {
            glColor3ub(255, 255, 150);  // Light yellow
        }

        float pi = 3.1416;
        float A = (i * 2 * pi) / 200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();
}


void lamp_ground(float radius, float cX, float cY) {
    glBegin(GL_POLYGON);
    for (int i=0; i<200; i++) {
        glColor3ub(10, 10, 10);
        float pi = 3.1416;
        float A = (i*2*pi)/200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();
}

void lamp1()
{
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(0,0,0);

    lamp_light();
    lamp_body();
    lamp_ground(0.4,-25.0,-9.0);
    lamp_Light(1.2,-27.0f, -4.0f);
    lamp_Light(1.2,-23.0f, -4.0f);

    glPopMatrix();

}
void lamp2()
{
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(20.0,0,0);

    lamp_light();
    lamp_body();
    lamp_ground(0.4,-25.0,-9.0);
     lamp_Light(1.2,-27.0f, -4.0f);
    lamp_Light(1.2,-23.0f, -4.0f);
    glPopMatrix();
}
void lamp3()
{
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(40.0,0,0);

    lamp_light();
    lamp_body();
    lamp_ground(0.4,-25.0,-9.0);
     lamp_Light(1.2,-27.0f, -4.0f);
    lamp_Light(1.2,-23.0f, -4.0f);

    glPopMatrix();
}
void glass_building1()
{
    glColor3ub(176, 196, 222);
    glBegin(GL_POLYGON);
    glVertex2f(-25.0f, -25.0f);
    glVertex2f(-25.0f, -11.0f);
    glVertex2f(-15.0f, -11.0f);
    glVertex2f(-15.0f, -25.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-25.0f, -25.0f);
    glVertex2f(-25.0f, -11.0f);
    glVertex2f(-15.0f, -11.0f);
    glVertex2f(-15.0f, -25.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(255,255,255);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-20.0f, -24.0f);
    glVertex2f(-20.0f, -12.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(255,255,255);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-24.0f, -18.0f);
    glVertex2f(-16.0f, -18.0f);
    glEnd();
}
void school()
{
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(0,0,0);
    glass_building1();
    glPopMatrix();
}
void school2()
{
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(40.0,0,0);
    glass_building1();
    glPopMatrix();
}
void renderBitmapString(float x, float y, void *font, const char *string) {
    const char *c;
    glRasterPos2f(x, y); // Set the position where the text will be rendered
    for (c = string; *c != '\0'; c++) {
        glutBitmapCharacter(font, *c); // Render each character
    }
}
void school3_signBoard()
{
    glColor3ub(211, 211, 211);
    glBegin(GL_POLYGON);
    glVertex2f(-5.0f, -17.0f);
    glVertex2f(-5.0f, -13.0f);
    glVertex2f(5.0f, -13.0f);
    glVertex2f(5.0f, -17.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-5.0f, -17.0f);
    glVertex2f(-5.0f, -13.0f);
    glVertex2f(5.0f, -13.0f);
    glVertex2f(5.0f, -17.0f);
    glEnd();
    glColor3f(0.0, 0.0, 0.0); // Set text color to black
    renderBitmapString(-4.0f, -15.0f, GLUT_BITMAP_8_BY_13, "Samio School");
}

void school3_window()
{
    glColor3ub(windowColor[0], windowColor[1], windowColor[2]);
    glBegin(GL_POLYGON);
    glVertex2f(-14.0f, -18.0f);
    glVertex2f(-14.0f, -16.0f);
    glVertex2f(-12.0f, -16.0f);
    glVertex2f(-12.0f, -18.0f);
    glEnd();

     glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-14.0f, -18.0f);
    glVertex2f(-14.0f, -16.0f);
    glVertex2f(-12.0f, -16.0f);
    glVertex2f(-12.0f, -18.0f);
    glEnd();
}
void school3()
{
    glColor3ub(139, 69, 19);
    glBegin(GL_POLYGON);
    glVertex2f(-15.0f, -25.0f);
    glVertex2f(-15.0f, -15.0f);
    glVertex2f(15.0f, -15.0f);
    glVertex2f(15.0f, -25.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-15.0f, -25.0f);
    glVertex2f(-15.0f, -15.0f);
    glVertex2f(15.0f, -15.0f);
    glVertex2f(15.0f, -25.0f);
    glEnd();
    school3_signBoard();
    //school gate
     glColor3ub(255, 255, 255);
    glBegin(GL_POLYGON);
    glVertex2f(-4.0f, -25.0f);
    glVertex2f(-4.0f, -21.0f);
    glVertex2f(4.0f, -21.0f);
    glVertex2f(4.0f, -25.0f);
    glEnd();
    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-4.0f, -25.0f);
    glVertex2f(-4.0f, -21.0f);
    glVertex2f(4.0f, -21.0f);
    glVertex2f(4.0f, -25.0f);
    glEnd();

    //school window
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(0,0,0);
    school3_window();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(0,-6.0,0);
    school3_window();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(6.0,0,0);
    school3_window();
    glPopMatrix();

     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(6.0,-6.0,0);
    school3_window();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(21.0,0,0);
    school3_window();
    glPopMatrix();

     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(21.0,-6.0,0);
    school3_window();
    glPopMatrix();

    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(26.0,0,0);
    school3_window();
    glPopMatrix();

       glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(26.0,-6.0,0);
    school3_window();
    glPopMatrix();


}

void drawTreeCircle(float radius, float cX, float cY) {
    glBegin(GL_POLYGON);
    for (int i = 0; i < 200; i++) {
        float pi = 3.1416;
        float theta = (i * 2 * pi) / 200;
        float x = radius * cos(theta);
        float y = radius * sin(theta);
        glVertex2f(x + cX + leafShakeOffset, y + cY);  // Apply shaking offset
    }
    glEnd();
}

void c_tree_circle(float radius, float cX, float cY) {
    glBegin(GL_POLYGON);
    for (int i=0; i<200; i++) {
        glColor3ub(34, 139, 34);
        float pi = 3.1416;
        float A = (i*2*pi)/200;
        float r = radius;
        float x = r * cos(A);
        float y = r * sin(A);
        glVertex2f(x + cX, y + cY);
    }
    glEnd();
}

// Function to draw the tree trunk
void drawTreeTrunk(float x, float y) {
    glColor3ub(160, 82, 4);  // Brown for trunk
    glBegin(GL_POLYGON);
    glVertex2f(x - 0.5f, y);
    glVertex2f(x + 0.5f, y);
    glVertex2f(x + 0.5f, y + 3.0f);
    glVertex2f(x - 0.5f, y + 3.0f);
    glEnd();
}

// School tree 1 with shaking leaves
void school_tree_1() {
    float x = -28.0f, y = -25.0f;
    drawTreeTrunk(x, y);  // Draw the trunk
    glColor3ub(34, 139, 34);  // Green for leaves
    drawTreeCircle(1.7f, x, y + 5.0f);  // Draw first circle leaf with shake
    drawTreeCircle(1.7f, x, y + 3.0f);  // Draw second circle leaf with shake
}

// School tree 2 with shaking leaves
void school_tree_2() {
    float x = 28.0f, y = -25.0f;
    drawTreeTrunk(x, y);  // Draw the trunk
    glColor3ub(34, 139, 34);  // Green for leaves
    drawTreeCircle(1.7f, x, y + 5.0f);  // Draw first circle leaf with shake
    drawTreeCircle(1.7f, x, y + 3.0f);  // Draw second circle leaf with shake
}

void road_tree1()
{
     glLineWidth(4);
    glColor3ub(160, 82, 4);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-15.0f, -9.0f);
    glVertex2f(-15.0f, -4.0f);
    glEnd();
    c_tree_circle(1.7,-15.0, -4.0);
    c_tree_circle(1.7,-15.0, -2.0);
}
void road_tree2()
{
     glLineWidth(4);
    glColor3ub(160, 82, 4);
    glBegin(GL_LINE_LOOP);
    glVertex2f(25.0f, -9.0f);
    glVertex2f(25.0f, -4.0f);
    glEnd();
    c_tree_circle(1.7,25.0, -4.0);
    c_tree_circle(1.7,25.0, -2.0);
}



// All Building Code Here
void building1()
{
    glColor3ub(238, 232, 170);
    glBegin(GL_POLYGON);
    glVertex2f(-25.0f, 10.0f);
    glVertex2f(-25.0f, 15.0f);
    glVertex2f(-15.0f, 15.0f);
    glVertex2f(-15.0f, 10.0f);
    glEnd();

    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-25.0f, 10.0f);
    glVertex2f(-25.0f, 15.0f);
    glVertex2f(-15.0f, 15.0f);
    glVertex2f(-15.0f, 10.0f);
    glEnd();

    // Roof Top
     glColor3ub(178, 34, 34);
    glBegin(GL_POLYGON);
    glVertex2f(-25.0f, 15.0f);
    glVertex2f(-27.0f, 15.0f);
    glVertex2f(-27.0f, 17.0f);
    glVertex2f(-25.0f, 19.0f);
    glVertex2f(-15.0f, 19.0f);
    glVertex2f(-13.0f, 17.0f);
    glVertex2f(-13.0f, 15.0f);
    glVertex2f(-25.0f, 15.0f);
    glEnd();

     glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-25.0f, 15.0f);
    glVertex2f(-27.0f, 15.0f);
    glVertex2f(-27.0f, 17.0f);
    glVertex2f(-25.0f, 19.0f);
    glVertex2f(-15.0f, 19.0f);
    glVertex2f(-13.0f, 17.0f);
    glVertex2f(-13.0f, 15.0f);
    glVertex2f(-25.0f, 15.0f);
    glEnd();

    //Door
    glColor3ub(173, 216, 230);
    glBegin(GL_POLYGON);
    glVertex2f(-24.0f, 10.0f);
    glVertex2f(-24.0f, 14.0f);
    glVertex2f(-21.0f, 14.0f);
    glVertex2f(-21.0f, 10.0f);
    glEnd();

     glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
     glVertex2f(-24.0f, 10.0f);
    glVertex2f(-24.0f, 14.0f);
    glVertex2f(-21.0f, 14.0f);
    glVertex2f(-21.0f, 10.0f);
    glEnd();

    //Window
    glColor3ub(255,255,255);
    glBegin(GL_POLYGON);
    glVertex2f(-19.0f, 12.0f);
    glVertex2f(-19.0f, 14.0f);
    glVertex2f(-16.0f, 14.0f);
    glVertex2f(-16.0f, 12.0f);
    glEnd();

    glLineWidth(0.5);
    glColor3ub(1,1,1);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-19.0f, 12.0f);
    glVertex2f(-19.0f, 14.0f);
    glVertex2f(-16.0f, 14.0f);
    glVertex2f(-16.0f, 12.0f);
    glEnd();

}

void play_ground()
{
    glColor3ub(188, 184, 138);
    glBegin(GL_POLYGON);
    glVertex2f(-30.0f, -25.0f);
    glVertex2f(-30.0f, -10.0f);
    glVertex2f(30.0f, -10.0f);
    glVertex2f(30.0f, -25.0f);
    glEnd();
}

// House_Building
void full_build2()
{
    glColor3ub(216, 216, 216);
    glBegin(GL_POLYGON);
    glVertex2f(-5.0f, 10.0f);
    glVertex2f(-5.0f, 25.0f);
    glVertex2f(5.0f, 25.0f);
    glVertex2f(5.0f, 10.0f);
    glEnd();
}

void full_build2_border()
{
     glLineWidth(0.5);
    glColor3ub(0, 0, 0);
    glBegin(GL_LINE_LOOP);
    glVertex2f(-5.0f, 10.0f);
    glVertex2f(-5.0f, 25.0f);
    glVertex2f(5.0f, 25.0f);
    glVertex2f(5.0f, 10.0f);
    glEnd();
}

void building2_window1()
{
     glColor3ub(192, 192, 192);
     glBegin(GL_POLYGON);
     glVertex2f(-4.0f, 12.0f);
     glVertex2f(-4.0f, 14.0f);
     glVertex2f(-2.0f, 14.0f);
     glVertex2f(-2.0f, 12.0f);
     glEnd();
     glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
     glVertex2f(-4.0f, 12.0f);
     glVertex2f(-4.0f, 14.0f);
     glVertex2f(-2.0f, 14.0f);
     glVertex2f(-2.0f, 12.0f);
     glEnd();
}
void building2_window2()
{
     glColor3ub(192, 192, 192);
     glBegin(GL_POLYGON);
     glVertex2f(2.0f, 12.0f);
     glVertex2f(2.0f, 14.0f);
     glVertex2f(4.0f, 14.0f);
     glVertex2f(4.0f, 12.0f);
     glEnd();
      glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
     glVertex2f(2.0f, 12.0f);
     glVertex2f(2.0f, 14.0f);
     glVertex2f(4.0f, 14.0f);
     glVertex2f(4.0f, 12.0f);
     glEnd();
}
void building2_window3()
{
     glColor3ub(192, 192, 192);
     glBegin(GL_POLYGON);
     glVertex2f(-4.0f, 17.0f);
     glVertex2f(-4.0f, 19.0f);
     glVertex2f(-2.0f, 19.0f);
     glVertex2f(-2.0f, 17.0f);
     glEnd();
      glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
    glVertex2f(-4.0f, 17.0f);
     glVertex2f(-4.0f, 19.0f);
     glVertex2f(-2.0f, 19.0f);
     glVertex2f(-2.0f, 17.0f);
     glEnd();
}
void building2_window4()
{
     glColor3ub(192, 192, 192);
     glBegin(GL_POLYGON);
     glVertex2f(2.0f, 17.0f);
     glVertex2f(2.0f, 19.0f);
     glVertex2f(4.0f, 19.0f);
     glVertex2f(4.0f, 17.0f);
     glEnd();
      glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
    glVertex2f(2.0f, 17.0f);
     glVertex2f(2.0f, 19.0f);
     glVertex2f(4.0f, 19.0f);
     glVertex2f(4.0f, 17.0f);
     glEnd();
}
void building2_window5()
{
     glColor3ub(192, 192, 192);
     glBegin(GL_POLYGON);
     glVertex2f(-4.0f, 22.0f);
     glVertex2f(-4.0f, 24.0f);
     glVertex2f(-2.0f, 24.0f);
     glVertex2f(-2.0f, 22.0f);
     glEnd();
      glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
    glVertex2f(-4.0f, 22.0f);
     glVertex2f(-4.0f, 24.0f);
     glVertex2f(-2.0f, 24.0f);
     glVertex2f(-2.0f, 22.0f);
     glEnd();
}
void building2_window6()
{
     glColor3ub(192, 192, 192);
     glBegin(GL_POLYGON);
     glVertex2f(2.0f, 22.0f);
     glVertex2f(2.0f, 24.0f);
     glVertex2f(4.0f, 24.0f);
     glVertex2f(4.0f, 22.0f);
     glEnd();
      glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
   glVertex2f(2.0f, 22.0f);
     glVertex2f(2.0f, 24.0f);
     glVertex2f(4.0f, 24.0f);
     glVertex2f(4.0f, 22.0f);
     glEnd();
}
void building2_Door()
{
     glColor3ub(50, 50, 50);
     glBegin(GL_POLYGON);
     glVertex2f(-1.0f, 10.0f);
     glVertex2f(-1.0f, 12.0f);
     glVertex2f(1.0f, 12.0f);
     glVertex2f(1.0f, 10.0f);
     glEnd();
      glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
   glVertex2f(-1.0f, 10.0f);
     glVertex2f(-1.0f, 12.0f);
     glVertex2f(1.0f, 12.0f);
     glVertex2f(1.0f, 10.0f);
     glEnd();
}


void Building2()
{
     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(-5.0,0,0);
    //
   full_build2();
   full_build2_border();
   building2_window1();
   building2_window2();
   building2_window3();
   building2_window4();
   building2_window5();
   building2_window6();
   building2_Door();
    glPopMatrix();

     glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glScalef(1.0,1.0,1.0);
    glTranslatef(8.0,0,0);
    //
   full_build2();
   full_build2_border();
   building2_window1();
   building2_window2();
   building2_window3();
   building2_window4();
   building2_window5();
   building2_window6();
   building2_Door();
    glPopMatrix();
}

void last_building()
{
     glColor3ub(245, 245, 220);
     glBegin(GL_POLYGON);
     glVertex2f(17.0f, 10.0f);
     glVertex2f(17.0f, 18.0f);
     glVertex2f(28.0f, 18.0f);
     glVertex2f(28.0f, 10.0f);
     glEnd();

      glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
      glVertex2f(17.0f, 10.0f);
     glVertex2f(17.0f, 18.0f);
     glVertex2f(28.0f, 18.0f);
     glVertex2f(28.0f, 10.0f);
     glEnd();

     //Door
      glColor3ub(173, 216, 230);
     glBegin(GL_POLYGON);
     glVertex2f(21.0f, 10.0f);
     glVertex2f(21.0f, 14.0f);
     glVertex2f(24.0f, 14.0f);
     glVertex2f(24.0f, 10.0f);
     glEnd();

       glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
      glVertex2f(21.0f, 10.0f);
     glVertex2f(21.0f, 14.0f);
     glVertex2f(24.0f, 14.0f);
     glVertex2f(24.0f, 10.0f);
     glEnd();
     // sign Board
       glColor3ub(211, 211, 211);
     glBegin(GL_POLYGON);
     glVertex2f(19.0f, 17.0f);
     glVertex2f(19.0f, 19.0f);
     glVertex2f(26.0f, 19.0f);
     glVertex2f(26.0f, 17.0f);
     glEnd();

       glLineWidth(0.5);
     glColor3ub(0, 0, 0);
     glBegin(GL_LINE_LOOP);
     glVertex2f(19.0f, 17.0f);
     glVertex2f(19.0f, 19.0f);
     glVertex2f(26.0f, 19.0f);
     glVertex2f(26.0f, 17.0f);
     glEnd();

     glColor3f(0.0, 0.0, 0.0); // Set text color to black
    renderBitmapString(21.0f, 17.5f, GLUT_BITMAP_8_BY_13, "Shop");

}

void c_cloud1()
{
    c_cloud_circle(1.5, -25.0 + cloudOffsetX, 30.0);
    c_cloud_circle(1.5, -26.5 + cloudOffsetX, 30.0);
    c_cloud_circle(1.5, -25.5 + cloudOffsetX, 31.0);
}

void c_cloud2()
{
    c_cloud_circle(1.5, 0.0 + cloudOffsetX, 30.0);
    c_cloud_circle(1.5, 1.5 + cloudOffsetX, 30.0);
    c_cloud_circle(1.5, 0.5 + cloudOffsetX, 31.0);
}

void c_cloud3()
{
    c_cloud_circle(1.5, -15.0 + cloudOffsetX, 30.0);
    c_cloud_circle(1.5, -16.5 + cloudOffsetX, 30.0);
    c_cloud_circle(1.5, -15.5 + cloudOffsetX, 31.0);
}


// City_view function (extracted from city_view.txt)
void City_view() {
    // Place city drawing code here
    glClear(GL_COLOR_BUFFER_BIT);
    // Sky
    c_sky();

    //Cloud
    c_cloud1();
    c_cloud2();
    c_cloud3();

    //Moon
    moon(2.0,20.0,moonY);

    // Play Ground
    play_ground();

    // Road
    Road();
    RoadDevider();

    // Sidewalk
    side_walk1();
    side_walk2();

    // Car
    car1();
    glPushMatrix();
    glTranslatef(carPosition, 0.0f, 0.0f);
    car2();  // Draw the car
    glPopMatrix();
    //Lamp-Post
    lamp1();
    lamp2();
    lamp3();

    //School
    school();
    school2();
    school3();

    // Tree1
    school_tree_1();
    school_tree_2();

    //Road Tree
    road_tree1();
    road_tree2();

    //All Buildings
    building1();
    Building2();
    last_building();
    // Add other drawing functions
    glFlush();
}

void updateMoon(int value)
{
    moonY += 0.02f; // Adjust the speed of movement

    // Wrap around if it goes off screen, e.g., reset to bottom if it exceeds 50
    if (moonY > 35.0f) {
        moonY = 25.0f; // Reset to a lower position (adjust as needed)
    }

    glutPostRedisplay(); // Request a redraw to apply changes

    // Call updateMoon again after 16 milliseconds (approx 60 FPS)
    glutTimerFunc(16, updateMoon, 0);
}

void updateClouds(int value)
{
    cloudOffsetX += 0.1f; // Adjust the speed of movement

    // Wrap around the axis [-30, 30]
    if (cloudOffsetX > 30.0f) {
        cloudOffsetX = -30.0f;
    }

    glutPostRedisplay(); // Request a redraw to apply changes

    // Call updateClouds again after 16 milliseconds (approx 60 FPS)
    glutTimerFunc(16, updateClouds, 0);
}

void timer(int value) {
    // Update animations based on current view
    if (currentView == 0) {
        // City view animations
        carPosX += 0.2f;  // Move the car
    if (carPosX > 30.0f) carPosX = -30.0f;  // Reset car position

    // Animate leaf shaking using sine wave pattern
    leafShakeOffset = 0.1f * sin(value * 0.1f);

    } else if (currentView == 1) {
        // Village view animations
         // Update truck position (move right to left)
          sunPosY += 0.02f; // Move the sun upwards by 0.1 each frame

    if (sunPosY > 35.0f) {
        sunPosY = 20.0f; // Reset the sun position once it crosses y = 35
    }

    _moveTruck -= 0.1f;
    if (_moveTruck < -30.0f)  // Reset position after moving out of screen
    {
        _moveTruck = 30.0f;
    }

    // Update car position (move left to right)
    _moveCar += 0.1f;
    if (_moveCar > 30.0f)  // Reset position after moving out of screen
    {
        _moveCar = -30.0f;
    }
    _moveBoat += 0.1f;
     if (_moveBoat > 25.0f)  // Reset position after moving out of screen
    {
        _moveBoat = 5.0f;
    }

     _windMill += 2.0f;  // Adjust the rotation speed
    if (_windMill > 360.0f)
    {
        _windMill -= 360.0f;  // Reset the angle after a full rotation
    }

      // Update cloud1 movement
    _moveCloud1 += 0.1f * _directionCloud1;  // Move cloud1 based on direction
    if (_moveCloud1 > 30.0f)  // If cloud1 goes beyond the right edge, reverse direction
    {
        _directionCloud1 = -1;  // Move left
    }
    if (_moveCloud1 < -30.0f)  // If cloud1 goes beyond the left edge, reverse direction
    {
        _directionCloud1 = 1;  // Move right
    }

    // Update cloud2 movement
    _moveCloud2 += 0.1f * _directionCloud2;  // Move cloud2 based on direction
    if (_moveCloud2 < -30.0f)  // If cloud2 goes beyond the left edge, reverse direction
    {
        _directionCloud2 = 1;  // Move right
    }
    if (_moveCloud2 > 30.0f)  // If cloud2 goes beyond the right edge, reverse direction
    {
        _directionCloud2 = -1;  // Move left
    }


    // Rotate wheels based on movement
    _wheelAngle -= 10.0f;  // Adjust this value to control wheel rotation speed
    if (_wheelAngle < -360.0f) {
        _wheelAngle += 360.0f;
    }



    }
    glutPostRedisplay();  // Redraw the scene
    glutTimerFunc(1000 / 60, timer, value + 1);  // 60 FPS

}

void toggleLampLight(int value) {
    // Toggle the lamp state
    lampState = 1 - lampState;  // Toggle between 0 (deep yellow) and 1 (light yellow)

    glutPostRedisplay();  // Request a redraw of the scene
    glutTimerFunc(2000, toggleLampLight, 0);  // Call this function again after 2 seconds
}

void update(int value) {
    if (carPosition > 30.0f) carPosition = -30.0f;  // Reset position after moving off the screen
    carPosition += carSpeed;  // Update position based on speed

    glutPostRedisplay();  // Redraw the scene
    glutTimerFunc(100, update, 0);  // Call this function again in 100ms
}

// Keyboard handler function
void handleKeypress(unsigned char key, int x, int y) {
    if (key == 'v') {
        currentView = 1; // Switch to village view
    }
    if (key == 'c') {
        currentView = 0; // Switch to city view
    }
     if (key == 's') {
        carSpeed = 0.0f;  // Stop the car
    }
    if (key == 'w') {
        carSpeed = 0.1f;  // Resume the car's movement at normal speed
    }
     if (key == 'l') {
        // Change window color to deep yellow
        windowColor[0] = 255.0f;
        windowColor[1] = 255.0f;
        windowColor[2] = 0.0f; // Deep yellow
    }
    if (key == 'n') {
        // Reset window color to white
        windowColor[0] = 1.0f;
        windowColor[1] = 1.0f;
        windowColor[2] = 1.0f; // White
    }
    glutPostRedisplay(); // Trigger display refresh
}

// Handle mouse clicks to control car speed
void handleMouse(int button, int state, int x, int y) {
    if (button == GLUT_LEFT_BUTTON && state == GLUT_DOWN) {
        carSpeed += 0.1f;  // Increase speed on left click
    }
    if (button == GLUT_RIGHT_BUTTON && state == GLUT_DOWN) {
        carSpeed -= 0.1f;  // Decrease speed on right click
        if (carSpeed < 0.0f) carSpeed = 0.0f;  // Prevent negative speed
    }
    glutPostRedisplay();
}

// Handle keyboard input to stop/start the car

// Display function
void display() {
    if (currentView == 0) {
        City_view();
    } else if (currentView == 1) {
        Village_view();
    }
}


// Main function
int main(int argc, char** argv) {
    glutInit(&argc, argv);
    glutInitWindowSize(650, 650);
    glutCreateWindow("Village and City View");
    glClearColor(1.0, 1.0, 1.0, 1.0);
    gluOrtho2D(-30.0, 30.0, -25.0, 35.0);

    // Register callback functions
    glutDisplayFunc(display);
    glutKeyboardFunc(handleKeypress);
    glutMouseFunc(handleMouse);  // Register mouse input function
    glutTimerFunc(0, timer, 0);  // Start the timer for car movement
     glutTimerFunc(2000, toggleLampLight, 0);  // Start the lamp toggling every 2 seconds
     glutTimerFunc(100, update, 0);  // Register timer function for car movement
      glutTimerFunc(0, updateClouds, 0);
      glutTimerFunc(0, updateMoon, 0);



    glutMainLoop();
    return 0;
}
