# Learning ThreeJS 

## 1. ABCs of ThreeJS
### 1.1 三大组件
`scene`,`camera`,`render`

```
var scene = new THREE.Scene();  // 场景
var camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);// 透视相机
var renderer = new THREE.WebGLRenderer();   // 渲染器
renderer.setSize(window.innerWidth, window.innerHeight);    // 设置渲染器的大小为窗口的内宽度，也就是内容区的宽度
document.body.appendChild(renderer.domElement);
```
场景是所有物体的容器，如果要显示一个苹果，就需要将苹果对象加入场景中。

相机决定了场景中那个角度的景色会显示出来，以及如何显示。

渲染器决定了渲染的结果应该画在页面的什么元素上面，并且以怎样的方式来绘制。渲染器renderer的domElement元素，表示渲染器中的画布，所有的渲染都是画在domElement上的，所以这里的appendChild表示将这个domElement挂接在body下面，这样渲染的结果就能够在页面中显示了。

### 1.2 添加物体到场景
```
var geometry = new THREE.CubeGeometry(1,1,1); 
var material = new THREE.MeshBasicMaterial({color: 0x00ff00});
var cube = new THREE.Mesh(geometry, material); 
scene.add(cube);
```

### 1.3 渲染
渲染函数的原型如下：

render( scene, camera, renderTarget, forceClear )

各个参数的意义是：
- scene：前面定义的场景
- camera：前面定义的相机
- renderTarget：渲染的目标，默认是渲染到前面定义的render变量中
- forceClear：每次绘制之前都将画布的内容给清除，即使自动清除标志autoClear为false，也会清除。

渲染分为实时渲染和离线渲染，下例是一个渲染循环。
```
function render() {
	cube.rotation.x += 0.1;
	cube.rotation.y += 0.1;
	renderer.render(scene, camera);
	requestAnimationFrame(render);
}
```

## 2. 点、线、面
右手坐标系
### 2.1 点
用`Vector`表示，`/src/math/Vector3.js`

这个点只是空间中的一个点，而不是图数据中的节点。点由THREE.Vector3表示，Threejs中没有提供单独画点的函数，它必须被放到一个THREE.Geometry形状中，这个结构中包含一个数组vertices，这个vertices就是存放无数的点（THREE.Vector3）的数组。
### 2.2 线


## 3. 渲染循环
使用requestAnimationFrame循环调用渲染函数。
```
function animate() {
	render();
	requestAnimationFrame( animate );
}
```

## 4. 性能监控
```
var stats;
在初始化中绑定
stats = new Stats();
stats.domElement.style.position = 'absolute';
stats.domElement.style.left = '0px';
stats.domElement.style.top = '0px';
//在渲染中刷新
stats.update()
```

## 5. 相机的使用
- 正投影相机THREE.OrthographicCamera，远近同大
- 透视投影相机THREE.PerspectiveCamera，进大远小

投影变换的目的就是定义一个视景体，使得视景体外多余的部分裁剪掉，最终图像只是视景体内的有关部分。
```
OrthographicCamera( left, right, top, bottom, near, far )
六个参数表示长方体6个面到镜头中间点的距离
var camera = new THREE.OrthographicCamera( width / - 2, width / 2, height / 2, height / - 2, 1, 1000 );
scene.add( camera );
```
```
PerspectiveCamera( fov, aspect, near, far )
//fov: 视场大小，单位是视角
//near: 近处截面的距离
//far: 远处截面的距离
//aspect： 实际窗口的横纵比，宽度除以高度。
var camera = new THREE.PerspectiveCamera( 45, width / height, 1, 1000 );
scene.add( camera );
```

