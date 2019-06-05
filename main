#if defined(UNICODE) && !defined(_UNICODE)
#define _UNICODE
#elif defined(_UNICODE) && !defined(UNICODE)
#define UNICODE
#endif

#include <tchar.h>
#include <windows.h>
#include <iostream>
#include <vector>
#include <utility>
#include <math.h>
#include <fstream>
using namespace std;
// Add new popup menu
#define ADDPOPUPMENU(hmenu, string) \
	HMENU hSubMenu = CreatePopupMenu(); \
	AppendMenu(hmenu, MF_STRING | MF_POPUP, (UINT)hSubMenu, string);

// Add a menu item
#define ADDMENUITEM(hmenu, ID, string) \
	AppendMenu(hSubMenu, MF_STRING, ID, string);

enum
{
	ID_FILE_EXIT,
	ID_Line_DDA,ID_Line_Midpoint,ID_Line_Parametric,
	ID_Ellipse_Cartesian,ID_Ellipse_Polar,ID_Ellipse_MidPoint,
	ID_Curve_ThirdHermit,ID_Curve_ThirdBezier,
	ID_ClippingP,
	ID_Save,ID_Reload
};

///creating List
void CreateMainMenu(HWND hwnd)
{
    HMENU hmenu = CreateMenu();

	ADDPOPUPMENU(hmenu, "&Menu");
	ADDMENUITEM(hmenu, ID_Save, "&Save Current Draw");
	ADDMENUITEM(hmenu, ID_Reload, "&Load Previous Draw");

	ADDMENUITEM(hmenu, NULL, NULL);

	ADDMENUITEM(hmenu, ID_Line_DDA, "&Line: DDA");
	ADDMENUITEM(hmenu, ID_Line_Midpoint, "&Line: MidPoint");
	ADDMENUITEM(hmenu, ID_Line_Parametric, "&Line: Parametric");

	ADDMENUITEM(hmenu, NULL, NULL);

	ADDMENUITEM(hmenu, ID_Ellipse_Cartesian, "&Ellipse: Cartesian");
	ADDMENUITEM(hmenu, ID_Ellipse_Polar, "&Ellipse: Polar");
	ADDMENUITEM(hmenu, ID_Ellipse_MidPoint, "&Ellipse: MidPoint");

	ADDMENUITEM(hmenu, NULL, NULL);

	ADDMENUITEM(hmenu, ID_Curve_ThirdHermit, "&Curve: Hermit");
	ADDMENUITEM(hmenu, ID_Curve_ThirdBezier, "&Curve: Bezier");

	ADDMENUITEM(hmenu, NULL, NULL);

	ADDMENUITEM(hmenu, ID_ClippingP, "&Clipping: Polygon Clipping Polygon");
    ADDMENUITEM(hmenu, NULL, NULL);
	ADDMENUITEM(hmenu, ID_FILE_EXIT, "&Exit");

    SetMenu(hwnd, hmenu);
}


///points container for save and load
vector< pair<int,int> > container;

///general functions
void swap(int& x, int& y)
{
    int tmp = x;
    x = y;
    y = tmp;
}
int roundPoint(double x)
{
    return (int)(x + 0.5);
}


///save pixel before drawing
void setMypixel(HDC hdc,int x,int y,COLORREF c)
{
    pair<int,int> point;
    point.first=x;
    point.second=y;
    container.push_back(point);
    SetPixel(hdc,x,y,c);
}

///save function
void saveToFile()
{
    fstream file;
    file.open("file.txt",ios::out|ios::binary|ios::trunc);
    for(unsigned int i=0;i<container.size();i++)
    {
        file.write((char*) &container[i],sizeof(container[i]));
    }
    file.close();
}

///drawing saved points
void draw_container(HDC hdc,COLORREF c)
{
    for(unsigned int i=0;i<container.size();i++)
    {
        SetPixel(hdc,container[i].first,container[i].second,c);
    }
}

///load function
void loadFromFile(HDC hdc,COLORREF rgb)
{
    ifstream file;
    file.open("file.txt",ios::in|ios::binary);
    while(!file.eof())
    {
        pair<int,int> obj;
        file.read((char*) &obj,sizeof(obj));
        SetPixel(hdc,obj.first,obj.second,rgb);
        container.push_back(obj);
    }
    file.close();
}

