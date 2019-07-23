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