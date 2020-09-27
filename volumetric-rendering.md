## Introduction
## 介绍
Spheres, cubes and all the other complex geometries are made out of triangles in 3D egame engines, which are flat by definition. The real time lighting systems adopted by Unity is only capable of rendering flat surfaces. When you are rendering a sphere, for instance, Unity only draws the triangles that make its surface. Even for materials that are semi-transparent, only the outer shell is actually drawn and its color is blended with the one of the object behind it. There is no attempt in the lighting system to probe into a material’s volume. For the GPU, the world is made out of empty shells.

球体，立方体和其他复杂的几何图形在3D游戏引擎中都是由三角形组合起来的，这些三角形从定义上来说是平面的。Unity采用的实时光照系统只能够针对平面进行渲染。当你想要渲染球体时，实际上，Unity只是通过绘制三角形来构成它的表面。即使是半透明的材质，也只是绘制了它的外表面，而他的颜色是和处于后面的物体混合后呈现出来的。在光照系统中没有尝试去实现一种针对体积渲染的材质，对GPU而言，整个世界都是由一些空壳构成的。

A broad range of techniques exists, in order to overcome this strong limitation. Even though is true that a traditional shader ultimately stops at the very surface of an object, it doesn’t mean we can’t go deeper. Volume rendering techniques simulate the propagation of light rays into a material’s volume, allowing for stunning and sophisticated visual effects.
实际上存在很多技术可以用于克服这种限制。尽管传统着色器最终只是绘制在物体的表面上，这并不意味着我们不能实现更多。体积渲染技术模拟了光照在材质体积中的传播，以此来实现一些复杂而惊人的虚幻效果。

## Volumetric Rendering
## 体积渲染
The fragment shader of an unlit textured object looks like this:
以下是一个无光照的片段着色器：
``` glsl
fixed4 frag (v2f i) : SV_Target
{
    fixed4 col = tex2D(_MainTex, i.texcoord);
    return col;
}
```
Loosely speaking, that piece of code is invoked for every potential pixel (fragment) in the final rendered image. When the GPU invokes that fragment shader, it is because there is a triangle that is intersecting the camera frustum. In other words, the camera sees the object. Unity needs to know the exact colour of the object, so that it can assign it to the respective pixel in the rendered image.
他们分配个大致上来说，最终渲染的图像中每一个潜在的像素都会执行一次上述的代码。当GPU执行这些片段着色器时，是因为有三角形和相机的裁剪空间中存在交集。换句话说，相机看到了这个物体。Unity需要知道物体的具体颜色，才能将他们分配给渲染图像上的相应像素。

vol1

Fragment shaders ultimately return the colour of an object at a specific location, as seen from a specific angle. The way this colour is calculated is entirely arbitrary. Nothing forbids us to “cheat” and to return something that does not necessarily match with the geometry that we are rendering realistically. The following diagram shows an example of this when rendering a 3D cube. When the fragment shader is queried to get the colour of the cube’s face, we are returning the same colours we could see on a sphere. The geometry is a cube, but from the camera’s perspective it looks and feels exactly like a sphere.

片段着色器最终返回的是从特定角度上看某个物体在指定位置上的颜色，这种颜色的计算方法是不准确的，并没有人阻止我们“欺骗”和返回一些和我们实际渲染的几何并不匹配的东西。下面的图片展示了渲染一个3D立方体的例子。当片段着色器被请求获得立方体表面颜色的时候，我们可以返回球体部分的颜色。几何图形是立方体，但是对于相机的透视角度就会看起来像一个球体。

vol2

This is the basic concept behind volumetric rendering: simulating how light would propagate within the volume of an object.
在体积渲染中有一个基本的概念：模拟光线如何在物体的体积内传播

If we want to emulate the effect shown in the previous diagram we need to describe it more precisely. Let’s say that our main geometry is a cube, and we want to volumetrically render a sphere inside it. There is actually no geometry associated with the sphere, as we will render it entirely via shader code. Our sphere is centred at _Centreand has radius _Radius, both expressed in world coordinates. Moving the cube won’t affect the position of the sphere, since it is expressed into absolute world coordinates. It’s also worth noting that the external geometry serves no purposes and won’t change the rest of the tutorial. The triangles of the outer shell of the cube becomes portals that allow to see inside the geometry. We could save triangles by using a quad, but a cube allows to see the volumetric sphere from every angle.

