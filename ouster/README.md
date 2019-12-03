# ouster

先創好的python2.7虛擬環境，name為py2

安裝ouster-example
=====

1. 把ouster_example clone在home底下 (不要clone再myworkspace底下就好，以下都是clone在home底下為例)  

```
source activate py2

export CMAKE_PREFIX_PATH=/home/doggy/ouster_example

source /opt/ros/kinetic/setup.bash

mkdir -p myworkspace/src && cd myworkspace && ln -s /home/doggy/ouster_example ./src/ && catkin_make -DCMAKE_BUILD_TYPE=Release
```

承上，創 symbolic link 的 ln -s /home/doggy/ouster_example ./src/ 是因為我clone在/home/doggy底下(doggy為user名)，如果clone在別的路徑就再更改即可  
直接創一個workspace在home底下，資料夾名稱並非catkin_ws

在rviz秀出東西
=====

1. 以下三列每個terminal都要輸入(或寫在bash.rc內，因為ros用的是python2.7，記得使用python3時要改回)

```
source activate py2
source /opt/ros/kinetic/setup.bash
source /home/doggy/myworkspace/devel/setup.bash
```
2. 開啟terminal 1:  
```
cd /home/doggy/myworkspace/src/ouster_example/ouster_ros
roslaunch os1.launch replay:=true
```
3. 開啟terminal 2輸入: `rosbag play --clock <你的bagfile>`
4. 再開一個terminal 3輸入rviz:
   1. add pointcloud2
   2. topic 選好
   3. 最上面的 frame 要改 Ouster_sensors

把錄到bag file轉錄成只有點雲，topic為/os1_cloud_node/points的.bag
=====

前面terminal可以全部關閉

1. 以下三列每個terminal都要輸入(或寫在bash.rc內，因為ros用的是python2.7，記得使用python3時要改回)

```
source activate py2
source /opt/ros/kinetic/setup.bash
source /home/doggy/myworkspace/devel/setup.bash
```

2. 開啟terminal 1跑roslaunch：

```
cd /home/doggy/myworkspace/src/ouster_example/ouster_ros ，底下才有os1.launch
roslaunch os1.launch replay:=true
```

3. 開啟terminal 2：
   1. 輸入 `rosbag record /os1_cloud_node/points`，會產生一個.bag.active檔，.active代表正在轉錄
   2. 第一行會看到Subscribing to /os1_cloud_node/points，/os1_cloud_node/points是之後會用到的點雲topic
   3. 第二行會看到Recording to 2019-10-17-00-44-08.bag. ，檔案名稱不一定，看你輸入指令的時間
   4. 承上，代表每次捨棄一個.bag.active都要重新輸入i.的，你後頭直接重複播放的話還是會錄到2019-10-17-00-44-08.bag.這份，重新輸入i.的指令才會有新的.bag.active檔可以用
   5. 錄完以後要記得ctrl+c中止，.bag.active檔才會轉為.bag檔
4. 開啟terminal 3：
   1. 輸入 `rosbag play --clock <你的bagfile>`
   2. 代表開始讀一開始錄到的.bag檔並且開始轉錄
5. 再次提醒，錄完以後記得終端2要輸入ctrl+c自行中止，.bag.active檔才會轉為.bag檔

建圖
=====

loam_ouster
---

參考: https://github.com/snakehaihai/LOAM_3D_LIDARs

clone在有ouster的myworkspace底下

用的是轉錄後的bag file，轉錄前的檔案無法輸出loam後的
-----

1. 開啟terminal 1依序輸入: 

```
source activate py2  
source /opt/ros/kinetic/setup.bash  
source ~/myworkspace/devel/setup.bash  
roslaunch loam_ouster  loam_ouster.launch  ，啟動LOAM。(但這邊不會順便開RViz，rviz要自己再開)
```

2. 創建一個空資料夾：
   1. 目的是放龐大pcd檔案與對應的.bag檔
   2. 創完後把剛剛的.bag檔案拉進新資料夾的路徑之下，再在此路徑打開終端2
3. 於終端2依序輸入：

```
source activate py2
source /opt/ros/kinetic/setup.bash
rosrun pcl_ros pointcloud_to_pcd input:=/velodyne_cloud_registered
```

輸入結束後別關掉  
此 terminal 目的是存放等等跑出來的pcd檔案們  
等等rosbag play之後，此 terminal 就會開始產生許多pcd

4. 在.bag檔的路徑下開啟終端3，並於終端3依序輸入：

```
source activate py2
source /opt/ros/kinetic/setup.bash
rosbag play *.bag --clock /os1_cloud_node/points:=/os1_node/points
```

bag 是要你在.bag檔路徑底下輸入自己的.bag檔名，但pcd會儲存終端2的路徑底下

如果.bag檔案一開始就放在空資料夾內，因為資料夾的bag檔只有一個所以維持*.bag即可

連結所有loam過後的pcd
---

1. pcd全儲存完畢後，開啟一路徑在pcd存放資料夾的terminal 4  
2. 於終端4輸入：pcl_concatenate_points_pcd *.pcd，得到地圖 點雲output.pcd。  

下載CloudCompare(Ubuntu16.04可一行指令就下載完畢)
---

連結參考: https://www.danielgm.net/cc/

開啟CloudCompare，再將output.pcd拖入，即可以看到建圖結果