///lines
void DrawLine(HDC hdc, int xs, int ys, int xe, int ye, COLORREF RGB)
{
    int dx = xe - xs;
    int dy = ye - ys;
    if (abs(dy) <= abs(dx))
    {
        double slope = (double)dy / dx;
        if (xs > xe)
        {
            swap(xs, xe);
            swap(ys, ye);
        }
        int x = xs;
        while (x <= xe)
        {
            int y = roundPoint(ys + (x - xs)*slope);
            setMypixel(hdc, x, y, RGB);
            x++;
        }

    }
    else
    {
        double islope = (double)dx / dy;
        if (ys > ye)
        {
            swap(xs, xe);
            swap(ys, ye);
        }
        int y = ys;
        while (y <= ye)
        {
            int x = roundPoint(xs + (y - ys)*islope);
            setMypixel(hdc, x, y, RGB);
            y++;
        }
    }
}
void DrawLineParamtaric(HDC hdc, int x1,int y1,int x2,int y2, COLORREF color )
{
    int dy=abs(y2-y1);
    int dx=abs(x2-x1);
   float dt=((dx>dy)?dx:dy)+1;
     if(x1>x2)
    {
        swap(x1,x2);
        swap(y1,y2);
    }
    dt=1/dt;
    for(double t=0;t<=1;t+=dt){
       int x=roundPoint(x1+(t*(x2-x1)));
       int y=roundPoint(y1+(t*(y2-y1)));
         setMypixel(hdc,x,y,color);
    }
}
void DrawLineDDA(HDC hdc, int x1,int y1,int x2,int y2, COLORREF color )
{
   int dx=x2-x1;
   int dy=y2-y1;
   setMypixel(hdc,x1,y1,color);
   if(abs(dx)>abs(dy)){
        int x=x1 ;
        int xinc=dx>0? 1:-1;
        float y=y1;
        float yinc=(float)dy/(dx*xinc);
        while(x!= x2){
            x+=xinc;
            y+=yinc;
            setMypixel(hdc,x,roundPoint(y),color);
        }
   }else{
        int y=y1;
        int yinc= dy>0? 1:-1;
        float x=x1;
        float xinc=(float)dx/(dy*yinc);
          while(y!= y2){
            x+=xinc;
            y+=yinc;
            setMypixel(hdc,roundPoint(x),y,color);
   }
}
}
void DrawLineMadPoint (HDC hdc, int x1,int y1,int x2,int y2, COLORREF color )
{ // at any slope
    if(x1>x2)
    {
        swap(x1,x2);
        swap(y1,y2);
    }

    int x,y,dy,dx,d,d2,d3,d4,ch1,ch2,ch3,ch4,ch5,ch6,ch7,ch8;
    x=x1;
    y=y1;
    dy=y2-y1;
    dx=x2-x1;
    d=dx-(2*dy);
    d2=2*dx-dy;
    d3=(-2*dx)-dy;
    d4=-dx-(2*dy);
    ch1=2*(dx-dy);
    ch2=-2*dy;
    ch3=2*(dx-dy);
    ch4=2*dx;
    ch5=-2*(dx+dy);
    ch6=-2*dx;
    ch7=-2*(dx+dy);
    ch8=-2*dy;
    setMypixel(hdc,x,y,color);
    if(abs(dx)>abs(dy))
    {
        if(dy>0 && dx >0)
        {
            while(x<x2)
            {
                if(d<0)
                {
                    d=d+ch1;
                    y=y+1;
                }
                else
                {
                    d=d+ch2;
                }
                x=x+1;
                setMypixel(hdc,x,y,color);
            }
        }
        else
        {
            while(x<x2)
            {
                if(d4>0)
                {
                    d4=d4+ch7;
                    y=y-1;
                }
                else
                {
                    d4=d4+ch8;
                }
                x=x+1;
                setMypixel(hdc,x,y,color);
            }
        }
    }
    else if (abs(dy)>abs(dx))
    {
        if(dx>0 && dy>0)
        {
            while(y<y2)
            {
                if(d2>0)
                {
                    d2=d2+ch3;
                    x=x+1;
                }
                else
                {

                    d2=d2+ch4;
                }
                y=y+1;
                setMypixel(hdc,x,y,color);
            }
        }
        else
        {
            while(y>y2)
            {
                if(d3<0)
                {
                    x=x+1;
                    d3=d3+ch5;
                }
                else
                {
                    d3=d3+ch6;
                }
                y=y-1;
                setMypixel(hdc,x,y,color);
            }
        }
    }
}