如果我们想要实现上面展示的效果，我们需要更精确的描述它。我们假设我们的主几何物体是一个立方体，而我们想要把他渲染成一个球。实际上没有和球形相关的几何图形，我们要完全通过着色器代码来做到这一点。我们的球体有坐标中心（_Centre）和半径（_Radius），两者都可以用世界坐标描述。由于都使用了世界坐标描述，所以移动立方体并不会影响到球体的位置。值得注意的是外部几何图形没有任何用处，也不会再影响接下来的教程。我们可以正方形来保存三角形，但是立方体需要从每一个角度都看上去像一个球。
## Volumetric Raycasting
## 体积射线检测
The first approach to volumetric rendering works exactly like the previous diagram. The fragment shader receives the point we are rendering ( worldPosition) and the direction we are looking at ( viewDirection); it then uses a function called raycastHit that indicates whether we are hitting the red sphere or not. This technique is called volumetric raycasting, as it extends the rays cast from the camera into the geometry.
这是让体积渲染能实现上面效果的第一要素。片段着色器获得了我们想要渲染的坐标（worldPosition）和我们看过去的方向（viewDirection），然后他使用了一个名为（raycastHit ）的函数来检测我们是否匹配到了红色球体。这种技术我们称之为体积射线检测，他由相机发射向几何物体。
We can now write a stub for our fragment shader:
我们可以把我们的片段着色器简写为：
``` glsl
float3 _Centre;
float _Radius;

fixed4 frag (v2f i) : SV_Target
{
    float3 worldPosition = ...
    float3 viewDirection = ...
    if ( raycastHit(worldPosition, viewDirection) )
        return fixed4(1,0,0,1); // Red if hit the ball
    else
        return fixed4(1,1,1,1); // White otherwise
}
```

Let’s tackle each one of those missing components.
让我们来处理每一个缺失的组件

## World Position
## 世界坐标
Firstly, the world position of the fragment is the point where the rays generated from the camera hit the geometry. We have seen in Vertex and Fragment Shaderhow to retrieve the world position in a fragment shader:

首先，片段着色器中的世界坐标代表了由相机发射的射线接触到几何体表面的点。我们在[顶点和片段着色器]()中了解了如何在片段着色器中检索世界坐标：
``` glsl
struct v2f {
    float4 pos : SV_POSITION;    // Clip space
    float3 wPos : TEXCOORD1;    // World position
};

v2f vert (appdata_full v)
{
    v2f o;
    o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
    o.wPos = mul(_Object2World, v.vertex).xyz; 
    return o;
}
```
## View Direction
## 视角
Secondly, the view direction is the direction of the ray that comes from the camera and hits the geometry at the point we are rendering. It requires us to know the position of the camera; which Unity includes in the built-in variable _WorldSpaceCameraPos. The direction of a segment that passes through these two points can be calculated as follows:
其次，视角就是射线从摄像机射到我们渲染的几何体上点的方向，这需要我们知道相机的坐标。这Unity在内置变量中提供了（_WorldSpaceCameraPos）。通过这两个点的线段的方向可以以下面的方式计算：
``` glsl
float3 viewDirection = normalize(i.wPos - _WorldSpaceCameraPos);
```
## Raycast Hit Function
## 射线检测函数
What we need now is a function raycastHit that, given the point we are rendering and the direction we are looking at it from, determines if we are hitting the virtual red sphere or not. This is the problem of intersecting a sphere with a segment. Formulae for this problems exist (link), but they are generally very inefficient. If you want to go for an analytic approach, you will need to derive formulae to intersect segments with custom geometries. This solution strongly constraints the models you can create; consequently it is rarely adopted.