## 6. 光源与材质
![image](https://github.com/Airzihao/MDPicturePool/raw/master/201907/7.png)

### 6.1 环境光
```
//THREE.AmbientLight( hex ) 十六进制颜色值
//无处不在，均匀影响，从不衰减
var light = new THREE.AmbientLight( 0xff0000 );
scene.add( light );
```
### 6.2 点光源
```
PointLight( color, intensity, distance )
//Color：光的颜色
//Intensity：光的强度，默认是1.0,就是说是100%强度的灯光，
//distance：光的距离，从光源所在的位置，经过distance这段距离之后，光的强度将从Intensity衰减为0。 默认情况下，这个值为0.0，表示光源强度不衰减。
```
### 6.3 聚光灯
```
THREE.SpotLight( hex, intensity, distance, angle, exponent )
//Hex：聚光灯发出的颜色，如0xFFFFFF
//Intensity：光源的强度，默认是1.0，如果为0.5，则强度是一半，意思是颜色会淡一些。和上面点光源一样。
//Distance：光线的强度，从最大值衰减到0，需要的距离。 默认为0，表示光不衰减，如果非0，则表示从光源的位置到Distance的距离，光都在线性衰减。到离光源距离Distance时，光源强度为0.
//Angle：聚光灯着色的角度，用弧度作为单位，这个角度是和光源的方向形成的角度。
//exponent：光源模型中，衰减的一个参数，越大衰减约快。
```
### 6.4 平行光
```
THREE.DirectionalLight = function ( hex, intensity )
//Hex：16进制光线颜色
//Intensity：光线的强度，默认为1。取值范围是0到1。如果为0，物体显示为黑色。
```

各种光源可以叠加。

## 7. 纹理
纹理是3D世界中的皮肤，3D世界的纹理由图片组成。
```
THREE.Texture( image, mapping, wrapS, wrapT, magFilter, minFilter, format, type, anisotropy )
//Image：这是一个图片类型，基本上它有ImageUtils来加载，如下代码
var image = THREE.ImageUtils.loadTexture(url); 
apping：是一个THREE.UVMapping()类型，它表示的是纹理坐标。
wrapS：表示x轴的纹理的回环方式，就是当纹理的宽度小于需要贴图的平面的宽度的时候，平面剩下的部分应该以何种方式贴图的问题。
wrapT：表示y轴的纹理回环方式。 magFilter和minFilter表示过滤的方式，这是OpenGL的基本概念.
format：表示加载的图片的格式，这个参数可以取值THREE.RGBAFormat，RGBFormat等。THREE.RGBAFormat表示每个像素点要使用四个分量表示，分别是红、绿、蓝、透明来表示。RGBFormat则不使用透明，也就是说纹理不会有透明的效果。
type：表示存储纹理的内存的每一个字节的格式，是有符号，还是没有符号，是整形，还是浮点型。这里默认是无符号型（THREE.UnsignedByteType）。
anisotropy：各向异性过滤。使用各向异性过滤能够使纹理的效果更好，但是会消耗更多的内存、CPU、GPU时间。
```
纹理坐标：

![image](https://github.com/Airzihao/MDPicturePool/raw/master/201907/8.png)

**使用纹理的步骤：**
```
1. 画一个对象
var geometry = new THREE.PlaneGeometry( 500, 300, 1, 1 );
2. 为对象赋予纹理坐标
geometry.vertices[0].uv = new THREE.Vector2(0,0);
geometry.vertices[1].uv = new THREE.Vector2(1,0);
geometry.vertices[2].uv = new THREE.Vector2(1,1);
geometry.vertices[3].uv = new THREE.Vector2(0,1);
逆时针方向
3. 加载纹理
var texture = THREE.ImageUtils.loadTexture("textures/a.jpg",null,function(t)
{
});
4. 将纹理用于材质
var material = new THREE.MeshBasicMaterial({map:texture});
var mesh = new THREE.Mesh( geometry,material );
scene.add( mesh );
```

## 8. 使用模型

模型加载的顺序：
![image](https://github.com/Airzihao/MDPicturePool/raw/master/201907/9.png)

### 8.1 模型在three.js中的表示
THREE.Mesh = function ( geometry, material )

- geometry：是一个THREE.Geometry类型的对象,他是一个包含顶点和顶点之间连接关系的对象。
- material：就是定义的材质。有了材质就能够让模型更好看，材质会影响光照、纹理对Mesh的作用效果。



