#include <iostream>
#include <cstdio>
#include <string>
#include <cstdlib>
#include<math.h>
#include <GL/glut.h>
#include<windows.h>
#include<vector>
#include<algorithm>
int startflag1=0;
int flag3d=0;
void placehouse();
void apply3D();
void threeD();
void printPanel();
void keystart(unsigned char keyss, int x, int y);
void dispfrst();




float angle = 0;
float xView = 50, yView = 5, zView =100;
float lx = 0, ly = 0, lz = -1;

int xOrigin = -1, yOrigin = -1, zOrigin = -1;

float deltaAngle = 0;
//float deltaMove = 0;



struct coordinates{
        int x,y;
};
coordinates c[100];
using namespace std;
class edges{
public:
    int node1,node2;
    int dist()
    {
        int dist1=c[node1].x-c[node2].x;
        dist1*=dist1;
        int dist2=c[node1].y-c[node2].y;
        dist2*=dist2;
        int dis=int(sqrt(double(dist1+dist2)));
        return dis;
    }
}e[100];
void print3dworld();
edges e1[200];
edges e2[100];
edges emin[100];
int correct=0;
int minp[100],arr[100];
int minx=-1;
int e2c=0;
int src=-1,dst=-1;
char k=13;
int minflag=0;
int tSP(int g[100][100],int s);
void alledges();
void edgedoubler();
void drawLines(edges[],int);
int Menupos=0;
int count1=0;
void printLines();
void printPoints();
void printcoordinates();
void displayer(int);
void display(void);
void printtext(int x, int y, string String,int num);
void createNewMap();
void loadExistingMap();
void applyTSP(edges[],int);
void inputLines();
void traverse(edges[],int);
int WindowHeight = 768;
int WindowWidth = 1366;
int main(int argc, char *argv[])
{
    glutInit(&argc, argv);
    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH);
    glutInitWindowSize(WindowWidth, WindowHeight);
    glutInitWindowPosition(0, 0);
 
    glutCreateWindow("Travelling Salesman Problem");
 
    glutDisplayFunc(dispfrst);
 
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();

 
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();
 
    glEnable(GL_LINE_SMOOTH);
    glEnable(GL_POLYGON_SMOOTH);
    glEnable(GL_POINT_SMOOTH);

    glutMainLoop();
    return 0;
    //glClearColor(1.0,1.0,1.0,1.0);
}

void startscreen()
{
     glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
 
    glEnable(GL_DEPTH_TEST);
    glColor3f(0,0,1);

 printtext(WindowWidth-1000,WindowHeight-50,"SIR M. VISVESVARAYA INSTITUTE OF TECHNOLOGY",0);
 glColor3f(0.7,0,1);
 char str[50];
 sprintf(str,"DEPARTMENT OF COMPUTER SCIENCE AND ENGINEERING");
 printtext(WindowWidth/2-16*strlen(str)/2,WindowHeight-90,str,2);
 glColor3f(1,0.5,0);
 printtext(WindowWidth-800,WindowHeight-160,"A MINI PROJECT ON",0);
 glColor3f(1,0,0);
 printtext(WindowWidth-900,WindowHeight-250,"GRAPHICAL VISUALISATION OF TSP",0);
 glColor3f(1,0.5,0);
 printtext(WindowWidth-1024,WindowHeight-340,"BY:",0);
 glColor3f(0.5,0,0.5);
 printtext(WindowWidth-1289,WindowHeight-400,"AASHISH PRAKASH   (1MV15CS002)",0);
 printtext(WindowWidth-1289,WindowHeight-440,"ADITYA GUPTA      (1MV15CS005)",0);
 printtext(WindowWidth-1289,WindowHeight-480,"ANOOP KULKARNI    (1MV15CS010)",0);
 glColor3f(1,0.5,0);
 printtext(WindowWidth-500,WindowHeight-340,"GUIDES:",0);
 glColor3f(0.5,0.2,0.2);
 printtext(WindowWidth-550,WindowHeight-400,"Mr.ELAIYARAJA P.",0);
  glColor3f(1,0.1,1);
 printtext(WindowWidth-900,WindowHeight-600,"PRESS ENTER TO START",0);
 glFlush();
    glutSwapBuffers();
}
void dispfrst()
{
        startscreen();
    glutKeyboardFunc(keystart);
}
 void keystart(unsigned char keyss, int x, int y)
 {
     
 switch(keyss)
 {
 case 13:
    startflag1=1;
     display();

       }
    //dispfrst();
}


     


