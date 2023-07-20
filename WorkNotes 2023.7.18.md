# WorkNotes 2023.7.18

1. 解决使用client.simSpawnObject()函数实现门生成问题。

* client端代码实现：

```python
    def simSpawnObject(self, object_name, asset_name, pose, scale, physics_enabled=False, is_blueprint=False):
        """Spawned selected object in the world

        Args:
            object_name (str): Desired name of new object
            asset_name (str): Name of asset(mesh) in the project database
            pose (airsim.Pose): Desired pose of object
            scale (airsim.Vector3r): Desired scale of object
            physics_enabled (bool, optional): Whether to enable physics for the object
            is_blueprint (bool, optional): Whether to spawn a blueprint or an actor

        Returns:
            str: Name of spawned object, in case it had to be modified
        """
        return self.client.call('simSpawnObject', object_name, asset_name, pose, scale, physics_enabled, is_blueprint)
```

* server端代码实现：

```c++
std::string WorldSimApi::spawnObject(const std::string& object_name, const std::string& load_object, const WorldSimApi::Pose& pose, const WorldSimApi::Vector3r& scale, bool physics_enabled, bool is_blueprint)
{
FString asset_name(load_object.c_str());
FAssetData* load_asset = simmode_->asset_map.Find(asset_name);

if (!load_asset->IsValid()) {
    throw std::invalid_argument("There were no objects with name " + load_object + " found in the Registry");
}

// Create struct for Location and Rotation of actor in Unreal
FTransform actor_transform = simmode_->getGlobalNedTransform().fromGlobalNed(pose);

bool spawned_object = false;
std::string final_object_name = object_name;
```

**问题处理一：**为什么参数不匹配？一定要让在pycharm中pip install的airsim包版本和在UE4中编译的airsim包版本一致，这样才能保证参数数量匹配。

**问题处理二：**为什么不能生成模型？一定要注意这个API仅通过名称去搜索，所以传入参数的时候只能传入模型的名称。

**问题处理三：**为什么传入的pose和scale参数会报错？必须得使用`airsim.Vector3r`和`airsim.Quaternionr`这两个表示位置和旋转的API来获取airsim能够处理的对象，同时传入的scale也需要做相应的处理。简单示例如下：

```python
import airsim

client = airsim.VehicleClient()
client.confirmConnection()
resource_type = "RedGate"  # 资源类型为静态网格模型
# resource_name = "E:/desert/gate.fbx"  根据AirSim server端C++代码的提示，这里应该不能用路径，而要用名称去搜索
resource_name = "gate"

position = airsim.Vector3r(0, 0, 600)  # 资源的位置，这里将资源放置在空中
rotation = airsim.Quaternionr(0, 0, 0, 1)  # 资源的旋转，这里使用默认的旋转
pose = airsim.Pose(position, rotation)
scale = airsim.Vector3r(0.68, 0.34, 0.5)
client.simSpawnObject(resource_type, resource_name, pose, scale)
```

2. 解决无人机能够执行训练好的控制器，并且调整了初始位置参数，能够在FOV中看到门的信息，但是无人机不能够钻框，如下所示，会被“空气”挡住，因为使用simSpawnObject生成门时，碰撞预设是默认值，所以无人机会被空气碰撞盒挡住，如下所示：

![image-20230719014259088](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230719014259088.png)

不能使用python中的API来做到碰撞预设的设置，只能对模型下手，双击门框进入资源处理界面，调节碰撞设置。

![image-20230720150558413](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230720150558413.png)
