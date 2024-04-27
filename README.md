# Lissajous-figure-3D-SwiftUI-SceneKit
# The first part
![IMG_2059](https://github.com/S-way520/Lissajous-figure-3D-SwiftUI-SceneKit/assets/95877651/88db84c0-30d1-4bed-b4e9-e4eb631beb07)

# The Second part
Code file 1
```swift
import SwiftUI

@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

```
Code file 2
```swift
import SceneKit
import SwiftUI

struct ContentView: View {
    var body: some View {
        Lissajous3D()
    }
}


struct Lissajous3D: View {
    @State var frequencyX: Float = 3
    @State var frequencyY: Float = 2
    var body: some View {
        ZStack {
            ZStack {
                Lissajous3DView(
                    a: $frequencyX, 
                    b: $frequencyY
                )
            }
            HStack {
                Spacer()
                VStack {
                    Stepper(value: $frequencyX, in: 1...15) {
                        Text("频率X：\(Int(frequencyX))")
                            .foregroundColor(.green)
                    }
                    Stepper(value: $frequencyY, in: 1...15) {
                        Text("频率Y：\(Int(frequencyY))")
                            .foregroundColor(.red)
                    }
                    Spacer()
                }
                .frame(width: 200)
            }
            .padding()
        }
    }
}

struct Lissajous3DView: View {
    let curveNode = SCNNode()
    
    @Binding var a: Float // x 轴上的振幅
    @Binding var b: Float // y 轴上的振幅
    let delta: Float = 0.0 // 相位差
    
    var sine: SCNNode {
        var vertices: [SCNVector3] = []
        
        for t in stride(from: 0, through: 2 * Float.pi, by: 0.001) {
            let x = sin(a * t + delta)
            let y = sin(b * t)
            let z = cos(b * t)
            let vertex = SCNVector3(x, y, Float(z))
            vertices.append(vertex)
        }
        var indices: [Int32] = []
        for i in 0..<vertices.count {
            indices.append(Int32(i))
        }
        
        let sphereGeometry = SCNSphere(radius: 0.02) // 设置小球的半径
        sphereGeometry.firstMaterial?.diffuse.contents = UIColor.cyan // 设置小球的颜色为蓝色
        // 创建小球的节点
        let sphereNode = SCNNode(geometry: sphereGeometry)
        // 遍历你的几何体的顶点，将小球节点放置在每个顶点处
        for vertex in vertices {
            let pointNode = sphereNode.clone()
            pointNode.position = vertex // 将小球放置在顶点处
            // 将pointNode添加到你的场景中
            curveNode.addChildNode(pointNode)
        }
        return curveNode
    }
    
    var camera: SCNNode {
        let camera = SCNCamera()
        let cameraNode = SCNNode()
        cameraNode.position = .init(0, 0, 6)
        cameraNode.camera = camera
        return cameraNode
    }
    
    var scene: SCNScene {
        let scene = SCNScene()
        scene.background.contents = UIColor.systemBackground
        scene.rootNode.addChildNode(sine)
        scene.rootNode.addChildNode(camera)
        return scene
    }
    
    var body: some View {
        SceneView(
            scene: scene,
            options: [.autoenablesDefaultLighting, .allowsCameraControl]
        )
    }
}


```
