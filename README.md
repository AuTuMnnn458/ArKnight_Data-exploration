# ArKnight_Data-exploration
本文主要通过拆解游戏明日方舟中部分系统的数值，反向推导与构建游戏在人物数值上所使用的数学模型，并研究、学习和讨论该游戏在人物数值上的设计思路。在本文的最后一部分，有作者使用相似模型构造出一个新的RPG游戏的人物数值。本文数据大量参考自[PRTS-玩家自由构筑的明日方舟中文Wiki](https://prts.wiki/w/%E9%A6%96%E9%A1%B5)。本文中有大量excel表格数据，**一般只展示前五行与最后五行数据**，全体数据的excel文件见。
## 1 基础介绍
* 明日方舟：《明日方舟》是由鹰角网络自主开发运营的一款策略向即时战略塔防游戏，于2019年5月1日公测。
* 博士：游戏中玩家称为“博士”。博士等级由声望决定。声望，即博士经验值，在作战结束时发放，积累一定数量后可以提升博士等级。一般地，作战通关时（剿灭作战则为行动结束时）按10倍实际消耗理智的比例（1:10）给予声望。如果是三星或四星通关，声望获取提升至1.2倍（即变为1:12）。
* 理智：游戏中玩家的体力。玩家可以使用体力进行游戏（特定模式不消耗理智）。可通过自然时间回复、使用源石回复、使用理智试剂或其他活动道具回复。
* 源石：游戏中的一种货币。玩家可使用源石购买商店中的物品或者兑换理智。可通过采购中心购买、首次通关获取、活动获取、官方邮件赠送等途径获得。
* 龙门币：游戏中的一种货币。龙门币用途广泛，为游戏内的通用货币。
* 干员：在游戏中，供玩家差遣的可操控角色称为“干员”，他们不仅性格各异，与敌人作战时也有各自的专长。游戏初期，这些角色被分为先锋、近卫、重装、狙击、特种、医疗、术师、辅助共计八个职业，现游戏版本中每个职业亦被细分为多个分支职业。
* 精英化：干员升级到一定等级后，需要提供一些材料与龙门币进行阶段提升，进一步开放属性和等级上限，这一过程称为“精英化”。根据干员的星级不同，有不同的精英化上限，如6星干员可以进行两次精英化达到精英化2。精英化后，等级会回到1级，但原有属性保留。

## 2 升级经验分布与曲线拟合
### 2.1 博士升级经验
不同的博士等级对应的理智上限及升级升级所需声望见下表。

| 当前等级 | 理智上限 | 升级所需声望 | 累计声望 | 升级所需声望差 |
|------|------|--------|------|---------|
| 1    | 82   | 500    | 0    | 300     |
| 2    | 84   | 800    | 500  | 440     |
| 3    | 86   | 1240   | 1300 | 80      |
| 4    | 88   | 1320   | 2540 | 80      |
| 5    | 90   | 1400   | 3860 | 80      |
| ...    | ...   | ...   | ... | ... |
| 116 | 134 | 88000  | 2566990 | 3000 |
| 117 | 135 | 91000  | 2654990 | 3000 |
| 118 | 135 | 94000  | 2745990 | 3000 |
| 119 | 135 | 97000  | 2839990 | 3000 |
| 120 | 135 | 100000 | 2936990 |

Excel中初步处理，并画出一些图的趋势线：

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%AD%89%E7%BA%A7-%E5%8D%87%E7%BA%A7%E6%89%80%E9%9C%80%E5%A3%B0%E6%9C%9B.png" >图1 等级-升级所需声望 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%AD%89%E7%BA%A7-%E7%90%86%E6%99%BA%E4%B8%8A%E9%99%90.png" >图2 等级-理智上限 </center></td>
    </tr>
</table>

<table>
<tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%AD%89%E7%BA%A7-%E7%B4%AF%E8%AE%A1%E5%A3%B0%E6%9C%9B.png" >图3 等级-累计声望 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%AD%89%E7%BA%A7_%E5%8D%87%E7%BA%A7%E6%89%80%E9%9C%80%E5%A3%B0%E6%9C%9B%E5%B7%AE.png" >图4 等级-升级所需声望差 </center></td>
    </tr>
</table>

* 根据图4，升级所需声望差在不同的等级区间大致取一个不同的递增的常数，可以推断出对于升级所需声望所建立的模型是**分段线性函数**。
* 用趋势线逼近图1与图3，曲线大致与三次函数接近。在上一点我们已经提出它所建立的模型为分段线性函数，这里表明增长的速度大致与三次函数相当。
* 图2的趋势与对数函数相当，故用对数函数进行拟合。这种曲线表明前期博士等级升级时，理智上限成长较快，后期较慢，是非常合理的数学模型。
* 图4表明前2级升级所需声望较多，有一点凸起，而后期是平稳上升。由于前期基数较小，所以前期升级所需声望差设置较高亦为合理调整。推断是为了调节前期玩家等级升级过快，导致理智不断溢出的现象做出的平衡调整。

### 2.2 干员升级经验
下表为干员升级所需要的经验和龙门币。这里忽略精英化时所需要的材料和龙门币。

不同星级的干员的不同精英化阶段的等级上限不同（如6星干员最高，可达精英化2的90级，5星干员其次，最高为精英化2的80级），但**在相同等级时无论星级如何，升级所需经验和龙门币一致**。故这里只讨论6星干员的情况。

| 目标等级 | 经验    | 龙门币   | 经验差  | 龙门币差 |
|------|-------|-------|------|------|
| 精英0  |       |       |      |      |
| 2    | 100   | 30    | 17   | 6    |
| 3    | 117   | 36    | 17   | 7    |
| ...    | ...   | ...   | ... | ... |
| 48   | 836   | 1413  | 16   | 61   |
| 49   | 852   | 1474  | 36   | 98   |
| 50   | 888   | 1572  |      |      |
| 精英1  |       |       |      |      |
| 2    | 120   | 48    | 52   | 23   |
| 3    | 172   | 71    | 52   | 24   |
| ...    | ...   | ...   | ... | ... |
| 78   | 13430 | 16599 | 497  | 768  |
| 79   | 13927 | 17367 | 622  | 936  |
| 80   | 14549 | 18303 |      |      |
| 精英2  |       |       |      |      |
| 2    | 191   | 76    | 112  | 48   |
| 3    | 303   | 124   | 112  | 49   |
| ...    | ...   | ...   | ... | ... |
| 88   | 28641 | 34083 | 1256 | 1823 |
| 89   | 29897 | 35906 | 1246 | 1839 |
| 90   | 31143 | 37745 |

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E7%BB%8F%E9%AA%8C-%E7%BB%8F%E6%B5%8E.png" >图1 精英0 等级-经验/经济 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E7%BB%8F%E9%AA%8C%E5%B7%AE-%E7%BB%8F%E6%B5%8E%E5%B7%AE.png" >图2 精英0 等级-经验差/经济差 </center></td>
    </tr>
</table>

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E7%BB%8F%E9%AA%8C-%E7%BB%8F%E6%B5%8E.png" >图3 精英1 等级-经验/经济 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E7%BB%8F%E9%AA%8C%E5%B7%AE-%E7%BB%8F%E6%B5%8E%E5%B7%AE.png" >图4 精英1 等级-经验差/经济差 </center></td>
    </tr>
</table>

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E7%BB%8F%E9%AA%8C-%E7%BB%8F%E6%B5%8E.png" >图5 精英2 等级-经验/经济 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E7%BB%8F%E9%AA%8C%E5%B7%AE-%E7%BB%8F%E6%B5%8E%E5%B7%AE.png" >图6 精英2 等级-经验差/经济差 </center></td>
    </tr>
</table>

* 图1，图3，图5表明随着等级增长，所需要的经验和龙门币都是逐步增长的，而且增长速度比较快，曲线大致与2次或3次函数增长速度相同。
* 图2表明精英化0阶段，经验是比较平稳上升的，所以图1中的经验曲线大致为线性函数，而龙门币的增长则大致为2次函数。图4中除了有两个点有异常凸起外，基本上曲线是阶梯递增常函数。图6与图4类似，除了有一部分异常点外均为阶梯递增常函数。
* 关于图2、图4、图6中的异常凸起点，推断原因有2个：
  * (1)3星干员与精英化1的55级即达到等级上限。升级到最后一级时一般都需要比前面升级更多的资源（见图2、4、6的曲线末端）。故在精英1的55级有较大波动，其他波动点也有类似原因。
  * (2)而多次波动的原因是为了调整不同星级干员到达等级上限后引起的资源突增的平衡。
* 总体来说，升级所需经验/龙门币所使用的模型**大致也能看作分段线性模型**。


## 3 干员属性
### 3.1 多个职业干员的属性数据
### 3.2 干员属性与职业定位分析

## 4 攻防公式
### 4.1 物理伤害公式推导与分析
### 4.2 法术伤害公式推导与分析

## 5 设计一套RPG人物数值
假设游戏中有6种属性：力量、体质、敏捷、精神、幸运、智力，有5种职业：武将、弓手、刺客、道士、术士。请设计各职业属性的初始值，并简单说明理由。