我们现在需要的是raycastHit函数，传入我们渲染的位置和朝向，判断我们是否匹配到了虚拟的红色球体。这是一个球体和线段相交的问题。这个问题的方程式[链接](http://www.realtimerendering.com/intersections.html)但他们通常效率很低。如果你想要实现一个解析法，那么你需要推导一个判断线段和自定义几何体相交的方程式。这个解决方案很大程度上限制了你可以使用的模型，因此很少采用。
## Volumetric Raymarching with Constant Step
## 定长步进式体积光照

As explained in the previous section, pure analytical volumetric raycasting is not a feasible approach to our problem. If we want to simulate arbitrary volumes, we need to find a more flexible technique that does not rely on intersecting equations. A common solution is called volumetric raymarching, and it is based on an iterative approach.
就像上面章节中描述的那样，单纯的解析体积光线检测不是一个可以解决我们问题的可行之法。如果我们想要模拟任意的体积，我们需要找到不基于相交方程的更为方便的技术。定长步进式体积光照是一个通常的解决方案，他是基于迭代的方式实现的。

Volumetric raymarching slowly extends the ray into the volume of the cube. At each step, it queries whether the ray is currently hitting the sphere or not.
步进式体积光照缓慢的延长射向立方体的射线。每一步，它都会判断射线是否碰撞到球体。

vol3
Each ray starts from the fragment position worldPosition, and is iteratively extended by STEP_SIZE units into the direction defined by viewDirection. This can be done algebraically adding STEP_SIZE * viewDirection to the worldPosition after each iteration.

We can now replace raycastHit with the following raymarchHit function:

每一个射线从片段着色器中的坐标worldPosition开始，他以STEP_SIZE 为单位长度迭代地在viewDirection方向上延伸。这可以通过在每次迭代时对worldPosition 代数相加STEP_SIZE * viewDirection

``` glsl
#define STEPS 64
#define STEP_SIZE 0.01

bool raymarchHit (float3 position, float3 direction)
{
    for (int i = 0; i < STEPS; i++)
    {
        if ( sphereHit(position) )
            return true;

        position += direction * STEP_SIZE;
    }

    return false;
}
```
The remaining piece of Maths required for this technique it to test whether a point p is inside a sphere:
这项技术所需的剩余数学部分是用来测试点p是否在球体内：
``` glsl
bool sphereHit (float3 p)
{
    return distance(p,_Centre) < _Radius;
}
```
Intersecting rays with spheres is hard, but iteratively checking if a point is inside a sphere is easy.

The result is this rather unsexy red sphere. Despite looking like a plain circle, this is actually an unlit sphere.

判断射线和球体的相交很坤丹，但是迭代式的检测一个点是否在球体内就非常简单。
这个不咋地的红色球体就是最终的结果。尽管看起来像一个普通的圆，但实际上这是一个无光照的球体。

rotate
## Conclusion
## 结论
This post introduces the concept of Volumetric Rendering. Even if traditional shader stops at the outer shell of a material, it is possible to keep projecting those rays inside a material’s volume to create the illusion of depth. Raymarching is one of the most commonly used technique. We have used is to draw an unlit red sphere. We will see in the following tutorials how to shade it realistically (Part 3. Surface Shading), how to make interesting shapes (Part 3. Signed Distance Fields) and even how to add shadows (Part 6. Hard and Soft Shadows). By the end of this series you’ll be able to create objects like the this one, with just three lines of code and a volumetric shader:
这部分介绍了体积渲染的概念。及时传统的着色器只能依附于材质的外表面，投射这些射线到材质体积来创造出深度的幻觉依然是可行的。步进检测是最常见的技术一直。我们用他绘制了一个无光照的红色球体。我们可以再下面的指引中看到如果更真实的给他着色（第3部分，[表面着色](https://www.alanzucconi.com/2016/07/01/surface-shading/)，如果绘制一些有趣的图形（第4部分，[有向距离函数](https://www.alanzucconi.com/2016/07/01/signed-distance-functions/)，给他们添加阴影（第6部分，[硬阴影和软阴影](https://www.alanzucconi.com/2016/07/01/shadow/)。在本文的最后，你可以只用3行代码和一个体积着色器来创造类似这样的物体：
aaaa
The next part of this tutorial will cover distance-aided raymaching, which is the de-facto standard technique used for volumetric rendering.
这个指引的下一部分会介绍距离辅助的步进光照。这是体积渲染实际上的标准技术。