void printtext(int x, int y, string String,int num)
{
//(x,y) is from the bottom left of the window
    glMatrixMode(GL_PROJECTION);
    glPushMatrix();
    glLoadIdentity();
    glOrtho(0, WindowWidth, 0, WindowHeight, -1.0f, 1.0f);
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glLoadIdentity();
    glPushAttrib(GL_DEPTH_TEST);
    glDisable(GL_DEPTH_TEST);
    if(Menupos==num&&startflag1==1)
        glColor3f(1.0,0.0,0.0);
        else
            if(startflag1==1)
            glColor3f(1.0,1.0,1.0);
    glRasterPos2i(x,y);
    for (int i=0; i<String.size(); i++)
    {
        glutBitmapCharacter(GLUT_BITMAP_TIMES_ROMAN_24, String[i]);
    }
    glPopAttrib();
    glMatrixMode(GL_PROJECTION);
    glPopMatrix();
    glMatrixMode(GL_MODELVIEW);
    glPopMatrix();
}
void keys(unsigned char key,int x,int y)
{  
    printf("%c",key);
    if(key=='s')
    {    Menupos=(Menupos+1)%3;display();}
    if(key=='w')
    {    if(Menupos==0)
            Menupos=2;
        else
            Menupos=(Menupos-1)%3;
    display();
    }
    if(key==13)
    {
        switch(Menupos)
        {
            case 0:
                createNewMap();
                break;
            case 1:
                threeD();
                break;
            case 2:
                exit(0);
        }
    }
}
void displayer(int pos)
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
 
    glEnable(GL_DEPTH_TEST);
  
    char string[3][64];
    sprintf(string[0], "2D Implementation of TSP");
	sprintf(string[1], "3D Visualization of TSP");
    sprintf(string[2], "Exit");
    printtext((WindowWidth)/2-40,WindowHeight/2+24,string[Menupos],Menupos);
    glutSwapBuffers();
}
void display(void)
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
 
    glEnable(GL_DEPTH_TEST);
  
    char string[3][64];
    sprintf(string[0], "2D Implementation of TSP");
    sprintf(string[1], "3D Visualization of TSP");
    sprintf(string[2], "Exit");

    for(int i=0;i<3;i++)
    printtext((WindowWidth)/2-40,WindowHeight/2+24-i*40,string[i],i);
    glutKeyboardFunc(keys);
    glutSwapBuffers();
}
void mouse(int btn,int state,int x1,int y1)
{
    if(btn==GLUT_LEFT_BUTTON&&state==GLUT_DOWN)
    {
        c[count1].x=x1;
        c[count1].y=WindowHeight-y1;
        count1++;
        printPoints();
    }
    if(btn==GLUT_RIGHT_BUTTON&&state==GLUT_DOWN)
        printcoordinates();
}
void createNewMap()
{
	//glClearColor(1,248.0/255.0,220.0/255.0,0);
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
 
    glEnable(GL_DEPTH_TEST);
  
    char string[2][100];
    sprintf(string[0], "Please Draw the Map");
    sprintf(string[1], "CLick on the Points You want to mark as City,then Right Click to Enter Lines");
    for(int i=0;i<2;i++)
    printtext(WindowWidth/2-150-i*220,WindowHeight-250-i*100,string[i],-1);
	glutSwapBuffers();
    glutMouseFunc(mouse);
}

