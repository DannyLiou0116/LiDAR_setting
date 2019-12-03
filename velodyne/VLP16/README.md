# VLP16

電腦IP設定
===
先接上電源與網路線，聽到VLP16開始轉動

Ubuntu16.04的Network內的IPv4設置，
1. address 192.168.1.77  
2. netmask 255.255.255.0  
3. gateway 192.168.1.1  

上述不行的話就用veloview偵測自己的有線網路ip  
注意連上剛剛設置的有線網路時，無線網路需disconnect  

安裝ros依賴項
===
```
sudo apt-get install ros-VERSION-velodyne
```

像我是kinetic，就把VERSION改為kinetic

當然需要先在catkin_ws把velodyne驅動裝好
===
```
cd ~/catkin_ws/src
git clone https://github.com/ros_drivers/velodyne.git
cd ..
rosdep install --from-paths src --ignore-src --rosdistro YOURDISTRO -y
cd ~/catkin_ws/ && catkin_make
```

欲rviz顯示
===
1. 打開terminal 1: 輸入`roscore`
2. 打開terminal 2: 輸入`roslaunch velodyne_pointcloud VLP16_points.launch`
3. 打開terminal 3: 輸入`rosrun rviz rviz -f velodyne`
   1. 進入rviz介面
   2. 點擊add之後選擇pointcloud2再點擊ok
   3. 在新的pointcloud2區域的topic中選擇/velodyne_points.