///clipping
union OutCode
{
    unsigned All : 4;
    struct
    {
        unsigned left : 1, top : 1, right : 1, bottom : 1;
    };
};
OutCode GetOutCode(double x, double y, int xleft, int ytop, int xright, int ybottom)
{
    OutCode out;
    out.All = 0;
    if (x<xleft)
        out.left = 1;
    else if (x>xright)
        out.right = 1;
    if (y>ytop)
        out.top = 1;
    else if (y<ybottom)
        out.bottom = 1;
    return out;
}
void VIntersect(double x1, double y1, double x2, double y2, int x, double *xi, double *yi)
{
    *xi = x;
    *yi= y1 + (x - x1)*(y2 - y1) / (x2 - x1);
}
void HIntersect(double xs, double ys, double xe, double ye, int y, double *xi, double *yi)
{
    *yi = y;
    *xi = xs + (y - ys)*(xe - xs) / (ye - ys);
}

vector< pair<int,int> > poly_points;
vector< pair<int,int> > clip_points;

void line_Clipping(HDC hdc,int xs,int ys,int xe,int ye ,int xleft, int ytop, int xright, int ybottom)
{
    double x1 = xs, y1 = ys, x2 = xe, y2 = ye;
    OutCode out1 = GetOutCode(x1, y1, xleft, ytop, xright, ybottom);
    OutCode out2 = GetOutCode(x2, y2, xleft, ytop, xright, ybottom);
    while ((out1.All || out2.All) && !(out1.All & out2.All))
    {
        double xi, yi;
        if (out1.All)
        {

            if (out1.left)
                VIntersect(x1, y1, x2, y2, xleft, &xi, &yi);
            else if (out1.top)
                HIntersect(x1, y1, x2, y2, ytop, &xi, &yi);
            else if (out1.right)
                VIntersect(x1, y1, x2, y2, xright, &xi, &yi);
            else
                HIntersect(x1, y1, x2, y2, ybottom, &xi, &yi);
            x1 = xi;
            y1 = yi;
            out1 = GetOutCode(x1, y1, xleft, ytop, xright, ybottom);
        }
        else
        {

            if (out2.left)
                VIntersect(x1, y1, x2, y2, xleft, &xi, &yi);
            else if (out2.top)
                HIntersect(x1, y1, x2, y2, ytop, &xi, &yi);
            else if (out2.right)
                VIntersect(x1, y1, x2, y2, xright, &xi, &yi);
            else
                HIntersect(x1, y1, x2, y2, ybottom, &xi, &yi);
            x2 = xi;
            y2 = yi;
            out2 = GetOutCode(x2, y2, xleft, ytop, xright, ybottom);
        }
    }

    if (out1.All==0 && out2.All==0)
    {
        pair<int,int> p1,p2;
        p1.first=round(x1);p1.second=round(y1);
        clip_points.push_back(p1);
        p2.first=round(x2);p2.second=round(y2);
        clip_points.push_back(p2);
    }
}
void poly_Clipping(HDC hdc,int xleft, int ytop, int xright, int ybottom,COLORREF rgb)
{
    int poly_Vec_size=poly_points.size();
    if(poly_Vec_size>=2)
    {
        if(poly_Vec_size>=5)
        {
            int x1=poly_points[poly_Vec_size-1].first,y1=poly_points[poly_Vec_size-1].second;
            int x2=poly_points[0].first,y2=poly_points[0].second;
            line_Clipping (hdc,x1,y1,x2,y2,xleft,ytop,xright,ybottom);
        }
        for(int i=0;i<poly_Vec_size-1;i++)
        {
            int x1=poly_points[i].first,y1=poly_points[i].second;
            int x2=poly_points[i+1].first,y2=poly_points[i+1].second;
            line_Clipping(hdc,x1,y1,x2,y2,xleft,ytop,xright,ybottom);
        }
        int clip_Vec_size=clip_points.size();
        for(int i=0;i<clip_Vec_size-1;i++)
        {
            int x1=clip_points[i].first,y1=clip_points[i].second;
            int x2=clip_points[i+1].first,y2=clip_points[i+1].second;
            DrawLine(hdc,x1,y1,x2,y2,rgb);
        }
        clip_points.clear();
    }
}


