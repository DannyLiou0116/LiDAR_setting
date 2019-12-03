# ouster

先創好的python2.7虛擬環境，name為py2

安裝ouster-example
===

1. 把ouster_example clone在home底下 (不要clone再myworkspace底下就好，以下都是clone在home底下為例)  
2. 先 source activate py2
3. 再 export CMAKE_PREFIX_PATH=/home/doggy/ouster_example
4. 再 source /opt/ros/kinetic/setup.bash
5. 最後 mkdir -p myworkspace/src && cd myworkspace && ln -s /home/doggy/ouster_example ./src/ && catkin_make -DCMAKE_BUILD_TYPE=Release

承上，創 symbolic link 的 ln -s /home/doggy/ouster_example ./src/ 是因為我clone在/home/doggy底下(doggy為user名)，如果clone在別的路徑就再更改即可  
直接創一個workspace在home底下，資料夾名稱並非catkin_ws

在rviz秀出東西
===

1. source activate py2
2. source /opt/ros/kinetic/setup.bash
3. source /home/doggy/myworkspace/devel/setup.bash
4. 以上三點每個terminal都要寫
5. 開一個路徑在/home/doggy/myworkspace/src/ouster_example/ouster_ros底下的終端機輸入：roslaunch os1.launch replay:=true
6. 再開一個輸入：rosbag play *.bag --clock /kitti/velo/pointcloud:=/velodyne_points
7. 再開一個輸入rviz
   1. add pointcloud2
   2. topic 選好
   3. 最上面的 frame 要改 Ouster_sensors



把錄到bag file轉錄成只有點雲，topic為/os1_cloud_node/points的.bag
===

前面terminal可以全部關閉
---
```
source activate py2
source /opt/ros/kinetic/setup.bash
source /home/doggy/myworkspace/devel/setup.bash
```
1. 以上三列每個terminal都要輸入(或寫在bash.rc內，因為ros用的是python2.7，記得使用python3時要改回)
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
   1. 輸入 rosbag play --clock <bagfile>
   2. 代表開始讀一開始錄到的.bag檔並且開始轉錄
5. 再次提醒，錄完以後記得終端2要輸入ctrl+c自行中止，.bag.active檔才會轉為.bag檔
