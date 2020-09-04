#### 判断点是否在三角形的内部

> ##### 题目描述
>
> 给定平面直角坐标系下的三个点 $(x_{v_1}, y_{v_1}), (x_{v_2}, y_{v_2}), (x_{v_3}, y_{v_3})$，连接这三个点可以围成一个三角形区域（题目保证这三个点可以构成一个三角形）。坐标系中有 $n$ 个坐标点 $(x_i, y_i), 1 \le i \le n$ ，判断这些坐标点是否在这个三角形区域的内部（不包含三角形区域的边界）。输入有三行，第一行输入 $6$ 个浮点型数字，以空格分隔，分别代表 $x_{v_1}, y_{v_1}, x_{v_2}, y_{v_2}, x_{v_3}, y_{v_3}$ ，第二行输入一个整数 $n$ ，第三行是 $2 \times n$ 个浮点型数字，以空格分隔，$x_1, y_1, x_2, y_2, \dots, x_n, y_n$ ，代表 $n$ 个坐标点。输出 $n$ 行， 每一行是 `YES` 或者 `NO`，分别表示坐标点 $(x_i, y_i)$ 是否在三角形内部。
>
> ###### 示例 $1$
>
> **输入**
>
> ```markdown
> 0.0 1.0 0.0 0.0 1.0 0.0
> 3
> 1.0 -2.0 0.5 0.5 0.15 0.82
> ```
>
> **输出**
>
> ```markdown
> NO
> NO
> YES
> ```
>
> 



#### 参考思路

> 三个点可以连成一个三角形的条件是三个点不在同一条直线上。我们知道平面直角坐标系中两个点可以唯一确定一条直线，而一条直线把平面分成了两个区域。平面直角坐标系中直线的表示方式有很多，比较常见$\&$常用的表示方式有：
>
> - 一般式： $Ax+By+C=0$
>
> - 点斜式： $y-y_0=k(x-x_0)$ （不能表示平行于 $y$ 轴的直线）
>
> - 斜截式： $y=kx+b$ （不能表示平行于 $y$ 轴的直线）
>
> - 两点式： $\frac {x - x_1} {x_1 - x_2} = \frac {y - y_1} {y_1 - y_2}(x_1 \ne x_2, y_1 \ne y_2)$ （不能表示平行于 $x$ 轴或 $y$ 轴的直线）
>
> **斜截式是代码中比较方便表示和计算的一种形式，推荐使用这种表示方式**。
>
> ![直线][]
>
> 在上面的图 $1$ 中，$N$ 点在直线上，那么 $y_n = kx_0 + b$ ；$V$点在直线的上方区域，那么 $y_v > y_n$ ；$B$ 点在直线的下方区域，那么 $y_b < y_n$ 。所以，**对于一个点 $(x_0, y_0)$ 和一条直线 $y=kx+b$ ，可以根据 $y_0$ 与 $kx_0 + b$ 的大小关系，即 $y_0 - (kx_0 + b)$ 与 $0$ 的大小关系来确定这个点是在直线上，还是在直线的某一侧。**
>
> 三角形可以看做是三条直线围成的区域，其中两个点连成一条直线，第三个点在这条直线的某一侧（不会落在直线上）。
>
> ![三角形][]
>
> 在上面的图 $2$ 中， 假设三角形的三个顶点分别是 $A$ 、 $B$ 、 $C$ ，点 $C$ 在 $A$ 和 $B$ 连成的直线的上方，那么所有三角形内部的点，一定都与点 $C$ 在直线 $AB$ 的同一侧（图中是直线 $AB$ 的上方区域），比如 点 $G$ 不与点 $C$ 在直线 $AB$ 的同一侧，那么点 $G$ 一定不在三角形区域内。
>
> 根据上面的分析，可以得出一个判断坐标点 $P$ 是否在三角形区域 $ABC$ 内部的方法：**从三角形的三个顶点中取两个顶点连成一条直线，如果对于三种取顶点的方法，点 $P$ 都 与三角形的第三个顶点在直线的同一侧区域，那么点 $P$ 一定在三角形区域的内部。**
>
> 前面的分析可以知道，一个点 $(x_0, y_0)$ 在直线的哪一侧可以根据 $y_0 - (kx_0 + b)$ 的正负号来判断，那么两个点 $(x_1, y_1), (x_2, y_2)$ 是否在直线的同一侧，可以根据 $y_1 - (kx_1 + b)$ 与 $y_2 - (kx_2 + b)$ 是否同号来判断，也就是 $(y_1 - (kx_1 + b)) \times (y_2 - (kx_2 + b))$ 是否大于 $0$ 。
>
> *注意：对于坐标点落在三角形边上以及三角形的某一条边垂直于 $y$ 轴的情况需要特殊考虑、特殊计算*



[直线]: ../../pictures/line.PNG
[三角形]: ../../pictures/triangle.PNG

#### 参考实现

```c++
#include <cmath>
#include <iostream>
using namespace std;

const double epsilon = 1e-7;

class Point {
public:
    double x;
    double y;
    explicit Point(double _x = 0.0, double _y = 0.0): x(_x), y(_y){}
};

/* 判断点p3和p是否在点p1和p2连成的直线的同一侧 */
bool are_points_on_same_side_of_line(Point p1, Point p2, Point p3, Point p) {
    double delta_x = p1.x - p2.x;

    /* 直线垂直于x轴，直线方程是 x = p1.x */
    if (fabs(delta_x) <= epsilon) {
        return (p3.x - p1.x) * (p.x - p1.x) > 0;
    }

    double k = (p1.y - p2.y) / delta_x;
    double b = p1.y - k * p1.x;
    return (p3.y - (k * p3.x + b)) * (p.y - (k * p.x + b)) > 0;
}

/* 判断点p是否在点p1、p2、p3连成的三角形的内部 */
bool is_point_inside_triangle(Point p1, Point p2, Point p3, Point p) {
    return are_points_on_same_side_of_line(p1, p2, p3, p) &&
           are_points_on_same_side_of_line(p1, p3, p2, p) &&
           are_points_on_same_side_of_line(p2, p3, p1, p);
}

int main() {
    Point p1, p2, p3, p;
    int n;

    cin >> p1.x >> p1.y >> p2.x >> p2.y >> p3.x >> p3.y;
    cin >> n;

    while (n--) {
        cin >> p.x >> p.y;
        if (is_point_inside_triangle(p1, p2, p3, p)) {
            cout << "YES" << endl;
        } else {
            cout << "NO" << endl;
        }
    }
    return 0;
}
```