///ellipse
void draw_4points(HDC hdc, int xc,int yc,int x,int y,COLORREF c)
{

    setMypixel(hdc,xc+x,yc+y,c);
    setMypixel(hdc,xc-x,yc-y,c);
    setMypixel(hdc,xc-x,yc+y,c);
    setMypixel(hdc,xc+x,yc-y,c);
}
void draw_ellipse_Cartesian(HDC hdc,int xc,int yc,double a,double b,COLORREF c)
{
    double a_sqr,b_sqr,x,y;
    x=0;
    y=b;
    a_sqr=a*a;
    b_sqr=b*b;
    while(x<=a+1)
    {
        draw_4points(hdc,xc,yc,roundPoint(x),roundPoint(y),c);
        x+=0.01;
        y=sqrt((a_sqr*b_sqr-b_sqr*(x*x))/a_sqr);
    }
}
void draw_ellipse_Polar(HDC hdc,int xc,int yc,double a,double b,COLORREF c)
{
    double x = 0;
    double y = b;
    for(double theta=0;theta<=1.57;theta+=0.001)
    {
        x = a*cos(theta);
        y = b*sin(theta);
        draw_4points(hdc, xc, yc, roundPoint(x), roundPoint(y), c);
    }
}
void draw_ellipse_mid_point(HDC hdc,int xc,int yc,double a,double b,COLORREF c)
{
    long x,y,a_sqr,b_sqr, fx,fy, d;
    float tmp1,tmp2;
    x=0;
    y=b;
    a_sqr=a*a;
    b_sqr=b*b;
    fx=2*b_sqr*x;
    fy=2*a_sqr*y;
    d=b_sqr-(a_sqr*b)+(a_sqr*0.25);
    do
    {
        draw_4points(hdc,xc,yc,x,y,c);
        if(d<0)
        {
            d=d+fx+b_sqr;
        }
        else
        {
            y=y-1;
            d=d+fx+b_sqr-fy;
            fy=fy-(2*a_sqr);
        }
        x=x+1;
        fx=fx+(2*b_sqr);
    }
    while(fx<fy);
    tmp1=(x+0.5)*(x+0.5);
    tmp2=(y-1)*(y-1);
    d=b_sqr*tmp1+a_sqr*tmp2-(a_sqr*b_sqr);
    do
    {
        draw_4points(hdc,xc,yc,x,y,c);
        if(d>0)
            d=d-fy+a_sqr;
        else
        {
            x=x+1;
            d=d+fx-fy+a_sqr;
            fx=fx+(2*b_sqr);
        }
        y=y-1;
        fy=fy-(2*a_sqr);
    }
    while(y>=0);
}


///curves
void draw_Curve_bezier(HDC hdc,int x1,int y1,int x2,int y2,int x3,int y3,int x4,int y4,COLORREF c)
{
    int x,y;
    for(double t=0;t<1;t+=0.001)
    {
        x = (1-3*t+3*pow(t,2)-pow(t,3))*x1+(3*t-6*pow(t,2)+3*pow(t,3))*x2+(3*pow(t,2)-3*pow(t,3))*x3+pow(t,3)*x4;
        y= (1-3*t+3*pow(t,2)-pow(t,3))*y1+(3*t-6*pow(t,2)+3*pow(t,3))*y2+(3*pow(t,2)-3*pow(t,3))*y3+pow(t,3)*y4;
        setMypixel(hdc,x,y,c);
    }
}
void draw_Curve_hermit(HDC hdc,int x1,int y1,int x2,int y2,int x3,int y3,int x4,int y4,COLORREF c)
{
    int a1,b1,c1,d1;
    a1= 2 * x1 + x2 - 2 * x3 + x4;
    b1= -3 * x1 -2* x2 + 3 * x3 - x4;
    c1= x2;
    d1= x1 ;
    int a2, b2, c2, d2;
    a2 = 2 * y1 + y2 - 2 * y3 + y4;
    b2 = -3 * y1 - 2 * y2 + 3 * y3 - y4;
    c2 = y2;
    d2 = y1;
    double x,y;
    for (double t = 0; t <= 1; t += 0.001)
    {
        x=a1*(t*t*t)+b1*(t*t)+c1*(t)+d1;
        y=a2*(t*t*t)+b2*(t*t)+c2*(t)+d2;
        setMypixel(hdc,round(x),round(y),c);
    }
}



