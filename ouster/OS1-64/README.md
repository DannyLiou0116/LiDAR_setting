# OS1-64

**先裝好ouster_example**

https://github.com/ouster-lidar/ouster_example

注意ouster_example不能clone在myworkspace/src底下，不然在symbolic link時會陷入無限迴圈，個人是放在home底下即可

**電腦ip設定**

藉由ouster studio去偵測此時OS1-64的id
目前是用
  (1)address: 169.254.117.100
  (2)mask: 255.255.255.0
  (3)gateway: 169.254.117.254
  
  
**launch file的更動**

裡頭ip的default也要依照自己的os1-64去更改
replay模式在錄data時為false，之後播放時皆為true  
replay的default維持false，之後要播放再在roslaunch指令後加上replay:=true即可，如下列：

`roslaunch os1.launch replay:=true`
   

**錄製自己的bag file**

  (1)在你想要存放bag file的地方輸入：
  
  `rosbag record /os1_node/imu_packets /os1_node/lidar_packets`  
  
  (2)注意你錄影結束後要按ctrl+c才會中止，檔案也才會由active轉回bag
