# AGV调度模型设计

[TOC]



## 1.模型最终需求

所有任务完成的总时间；每个小车从开始任务到它最后一个任务的时间；每个小车完成多少个任务；每个小车充电的时间；每个小车等待充电的时间；每个小车行走的距离

## 2.模型

### 描述

**任务：**  码头的工作区域内，AGV小车负责完成集装箱从各自起点岸桥送到指定终点场桥。

**对应关系：** 一个集装箱任务对应一个起点岸桥和一个终点场桥。

**任务选取规则：** 小车在完成一个任务达到指定场桥时要选择下一个任务，任务选择的原则就是小车当前场桥位置到剩余任务集装箱起点岸桥的行程时间和各个任务的紧急程度两者综合考虑进行加权，加权比例为**0.4** 和**0.6** ，选择加权值最小的任务。

**执行任务条件：** 选择任务过后就判断该小车的电量能否完成这个任务到达终点场桥后还剩不低于10%的电量（10%为小车返回充电站的安全值），如果电量不够就放弃任务去充电，够就执行这个任务。

**充电规则：** 小车到达充电站就从当前剩余电量开始充电，充电站的限制就是最低要充到60%，充到60%后要有其他小车等待充电就在该时断开充电，没有小车等待就一直充到满电。

**任务属性：** 

1. id（唯一标示）
2. 完成时间
3. 紧急程度
4. 任务总数
5. 已完成任务数

**AGV属性：**

1. id（唯一标示）
2. 满电空载行程
3. 满电负载行程
4. 当前电量
5. 空载速度
6. 负载速度
7. 空载耗电速度
8. 负载耗电速度
9. 充电速度
10. 允许工作的最低电量
11. 允许充电断开的最低电量
12. 完成任务数
13. 工作时间
14. 充电时间
15. 等待充电时间
16. 行驶距离

**场桥属性：**

1. id（唯一标示）
2. 到岸桥X的距离
3. 到充电站的距离



**岸桥桥属性：**

1. id（唯一标示）
2. 到场桥X的距离
3. 到场桥X的任务
4. 到充电站的距离







### 限制条件

1. t时刻充电小车的数量不大于充电桩的数量
2. 每个任务只能被完成一次且都要被完成
3. 小车在充电时间及在执行任务时不能被分配任务
4. 当小车的电量满足能够完成某任务并且能返回充电站时，小车前往执行任务，否则放弃任务去充电。
5. 若有小车需要充电，此时充电桩已满且在充电的小车电量达到电量允许工作值（60%）后可断开充电小车电源进入工作，需充电的小车进行充电。

## 3.参数

AGV满电行程：200km（空载），150km（重载）

AGV港内运行速度：25km/h（空载），20km/h（重载）

AGV耗电速度：12.5/h（空载）即0.0035/s，13.3/h（重载）即0.0037/s

AGV充满电速度：0.67/h （40分钟充满）

AGV允许断开进行工作的电量值PU：60

AGV的工作状态时最低电量限制PL：10

 

集装箱卸箱任务个数：1000   两个岸桥的任务数各为500，各个任务的紧急程度分为1,2,3等级，1为最紧急，2为较紧急，3为较不紧急（用编码随机给1000个任务赋值1,2,3）

起点岸桥个数：2

终点场桥个数：6

充电站个数：1

AGV个数：8

**AGV的初始位置设定在场桥1**



岸桥与场桥之间的距离表：（m）**从岸桥到场桥重载，从场桥到岸桥空载**



|      | 场桥1  | 场桥2  | 场桥3  | 场桥4  | 场桥5  | 场桥6  |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 岸桥1  | 700  | 630  | 630  | 910  | 1120 | 1330 |
| 岸桥2  | 840  | 630  | 630  | 840  | 980  | 1190 |



岸桥与场桥之间的任务数量表：（个）

|      | 场桥1  | 场桥2  | 场桥3  | 场桥4  | 场桥5  | 场桥6  | 合计   |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 岸桥1  | 88   | 70   | 150  | 90   | 50   | 52   | 500  |
| 岸桥2  | 105  | 96   | 80   | 100  | 74   | 45   | 500  |



给定8个AGV的初始电量：（60≤电量≤100）

| AGV   | 1    | 2    | 3    | 4    | 5    | 6    | 7    | 8    |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 初始电量% | 68   | 71   | 66   | 83   | 62   | 78   | 88   | 91   |



场桥到充电站距离（m）**场桥与充电站之间空载**

|      | 场桥1  | 场桥2  | 场桥3  | 场桥4  | 场桥5  | 场桥6  |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| 充电站  | 100  | 300  | 450  | 680  | 850  | 980  |



岸桥到充电站距离（m）**充电站到岸桥之间空载**

|      | 岸桥1  | 岸桥2  |
| ---- | ---- | ---- |
| 充电站  | 800  | 950  |





