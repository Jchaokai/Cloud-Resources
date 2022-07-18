#### 坐标系统

- local space

    乘   model matrix 

    ↓

- world space

    乘  view matrix    就是将相机移到原点（平移→旋转→缩放），其他点也跟着相对移动

    ↓

- view space

    乘 projection matrix	相机内的东西才需要渲染 

    ↓

- clip space          锥体坐标(视角所表示的那个锥体)，转换成立方体坐标（ndc坐标 -1,1）

    我们把坐标转换成ndc坐标，-1到1之间，OpenGL才会帮我们画

    - 我们将裁剪坐标变换为屏幕坐标，我们将使用一个叫做视口变换(Viewport Transform)的过程

        ↓

- screen space