int temp1=-1,temp2=-1;
int ec=0;
void printPoints()
{
	glMatrixMode (GL_PROJECTION);
    glPushMatrix();
    glLoadIdentity();
    glOrtho(0, WindowWidth, 0, WindowHeight, -1.0f, 1.0f);
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glLoadIdentity();
    glPushAttrib(GL_DEPTH_TEST);
    glDisable(GL_DEPTH_TEST);
  
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
    glPointSize(10);
    glEnable(GL_DEPTH_TEST);
    glColor3f(1.0,0.0,0.0);
    glBegin(GL_POINTS);
        for(int i=0;i<count1;i++)
        {
            glVertex2f(c[i].x,c[i].y);
        }
    glEnd();
    glPointSize(2);
    for(int i=0;i<count1;i++)
    {
        if(temp1==i||temp2==i)
            glColor3f(0.0,0.0,1.0);
        glBegin(GL_LINE_LOOP);
        glVertex2f(c[i].x-10,c[i].y-10);
        glVertex2f(c[i].x-10,c[i].y+10);
        glVertex2f(c[i].x+10,c[i].y+10);
        glVertex2f(c[i].x+10,c[i].y-10);
        glEnd();
        glColor3f(1.0,0.0,0.0);
    }
    glPopAttrib();
    glMatrixMode(GL_PROJECTION);
    glPopMatrix();
    glMatrixMode(GL_MODELVIEW);
    glPopMatrix();
    glutSwapBuffers();
  
}

