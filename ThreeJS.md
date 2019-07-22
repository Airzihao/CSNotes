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
