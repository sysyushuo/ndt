pure ndt-tku base autoware,
this moudle contain mapping and matching

### 建图模块：
#### 1.建图模块默认不保留建图时行驶路径及pcd建图文件，如需保留请修改 ndt_mapping.launch文件中的path_flag 为true。
#### 2.建图模块运行完成后保存为pcd点云文件，路径在ndt_mapping.launch中的pcd_path,如需改变保存路径，请修改相应代码。


### 重定位模块
#### 1.将建图模块生成的pcd点云文件放到 路径ndt_test/src/lidar_localizer/map_data/*.pcd下


### 需要修改的参数提示:
#### map_loader.launch 中的pcd_path 为pcd点云地图存放路径
#### ndt_mapping.launch 中的path_flag为建图阶段是否保留pcd及path文件为file_name,保存路径为pcd_path,lidar_topic为点云topic
#### ndt_matching.launch 为重定位启动文件
#### points_downsample.launch 为点云降采样启动文件
#### static_tf.launch为坐标系间转换文件


### 编译及启动
#### 1.catkin_make
#### 2.source devel/setup.sh
#### 3.roslaunch lidar_localizer ndt_mapping.launch （建图) 或 roslaunch lidar_localizer lidar_localizer.launch （重定位)

### 步骤说明
#### 1.先建图启动mapping后保留pcd点云地图及path文件，path文件格式为 时间(s) 0(定位状态) x y z(位置) q.w q.x q.y q.z(四元数姿态)
#### 2.建图完成后启动lidar_loalization可以基于pcd点云地图进行匹配，实时输出位姿。