/*  Declare Windows procedure  */
LRESULT CALLBACK WindowProcedure (HWND, UINT, WPARAM, LPARAM);

/*  Make the class name into a global variable  */
TCHAR szClassName[ ] = _T("CodeBlocksWindowsApp");

int WINAPI WinMain (HINSTANCE hThisInstance,
                     HINSTANCE hPrevInstance,
                     LPSTR lpszArgument,
                     int nCmdShow)
{
    HWND hwnd;               /* This is the handle for our window */
    MSG messages;            /* Here messages to the application are saved */
    WNDCLASSEX wincl;        /* Data structure for the windowclass */

    /* The Window structure */
    wincl.hInstance = hThisInstance;
    wincl.lpszClassName = szClassName;
    wincl.lpfnWndProc = WindowProcedure;      /* This function is called by windows */
    wincl.style = CS_DBLCLKS;                 /* Catch double-clicks */
    wincl.cbSize = sizeof (WNDCLASSEX);

    /* Use default icon and mouse-pointer */
    wincl.hIcon = LoadIcon (NULL, IDI_APPLICATION);
    wincl.hIconSm = LoadIcon (NULL, IDI_APPLICATION);
    wincl.hCursor = LoadCursor (NULL, IDC_ARROW);
    wincl.lpszMenuName = NULL;                 /* No menu */
    wincl.cbClsExtra = 0;                      /* No extra bytes after the window class */
    wincl.cbWndExtra = 0;                      /* structure or the window instance */
    /* Use Windows's default colour as the background of the window */
    wincl.hbrBackground = (HBRUSH) COLOR_BACKGROUND;

    /* Register the window class, and if it fails quit the program */
    if (!RegisterClassEx (&wincl))
        return 0;

    /* The class is registered, let's create the program*/
    hwnd = CreateWindowEx (
           0,                   /* Extended possibilites for variation */
           szClassName,         /* Classname */
           _T("Code::Blocks Template Windows App"),       /* Title Text */
           WS_OVERLAPPEDWINDOW, /* default window */
           CW_USEDEFAULT,       /* Windows decides the position */
           CW_USEDEFAULT,       /* where the window ends up on the screen */
           544,                 /* The programs width */
           375,                 /* and height in pixels */
           HWND_DESKTOP,        /* The window is a child-window to desktop */
           NULL,                /* No menu */
           hThisInstance,       /* Program Instance handler */
           NULL                 /* No Window Creation data */
           );

    CreateMainMenu(hwnd);

    /* Make the window visible on the screen */
    ShowWindow (hwnd, nCmdShow);

    /* Run the message loop. It will run until GetMessage() returns 0 */
    while (GetMessage (&messages, NULL, 0, 0))
    {
        /* Translate virtual-key messages into character messages */
        TranslateMessage(&messages);
        /* Send message to WindowProcedure */
        DispatchMessage(&messages);
    }

    /* The program return-value is 0 - The value that PostQuitMessage() gave */
    return messages.wParam;
}

/*  This function is called by the Windows function DispatchMessage()  */