void printcoordinates()
{
    printPoints();
    inputLines();
}
void select(int btn,int state,int x1,int y1)
{  
    y1=WindowHeight-y1;
    if(btn==GLUT_LEFT_BUTTON&&state==GLUT_DOWN)
        for(int i=0;i<count1;i++)
        {  
            if(x1>=c[i].x-10&&c[i].x+10>=x1)
            if(y1<=c[i].y+10&&y1>=c[i].y-10)
            {
                if(temp1==-1)
                {
                    temp1=i;
                    printPoints();
                }
                else
                if(temp1!=i)
                {      
                    temp2=i;
                    e[ec].node1=temp1;
                    e[ec].node2=temp2;
                    printPoints();
                    temp1=-1;
                    temp2=-1;
                    ec++;
                }
            }
        }
    if(btn==GLUT_RIGHT_BUTTON&&state==GLUT_DOWN)
    {  
        printLines();
    }
}
void enterr(unsigned char key,int x,int y)
{
    if(key==13)
    {
        k=key;
        traverse(e,ec);
    }
    if(key==' ')
    {
        k=key;
        edgedoubler();
    }
    if(key==8)
    {
        createNewMap();
        printPoints();
    }
    if(key=='a')
    {
        k=key;
        alledges();
    }
}
void inputLines()
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
 
    glEnable(GL_DEPTH_TEST);
  
    char string[2][100];
    sprintf(string[0], "Please Draw the Map");
    sprintf(string[1], "CLick on the pair of points to Enter Lines");
    for(int i=0;i<2;i++)
    printtext(WindowWidth/2-40-i*40,WindowHeight-30-i*40,string[i],-1);
    glutSwapBuffers();
    printPoints();
    glutMouseFunc(select);
    glutKeyboardFunc(enterr);
}
void printLines()
{  
    printPoints();
    if(k==13)
    edgedoubler();//drawLines(e,ec);
    else if(k==' ')
        edgedoubler();
    else if(k=='a')
        alledges();//applyTSP();
    //traverse();
}
void drawLines(edges ex[],int ex1)
{
    glMatrixMode(GL_PROJECTION);
    glPushMatrix();
    glLoadIdentity();
    glOrtho(0, WindowWidth, 0, WindowHeight, -1.0f, 1.0f);
    glMatrixMode(GL_MODELVIEW);
    glPushMatrix();
    glLoadIdentity();
    glPushAttrib(GL_DEPTH_TEST);
    glDisable(GL_DEPTH_TEST);
  
                glEnable(GL_DEPTH_TEST);
    glPointSize(4);
    glColor3f(1.0,1.0,1.0);
    for(int i=0;i<ex1;i++)
    {
            glBegin(GL_LINES);
                glVertex2f(c[ex[i].node1].x,c[ex[i].node1].y);
                glVertex2f(c[ex[i].node2].x,c[ex[i].node2].y);
            glEnd();
            char str[30];
            sprintf(str,"%d",ex[i].dist());
            printtext((c[ex[i].node1].x+c[ex[i].node2].x)/2,(c[ex[i].node1].y+c[ex[i].node2].y)/2,str,0);
    }
            glPopAttrib();
    glMatrixMode(GL_PROJECTION);
    glPopMatrix();
    glMatrixMode(GL_MODELVIEW);
    glPopMatrix();
    glutSwapBuffers();

}
void traverse(edges ex[],int ecx)
{
    int min,max;
    float j;
    float dy,dx,yt;
    for(int i=0;i<ecx;i++)
    {           
        min=c[ex[i].node1].x;
        max=c[ex[i].node2].x;
        dy=c[ex[i].node2].y-c[ex[i].node1].y;
        dx=max-min;
        yt=c[ex[i].node1].y;
        printPoints();
        drawLines(ex,ecx);
       
        if(min<max)
        {
            for(j=min;j<=max;j++)
            {
                glClearColor(0.0f, 0.0f, 0.0f, 1.0f );
                glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
                glLoadIdentity();
                glutSwapBuffers();
                glMatrixMode (GL_PROJECTION);
                glPushMatrix();
                glLoadIdentity();
                glOrtho(0, WindowWidth, 0, WindowHeight, -1.0f, 1.0f);
                glMatrixMode(GL_MODELVIEW);
                glPushMatrix();
                glLoadIdentity();
                glPushAttrib(GL_DEPTH_TEST);
                glDisable(GL_DEPTH_TEST);
                //printPoints();
                //drawLines();
                glPointSize(5);
                glEnable(GL_DEPTH_TEST);
                glColor3f(1.0,0.0,0.0);
                glBegin(GL_POINTS);
                glColor3f(0,1,1);
                if(minflag==1)glColor3f(1,0,1);
                glPointSize(5);
                glVertex2f(j,yt);
                glEnd();
                yt+=dy/dx;   
                glPopAttrib();
                glMatrixMode(GL_PROJECTION);
                glPopMatrix();
                glMatrixMode(GL_MODELVIEW);
                glPopMatrix();
                glutSwapBuffers();
                Sleep(10);
        }
        }
        else
        {
            for(j=min;j>max;j--)
        {
            glClearColor(0.0f, 0.0f, 0.0f, 1.0f );
            glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
            glLoadIdentity();
            glutSwapBuffers();
            glMatrixMode (GL_PROJECTION);
                glPushMatrix();
                glLoadIdentity();
                glOrtho(0, WindowWidth, 0, WindowHeight, -1.0f, 1.0f);
                glMatrixMode(GL_MODELVIEW);
                glPushMatrix();
                glLoadIdentity();
                glPushAttrib(GL_DEPTH_TEST);
                glDisable(GL_DEPTH_TEST);
                //printPoints();
                //drawLines();
                glPointSize(5);
                glEnable(GL_DEPTH_TEST);
                glColor3f(1.0,0.0,0.0);
                glBegin(GL_POINTS);
                glColor3f(0,1,1);
                if(minflag==1)glColor3f(1,0,1);
                glPointSize(5);
                glVertex2f(j,yt);
                glEnd();
                yt-=dy/dx;
                glPopAttrib();
                glMatrixMode(GL_PROJECTION);
                glPopMatrix();
                glMatrixMode(GL_MODELVIEW);
                glPopMatrix();
                glutSwapBuffers();
                Sleep(10);
        }

        }
    }

}
void edgedoubler()
{
    int i;
   
    for(i=0;i<ec;i++)
    {
        e1[i].node1=e[i].node1;
        e1[i].node2=e[i].node2;
    }
    for(i=0;i<ec;i++)
    {
        e1[i+ec].node1=e[i].node2;
        e1[i+ec].node2=e[i].node1;
    }
    traverse(e1,2*ec);

    applyTSP(e1,2*ec);
}
void alledges()
{
    for(int i=0;i<count1;i++)
    {
        for(int j=0;j<count1;j++)
            if(i!=j)
            {
                e2[e2c].node1=i;
                e2[e2c].node2=j;
                e2c++;
            }
    }
    traverse(e2,e2c);
    drawLines(e2,e2c);
    applyTSP(e2,e2c);
}
int tflag=1;
void applyTSP(edges ex[],int exc)
{
    /*if(k!='a')
    {
        char st[100];
        sprintf(st,"Select starting and ending points");
        printtext(10,10,st,0);
        glutSwapBuffers();
        glutMouseFunc(select1);
    }*/
    printPoints();
    drawLines(ex,exc);   
    src=dst=0;
    int g[100][100];
    for(int i=0;i<count1;i++)
        for(int j=0;j<count1;j++)
        g[i][j]=999;
    for(int i=0;i<count1;i++)
        g[i][i]=0;
    for(int i=0;i<exc;i++)
    {
        g[ex[i].node1][ex[i].node2]=ex[i].dist();
        g[ex[i].node2][ex[i].node1]=ex[i].dist();   
    }
    printf("Initial Graph for TSP:\n");
    for(int i=0;i<count1;i++)
    {    for(int j=0;j<count1;j++)
            printf("%d\t",g[i][j]);
            printf("\n");
    }
    int s=0;
    cout<<"Minimum cost:"<<tSP(g,s)<<endl;
    for(int i=0;i<minx-1;i++)
    {
        emin[i].node1=minp[i];
        emin[i].node2=minp[i+1];
    }
    minflag=1;
    if(correct==1)
    {
    printPoints();
    //drawLines(ex,exc);
    //if(flag3d==0)
		traverse(emin,minx-1);
    }
}
int tSP(int graph[100][100], int s)
{
    int x;
    // store all vertex apart from source vertex
    vector<int> vertex;
    for (int i = 0; i < count1; i++)
        if (i != s)
        {    vertex.push_back(i);
        }
    for(int i=0;i<100;i++)
        minp[i]=-1;
    // store minimum weight Hamiltonian Cycle.
    int min_path = INT_MAX;
   
    do {
        x=1;
        // store current Path weight(cost)
        int current_pathweight = 0;
        
        // compute current path weight
        int k = s;
        arr[0]=s;
        for (int i = 0; i < vertex.size(); i++) {
            if(graph[k][vertex[i]]!=999)
            {
                current_pathweight += graph[k][vertex[i]];
                k = vertex[i];
                arr[x]=k;
                x++;
            }
        }
        arr[x++]=0;
        current_pathweight += graph[k][s];
        cout<<x<<c;
        if(x-1==count1)
        {
            correct=1;
            // update minimum
            if(min_path>current_pathweight)
            {   
                minx=x;
                for(int i=0;i<x;i++)
                minp[i]=arr[i];
            for(int i=x;i<100;i++)
                minp[i]=-1;
            }
        }
       
        min_path = min(min_path, current_pathweight);
       
    } while (next_permutation(vertex.begin(), vertex.end()));
    if(correct==0)
    {
          glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
         glEnable(GL_DEPTH_TEST);  
        char str[30];
        sprintf(str,"No path found for tsp");
        printtext((WindowWidth)/2-40,WindowHeight/2+24-40,str,0);
        glutSwapBuffers();

    }
    cout<<"\nMinimum path:";
    for(int i=0;i<minx;i++)
        cout<<minp[i]<<" ";
    return min_path;
}
int rotcount=0;
float rotang=0;

