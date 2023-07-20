# Work Notes 2023.5.6

#### 如何根据x,y,z,raw,pitch,roll位姿数据在UE4里进行截图采样。

```python
import csv
import unreal
# 获取当前场景
editor_level = unreal.EditorLevelLibrary.get_editor_world()
# 查找门模型Actor
door_actor = unreal.EditorLevelLibrary.find_actor_by_name(editor_level, "GateActor")
if door_actor is not None:
    csv_file = "pose_data.csv"
    poses = []
    # 读取csv文件并获取位姿信息
    with open(csv_file, "r") as file:
        reader = csv.reader(file)
        for row in reader:
            # 将位姿信息存储为列表
            poses.append(row[0:6])
    # 遍历每条位姿信息，对门模型进行设置并截屏
    for pose_id, pose in enumerate(poses):
        x, y, z, roll, pitch, yaw = pose
        location = unreal.Vector(float(x), float(y), float(z))
        rotation = unreal.Rotator(float(pitch), float(yaw), float(roll))
        # 设置门模型Actor的位置和旋转
        door_actor.set_actor_location_and_rotation(location, rotation)
        # 请求UE4截屏
        file_path = "screenshots/pose_{}.png".format(pose_id)
        img_width = 1920
        img_height = 1080
        reader_mode = unreal.EPixelFormat.PF_B8G8R8A8
        set_alpha = True
        unlit = True
        highres = True
        unreal.SystemLibrary.request_screenshot2(door_actor, file_path, img_width, img_height, reader_mode, set_alpha, unlit, highres)
    unreal.log_warning("截屏完成！")
else:
    unreal.log_error("无法找到门模型Actor！")
```

这个Python脚本包括以下步骤：

1. 获取当前场景和门模型Actor对象
2. 读取csv文件并获取位姿信息
3. 遍历所有位姿信息，对门模型进行设置并截屏
4. 保存截图并在控制台中输出完成信息或错误信息

注意保持csv文件和门模型Actor的名称正确。同时，需要在UE4环境中设置Python接口才能成功运行此脚本。

#### 运行报错！AttributeError: module 'unreal' has no attribute 'EditorLevelLibrary'

> 这个错误通常是因为您未正确设置Python接口，导致Python无法访问UE4编辑器级别的API。请确保根据以下步骤设置Python接口：
>
> 1. 在UE4编辑器中打开您的项目。
> 2. 转到Edit > Plugins。
> 3. 在左侧的分类列表中选择Scripting。
> 4. 在右侧的插件列表中启用Python Editor Script Plugin。
> 5. 重启UE4编辑器以使更改生效。
>
> 请确保在执行脚本之前重新启动编辑器，因为Python脚本需要新的运行时才能重新加载插件。如果已经启用了Python Editor Script插件并重启了编辑器，但仍然遇到此错误，就检查python的脚本是否正确导入了unreal模块。可以在脚本开头添加以下行测试：

```python
import unreal
print(dir(unreal))
```

这将列出unreal模块的所有属性和方法，包括EditorLevelLibrary。如果找不到EditorLevelLibrary这个属性，那么可能需要重新安装或更新UE4。

#### 运行Python脚本时不能直接在第三方IDE运行，会出现模块缺失的问题。

Python脚本是在UE4编辑器中的Python控制台或蓝图脚本中运行的。通过以下步骤在UE4中启动Python控制台：

1. 打开UE4项目。
2. 转到Window > Developer Tools > Python Console。
3. 在控制台窗口中，单击鼠标右键以显示菜单，然后选择Run Script File (运行脚本文件)。
4. 在打开的文件对话框中，选择包含要运行的Python脚本的文件并点击Open (打开)。

蓝图脚本的运行方式与Python控制台略有不同。需要在蓝图中创建Python脚本节点，然后将代码复制到该节点中。执行蓝图时，UE4将运行这个Python脚本节点。

注：如果Python脚本需要使用UE4编辑器级别的功能（例如关卡编辑器、Actor、Material等），则需要在启动UE4时将Editor选项设置为True，并在运行Python控制台或蓝图脚本时激活该选项。

#### 如何在启动UE4时将Editor选项设置为True，并在运行Python控制台或蓝图脚本时激活该选项。

1. 找到UE4Editor.exe的可执行文件。它通常位于以下位置：`C:\Program Files\Epic Games\UE_<version number>\Engine\Binaries\Win64`。
2. 右键单击UE4Editor.exe并选择 "创建快捷方式"。
3. 在新的快捷方式上右键单击，然后选择 "Properties"（属性）选项。
4. 在 "Shortcut" （快捷方式）选项卡中找到 "Target" （目标）输入框。
5. 在该输入框中添加编辑器启动参数 `-Editor` 以启动编辑器，例如："C:\Program Files\Epic Games\UE_\Engine\Binaries\Win64\UE4Editor.exe" -Editor。

在运行Python控制台或蓝图脚本时激活该选项：

1. 在创建Python控制台或蓝图脚本节点之前，选择Edit > Editor Preferences。
2. 找到"Python"菜单选项，并在下面找到"Automatically load UClasses for Variables of Type" (自动加载类型变量的UClasses)选项。
3. 将该选项设置为"Always" (始终)。

![image-20230506170641459](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230506170641459.png)

#### 在UE4.26中运行Python的方法有所不同，如下步骤所示：

1. 打开 UE4 编辑器，并打开要运行 Python 脚本的项目。
2. 转到顶部菜单栏中的 "Window"（窗口）选项。
3. 在弹出的下拉菜单中，选择 "Developer Tools"（开发者工具）选项。
4. 在 "Developer Tools" 菜单下面，单击 "Output Log"（输出日志）。
5. 在输出日志窗口中，在文本框中输入 `Python` 并按下回车键。这将打开 Unreal 的 Python 控制台（Python Console）。
6. 在控制台中，输入 `execfile("<path-to-script>")` 命令，其中 `<path-to-script>` 是您要执行的 Python 脚本的完整路径。

代码示例：

```python
execfile "C:/My Project/MyScript.py"
```

#### 在UE4 项目中启用 Python 插件并设置 Python 环境

1. 打开 UE4 编辑器，打开项目。
2. 转到顶部菜单栏中的 "Edit"（编辑）选项，并选择 "Plugins"（插件）选项。
3. 在 "Plugins" 窗口中，选择 "Scripting" 类别。
4. 在 "Scripting" 类别下方，将 "Python Editor Script Plugin" 的开关设置为 "Enabled"（已启用）状态。
5. 如果想让所有项目都使用相同的 Python 环境，请将 "Use Shared Python Installation" 的开关设置为 "Enabled" 状态。然后，根据您的系统配置，选择您的 Python 安装或虚拟环境的路径。
6. 如果想为每个项目设置不同的 Python 环境，请将 "Use Project Specific Python Environment" 的开关设置为 "Enabled" 状态。然后，在 "Project Specific Python Environment" 部分中，选择您想要使用的 Python 可执行文件路径，并指定虚拟环境路径（如果使用虚拟环境）。