LRESULT CALLBACK WindowProcedure (HWND hwnd, UINT message, WPARAM wParam, LPARAM lParam)
{
    static int x,y,xs,ys,xe,ye,a,b,x1,y1,x2,y2,x3,y3,x4,y4,xL,yU,xR,yD;
    static bool first=true;
    static int flag=0,poly_count=0;
    HDC hdc;
    static string algo="";
    static pair < int,int > point;
    COLORREF rgb=(0);
     switch (message)
	{
		case WM_CLOSE:
			DestroyWindow(hwnd);
			return 0;

        case WM_DESTROY:
            PostQuitMessage(0);
			return 0;

		case WM_COMMAND:
		{
			switch(LOWORD(wParam))
			{
				case ID_FILE_EXIT:
					PostQuitMessage(0);
					break;

                case ID_Save:
                    saveToFile();
                    hdc=GetDC(hwnd);
                    draw_container(hdc,RGB(255,0,0));
                    ReleaseDC(hwnd,hdc);
					break;

                case ID_Reload:

                    hdc=GetDC(hwnd);
                    loadFromFile(hdc,RGB(255,0,0));
                    ReleaseDC(hwnd,hdc);
                    break;

                case ID_Line_Parametric:
                    algo="lineP";
                    break;

				case ID_Line_DDA:
                    algo="lineD";
					break;

                case ID_Line_Midpoint:
                    algo="lineM";
                    break;

                case ID_Ellipse_Cartesian:
                    algo="EllipseC";
                    break;

                case ID_Ellipse_Polar:
                    algo="EllipseP";
                    break;

                case ID_Ellipse_MidPoint:
                    algo="EllipseM";
                    break;

                case ID_Curve_ThirdHermit:
                    algo="curveH";
                    break;

                case ID_Curve_ThirdBezier:
                    algo="curveB";
                    break;

                case ID_ClippingP:
                    algo="clippingP";
                    break;

			}

		} break;

        case WM_LBUTTONDOWN:
            if(algo=="lineP")
            {
                if(first)
                {
                    xs = LOWORD(lParam);
                    ys = HIWORD(lParam);
                    first=false;
                }
                else
                {
                    xe = LOWORD(lParam);
                    ye = HIWORD(lParam);
                    first=true;
                    hdc=GetDC(hwnd);
                    DrawLineParamtaric(hdc,xs,ys,xe,ye,rgb);
                    ReleaseDC(hwnd,hdc);
                }
            }
            else if(algo=="lineD")
            {
                if(first)
                {
                    xs = LOWORD(lParam);
                    ys = HIWORD(lParam);
                    first=false;
                }
                else
                {
                    xe = LOWORD(lParam);
                    ye = HIWORD(lParam);
                    first=true;
                    hdc=GetDC(hwnd);
                    DrawLineDDA(hdc,xs,ys,xe,ye,rgb);
                    ReleaseDC(hwnd,hdc);
                }
            }
            else if(algo=="lineM")
            {
                if(first)
                {
                    xs = LOWORD(lParam);
                    ys = HIWORD(lParam);
                    first=false;
                }
                else
                {
                    xe = LOWORD(lParam);
                    ye = HIWORD(lParam);
                    first=true;
                    hdc=GetDC(hwnd);
                    DrawLineMadPoint(hdc,xs,ys,xe,ye,rgb);
                    ReleaseDC(hwnd,hdc);
                }
            }
            else if(algo=="EllipseC")
            {
                if(flag==0)
                {
                    x = LOWORD(lParam);
                    y = HIWORD(lParam);
                    flag=1;
                }
                else if(flag==1)
                {
                    xs = LOWORD(lParam);
                    ys = HIWORD(lParam);
                    flag=2;
                }
                else if(flag==2)
                {
                    xe = LOWORD(lParam);
                    ye = HIWORD(lParam);
                    a=abs(xs-x);
                    b=abs(ye-y);
                    hdc=GetDC(hwnd);
                    draw_ellipse_Cartesian(hdc,x,y,a,b,rgb);
                    ReleaseDC(hwnd,hdc);
                    flag=0;
                }
            }
            else if(algo=="EllipseP")
            {
                if(flag==0)
                {
                    x = LOWORD(lParam);
                    y = HIWORD(lParam);
                    flag=1;
                }
                else if(flag==1)
                {
                    xs = LOWORD(lParam);
                    ys = HIWORD(lParam);
                    flag=2;
                }
                else if(flag==2)
                {
                    xe = LOWORD(lParam);
                    ye = HIWORD(lParam);
                    a=abs(xs-x);
                    b=abs(ye-y);
                    hdc=GetDC(hwnd);
                    draw_ellipse_Polar(hdc,x,y,a,b,rgb);
                    ReleaseDC(hwnd,hdc);
                    flag=0;
                }
            }
            else if(algo=="EllipseM")
            {
                if(flag==0)
                {
                    x = LOWORD(lParam);
                    y = HIWORD(lParam);
                    flag=1;
                }
                else if(flag==1)
                {
                    xs = LOWORD(lParam);
                    ys = HIWORD(lParam);
                    flag=2;
                }
                else if(flag==2)
                {
                    xe = LOWORD(lParam);
                    ye = HIWORD(lParam);
                    a=abs(xs-x);
                    b=abs(ye-y);
                    hdc=GetDC(hwnd);
                    draw_ellipse_mid_point(hdc,x,y,a,b,rgb);
                    ReleaseDC(hwnd,hdc);
                    flag=0;
                }
            }
            else if(algo=="curveH")
            {
                if(flag==0)
                {
                    x1 = LOWORD(lParam);
                    y1 = HIWORD(lParam);
                    flag=1;
                }
                else if (flag==1)
                {
                    x2 = LOWORD(lParam);
                    y2 = HIWORD(lParam);
                    flag=2;
                }
                else if (flag==2)
                {
                    x3 = LOWORD(lParam);
                    y3 = HIWORD(lParam);
                    flag=3;
                }
                else if (flag==3)
                {
                    x4 = LOWORD(lParam);
                    y4 = HIWORD(lParam);
                    hdc=GetDC(hwnd);
                    draw_Curve_hermit(hdc,x1,y1,x2,y2,x3,y3,x4,y4,rgb);
                    ReleaseDC(hwnd,hdc);
                    flag=0;
                }
            }
            else if(algo=="curveB")
            {
                if(flag==0)
                {
                    x1 = LOWORD(lParam);
                    y1 = HIWORD(lParam);
                    flag=1;
                }
                else if (flag==1)
                {
                    x2 = LOWORD(lParam);
                    y2 = HIWORD(lParam);
                    flag=2;
                }
                else if (flag==2)
                {
                    x3 = LOWORD(lParam);
                    y3 = HIWORD(lParam);
                    flag=3;
                }
                else if (flag==3)
                {
                    x4 = LOWORD(lParam);
                    y4 = HIWORD(lParam);
                    hdc=GetDC(hwnd);
                    draw_Curve_bezier(hdc,x1,y1,x2,y2,x3,y3,x4,y4,rgb);
                    ReleaseDC(hwnd,hdc);
                    flag=0;
                }
            }
            else if(algo=="clippingP")
            {
                if (flag ==0)
                {
                    xL = LOWORD(lParam);
                    yD = HIWORD(lParam);
                    flag=1;
                }
                else if (flag ==1)
                {
                    xR = LOWORD(lParam);
                    yU = HIWORD(lParam);
                    ///drawing the rectangle
                    hdc = GetDC(hwnd);
                    DrawLine(hdc, xL, yU, xL, yD, rgb);
                    DrawLine(hdc, xR, yU, xR, yD, rgb);
                    DrawLine(hdc, xR, yD, xL, yD, rgb);
                    DrawLine(hdc, xR, yU, xL, yU, rgb);
                    ReleaseDC(hwnd,hdc);
                    flag=2;
                }
                else if (flag==2)
                {
                    x = LOWORD(lParam);
                    y = HIWORD(lParam);
                    point.first=x;
                    point.second=y;
                    poly_points.push_back(point);
                    flag=3;
                }
                if (flag==3 &&poly_count<5)
                {
                    if(poly_points.size()>=2)
                    {
                        hdc = GetDC(hwnd);
                        poly_Clipping(hdc,xL, yU, xR, yD,rgb);
                        ReleaseDC(hwnd,hdc);
                    }
                    flag=2;
                    poly_count++;
                }
                ///Redraw clipped polygon
                if(poly_count>=5)
                {
                    flag=0;
                    poly_count=0;
                    poly_points.clear();
                }
            }
            break;

		default:
			return DefWindowProc(hwnd,message,wParam,lParam);
	}

    return 0;
}