void mouse1(int btn,int state,int x1,int y1)
{
	if(btn==GLUT_LEFT_BUTTON&&state==GLUT_DOWN)
    {
        c[count1].x=x1;
        c[count1].y=WindowHeight-y1;
        count1++;
        printPoints();
    }
    if(btn==GLUT_RIGHT_BUTTON&&state==GLUT_DOWN)
	{	
		
		apply3D();		
		//rotcount++;print3dworld();
	}
	if(btn==GLUT_MIDDLE_BUTTON&&state==GLUT_DOWN)
	{	
		print3dworld();
	}
}
void keyb(unsigned char kb,int x,int y)
{
	if(kb=='x')
		print3dworld();
}

void threeD()
{
	//glClearColor(135.0/255.0,206.0/255.0,250.0/255.0,0);
	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
 
    glEnable(GL_DEPTH_TEST);
  
    char string[2][100];
    sprintf(string[0], "Please Draw the Map");
    sprintf(string[1], "CLick on the Points You want to mark as City,then Right Click to Enter Lines");
    for(int i=0;i<2;i++)
    printtext(WindowWidth/2-40-i*40,WindowHeight-30-i*40,string[i],-1);
	glutSwapBuffers();
    glutMouseFunc(mouse1);
	glutKeyboardFunc(keyb);
}
int dispcount=-1;
void keypress(unsigned char ke,int x,int y)
{
	if(ke==' ')
		{
			if(rotcount==0)
				dispcount++;
			rotcount++;
			
			if(dispcount!=count1)
			print3dworld();
			else
				exit(0);

	}
}
void apply3D()
{
	flag3d=1;
	for(int i=0;i<count1;i++)
    {
        for(int j=0;j<count1;j++)
            if(i!=j)
            {
                e2[e2c].node1=i;
                e2[e2c].node2=j;
                e2c++;
            }
    }
	//drawLines(e2,e2c);
	applyTSP(e2,e2c);
	//print3dworld();
	
}
void print3dworld()
{
glutKeyboardFunc(keypress);
	//glClearColor(1,1,1,0.0);
	glClearColor(135.0/255.0,206.0/255.0,250.0/255.0,0);
	glMatrixMode(GL_PROJECTION);
	glLoadIdentity();
	glViewport(0, 0, WindowWidth, WindowHeight);
	float aspect = WindowWidth / WindowHeight;
	gluPerspective(45, aspect, 0.1, 1000);
	glMatrixMode(GL_MODELVIEW);

	glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
	glMatrixMode(GL_MODELVIEW);
	glLoadIdentity();
	

	gluLookAt(xView, yView, zView,
	xView + lx, yView + ly, zView + lz,
	0, 1, 0);
	glColor3f(1, 0, 0);
	glLineWidth(3);
	if(rotang!=0)
	{	glTranslatef(50,0,-95);
	glRotatef(rotang,0,1,0);
	glTranslatef(-50,0,95);
	}
	glColor3f(0, 0.5, 0);
	glBegin(GL_QUADS);
	glVertex3f(-400, 0, -90);
	glVertex3f(-00, 0, 100);
	glVertex3f(45, 0, 100);
	glVertex3f(45, 0, -90);
	glEnd();

	glColor3f(0.5, 0.5, 0.5);
	glBegin(GL_QUADS);
	glVertex3f(45, 0, -90);
	glVertex3f(45, 0, 100);
	glVertex3f(55, 0, 100);
	glVertex3f(55, 0, -90);	
	glEnd();

	glColor3f(0, 0.5, 0);
	glBegin(GL_QUADS);
	glVertex3f(55, 0, -90);
	glVertex3f(55, 0, 100);
	glVertex3f(400, 0, 100);
	glVertex3f(400, 0, -90);
	glEnd();

	glColor3f(0.5, 0.5, 0.5);
	glBegin(GL_QUADS);
	glVertex3f(45, 0, -100);
	glVertex3f(45, 0, 100);
	glVertex3f(55, 0, 100);
	glVertex3f(55, 0, -100);
	glEnd();

	glColor3f(0.5, 0.5, 0.5);
	glBegin(GL_QUADS);
	glVertex3f(55, 0, -100);
	glVertex3f(55, 0, -90);
	glVertex3f(400, 0, -90);
	glVertex3f(400, 0, -100);
	glEnd();

	glColor3f(0, 0.5, 0);
	glBegin(GL_QUADS);
	glVertex3f(400, 0, -150);
	glVertex3f(400, 0, -100);
	glVertex3f(-400, 0, -100);
	glVertex3f(-400, 0, -150);
	glEnd();
	
	glColor3f(0.5, 0.5, 0.5);
	glBegin(GL_QUADS);
	glVertex3f(45, 0, -100);
	glVertex3f(45, 0, -90);
	glVertex3f(-400, 0, -90);
	glVertex3f(-400, 0, -100);
	glEnd();

		glPushMatrix();
		glColor3f(210.0/255,69.105/255.0,30.0/255.0);
		glTranslatef(40,5,0);
		glRotatef(0,0,1,0);
		glScalef(1,1,0.1);
		glutSolidCube(10);
		glPopMatrix();
		glPushMatrix();
		glColor3f(139.0/255,69.0/255.0,19.0/255.0);
		glTranslatef(44.5,5,-5);
		glRotatef(0,0,1,0);
		glScalef(0.1,1,1);
		glutSolidCube(10);
		glPopMatrix();

		placehouse();
	
	glutSwapBuffers();
}
void placehouse()
{
	if(zView!=-95)
	{
		zView-=5;
	gluLookAt(xView, yView, zView,xView + lx, yView + ly, zView + lz,0, 1, 0);
	}
	else if(dispcount%2==0)
	{
		if(rotang!=95)
		rotang+=5;
	}
	else
	{
		if(rotang!=-95)
		rotang-=5;
	}
	if(rotang==95||rotang==-95)
	{
		rotcount=0;
		rotang=0;
		zView=100;
	}
}