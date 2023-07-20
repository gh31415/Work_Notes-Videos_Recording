## Work notes 2023.5.9

* 解决airsimdroneracingvae 接口同现版本不匹配的问题。

> 经过开发文档的反复对比，以及chatgpt的合理使用，终于得出了该无人机仿真框架airsimdroneracingvae基于的AirSim版本是1.2.0，因此根据对应的版本去git clone。！注意别放在C盘，放在其它盘里。

1. 打开 Developer Command Prompt for VS2022

```
git clone https://github.com/microsoft/AirSim.git
```

2. ```
   cd AirSim
   ```

3. ```
   build.cmd
   ```

![image-20230509164117793](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20230509164117793.png)