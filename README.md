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
不同的博士等级对应的理智上限及升级所需声望见下表。

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
* 图4表明前2级升级所需声望较多，有一点凸起，而后期是平稳上升。前期基数较小，所以前2级升级所需声望差设置较高亦为合理调整。推断是为了调节前期玩家等级升级过快，导致理智不断溢出的现象做出的平衡调整。

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
* 图2表明精英化0阶段，经验是比较平稳上升的，所以图1中的经验曲线大致为线性函数，而龙门币的增长则大致为2次函数。图4中除了有两个点有异常凸起外，基本上符合阶梯递增常函数。图6与图4类似，除了有一部分异常点外均符合阶梯递增常函数。
* 关于图2、图4、图6中的异常凸起点，推断原因有2个：
  * (1)3星干员与精英化1的55级即达到等级上限。升级到最后一级时一般都需要比前面升级更多的资源（见图2、4、6的曲线末端）。故在精英1的55级有较大波动，其他波动点也有类似原因。
  * (2)而多次波动的原因是为了调整不同星级干员到达等级上限后引起的资源突增的平衡。
* 总体来说，升级所需经验/龙门币所使用的模型大致也能看作**分段线性模型**。


## 3 干员属性
### 3.1 干员属性介绍
<center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E5%B1%9E%E6%80%A7%E9%9D%A2%E6%9D%BF.png" width = "300px")</center>

一个干员拥有最基础的八项属性值：生命上限、攻击、防御、法术抗性、再部署、部署费用、阻挡数、攻击速度。通过这些属性值可以了解这个干员的基础性能。本部分只讨论生命上限、攻击、防御、法术抗性的数据，并只考虑信赖为0、潜能为1的情况，研究这4项数据与职业定位的相关性。

游戏有8大职业：先锋、近卫、狙击、术师、医疗、重装、特种、辅助。游戏内的对于职业的定位是：

* **先锋（VANGUARD）**：对部署有更佳效果的职业。大多数能够提供增加部署费用的方法。放置于近战位（除战术家外），费用和属性整体较低，主要造成物理伤害。
* **狙击（SNIPER）**：擅长远程物理攻击的输出职业。费用差别很大，防御及血量通常较低，放置于远程位，主要造成物理伤害。
* **近卫（GUARD）**：近战攻击为主的输出职业。放置于近战位，攻击较高，防御中等。
* **术师（CASTER）**：擅长远程法术攻击的输出职业。放置于远程位，费用高，攻击较高，攻速较慢，防御及血量较低，法抗高，主要造成法术伤害。
* **重装（DEFENDER）**：高生命与防御力的职业。放置于近战位，攻击较低，防御及血量较高，攻击范围整体上较小，阻挡数较大，主要造成物理伤害。   	
* **医疗（MEDIC）**：提供治疗，回复生命或元素损伤的职业。放置于远程位，血量及防御较低。通常不具备攻击能力，但可以为友方干员恢复生命或元素损伤。
* **特种（SPECIALIST）**：攻击手段与部署方式多样化的职业。部分干员可同时部署于高台或者地面。多以物理伤害为主，功能与属性相差很大。  	
* **辅助（SUPPORTER）**：提供多样增益减益效果的职业。放置于远程位（除工匠外），攻击中等，防御及血量较低，多以法术伤害为主。 
    
### 3.2 多个职业干员的属性数据
我们将结合部分干员的数据，分析游戏是怎么通过数值设计来有效定位不同职业的。由于特种和辅助的多个干员间差异巨大，很难找到有代表性的人物，故本文不讨论特种和辅助职业的干员。我们选择推进之王（先锋）、能天使（狙击）、银灰（近卫）、艾雅法拉（术师）、星熊（重装）、闪灵（医疗）的数据进行讨论。值得一提的是，选择的这6位干员均为各自职业中游戏推出的**第一位**干员，具有一定的代表性。通过研究他们的数据，可以研究出游戏初期干员数值的一些设计思路。

我们使用的办法是收集每一级的干员属性数据，然后作差（即为表格内的成长量），并作图分析。下面只展示出能天使的部分表格数据，其余干员的数据请参看。

| 等级  | 生命上限 | 攻击  | 防御  | 法术抗性 |   | 生命  | 攻击 | 防御 | 法术抗性 |
|-----|------|-----|-----|------|---|-----|----|----|------|
| 精英0 | 信赖0  | 潜能1 |     |      |   | 成长量 |    |    |      |
| 1   | 711  | 183 | 57  | 0    |   | 6   | 2  | 1  | 0    |
| 2   | 717  | 185 | 58  | 0    |   | 6   | 3  | 1  | 0    |
| 3   | 723  | 188 | 59  | 0    |   | 7   | 2  | 0  | 0    |
| ...   | ...  | ... | ...  | ...    |  ... | ...   | ... | ...  | ...  |
| 48  | 1004 | 300 | 93  | 0    |   | 6   | 3  | 1  | 0    |
| 49  | 1010 | 303 | 94  | 0    |   | 6   | 2  | 1  | 0    |
| 50  | 1016 | 305 | 95  | 0    |   |     |    |    |      |
| 精英1 |    |   |     |      |   |   |    |    |      |
| 1   | 1016 | 305 | 95  | 0    |   | 4   | 2  | 1  | 0    |
| 2   | 1020 | 307 | 96  | 0    |   | 4   | 1  | 0  | 0    |
| 3   | 1024 | 308 | 96  | 0    |   | 4   | 2  | 1  | 0    |
| ...   | ...  | ... | ...  | ...    |  ... | ...   | ... | ...  | ...  |
| 78  | 1330 | 434 | 135 | 0    |   | 4   | 1  | 0  | 0    |
| 79  | 1334 | 435 | 135 | 0    |   | 4   | 2  | 1  | 0    |
| 80  | 1338 | 437 | 136 | 0    |   |     |    |    |      |
| 精英2 |    |   |     |      |   |   |    |    |      |
| 1   | 1338 | 437 | 136 | 0    |   | 4   | 1  | 0  | 0    |
| 2   | 1342 | 438 | 136 | 0    |   | 4   | 1  | 1  | 0    |
| 3   | 1346 | 439 | 137 | 0    |   | 3   | 1  | 0  | 0    |
| ...   | ...  | ... | ...  | ...    |  ... | ...   | ... | ...  | ...  |
| 88  | 1665 | 538 | 160 | 0    |   | 4   | 1  | 1  | 0    |
| 89  | 1669 | 539 | 161 | 0    |   | 4   | 1  | 0  | 0    |
| 90  | 1673 | 540 | 161 | 0    |
    
* 能天使：
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图1 能天使精英0 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图2 能天使精英1 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图3 能天使精英2 等级-属性</center></td>
    </tr>
</table>

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图4 能天使精英0 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图5 能天使精英1 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图6 能天使精英2 等级-属性成长量 </center></td>
    </tr>
</table>

* 推进之王：
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%8E%A8%E8%BF%9B%E4%B9%8B%E7%8E%8B%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图1 推进之王精英0 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%8E%A8%E8%BF%9B%E4%B9%8B%E7%8E%8B%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图2 推进之王精英1 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%8E%A8%E8%BF%9B%E4%B9%8B%E7%8E%8B%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图3 推进之王精英2 等级-属性</center></td>
    </tr>
</table>

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%8E%A8%E8%BF%9B%E4%B9%8B%E7%8E%8B%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图4 推进之王精英0 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%8E%A8%E8%BF%9B%E4%B9%8B%E7%8E%8B%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图5 推进之王精英1 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%8E%A8%E8%BF%9B%E4%B9%8B%E7%8E%8B%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图6 推进之王精英2 等级-属性成长量 </center></td>
    </tr>
</table>

* 星熊：
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%98%9F%E7%86%8A%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图1 星熊精英0 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%98%9F%E7%86%8A%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图2 星熊精英1 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%98%9F%E7%86%8A%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图3 星熊精英2 等级-属性</center></td>
    </tr>
</table>

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%98%9F%E7%86%8A%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图4 星熊精英0 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%98%9F%E7%86%8A%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图5 星熊精英1 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E6%98%9F%E7%86%8A%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图6 星熊精英2 等级-属性成长量 </center></td>
    </tr>
</table>    
    
* 银灰：
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%93%B6%E7%81%B0%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图1 银灰精英0 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%93%B6%E7%81%B0%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图2 银灰精英1 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%93%B6%E7%81%B0%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图3 银灰精英2 等级-属性</center></td>
    </tr>
</table>

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%93%B6%E7%81%B0%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图4 银灰精英0 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%93%B6%E7%81%B0%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图5 银灰精英1 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%93%B6%E7%81%B0%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图6 银灰精英2 等级-属性成长量 </center></td>
    </tr>
</table>   
    
* 艾雅法拉：
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%89%BE%E9%9B%85%E6%B3%95%E6%8B%89%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图1 艾雅法拉精英0 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%89%BE%E9%9B%85%E6%B3%95%E6%8B%89%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图2 艾雅法拉精英1 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%89%BE%E9%9B%85%E6%B3%95%E6%8B%89%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图3 艾雅法拉精英2 等级-属性</center></td>
    </tr>
</table>

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%89%BE%E9%9B%85%E6%B3%95%E6%8B%89%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图4 艾雅法拉精英0 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%89%BE%E9%9B%85%E6%B3%95%E6%8B%89%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图5 艾雅法拉精英1 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%89%BE%E9%9B%85%E6%B3%95%E6%8B%89%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图6 艾雅法拉精英2 等级-属性成长量 </center></td>
    </tr>
</table>   

* 闪灵：
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%97%AA%E7%81%B5%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图1 闪灵精英0 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%97%AA%E7%81%B5%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图2 闪灵精英1 等级-属性 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%97%AA%E7%81%B5%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7.png" >图3 闪灵精英2 等级-属性</center></td>
    </tr>
</table>

<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%97%AA%E7%81%B5%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图4 闪灵精英0 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%97%AA%E7%81%B5%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图5 闪灵精英1 等级-属性成长量 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E9%97%AA%E7%81%B5%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F.png" >图6 闪灵精英2 等级-属性成长量 </center></td>
    </tr>
</table>   

* 可以看到精英化的过程是不增加生命上限、攻击和防御数值的，如精英0的50级与精英1的1级的该三项数值相同。但是法术抗性的成长只会出现在这个点，如艾雅法拉精英0的50级法抗为10，而精英1的50级法抗为15。 
* 可以从多个等级成长量的图发现，属性的增长曲线**等价于一个线性函数**，或者说，是一个**多段组合起来的分段线性函数**，增长速度等价于线性函数。这样设计的目的是防止数据增长过快，数值不会出现膨胀现象。如果使用指数函数类型的模型（多见于一些高数值成长类型的游戏），会出现数值膨胀现象。而明日方舟作为一款中低数值成长的游戏，**使用线性模型可以有效防止数值膨胀**，减少高低级玩家数值的差异。
    
### 3.3 干员属性与职业定位分析
综合6位干员的4项属性，我们得到如下的图：
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/6%E4%BA%BA%E7%AD%89%E7%BA%A7-%E7%94%9F%E5%91%BD%E4%B8%8A%E9%99%90.png" >图1 6人等级-生命上限 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/6%E4%BA%BA%E7%AD%89%E7%BA%A7-%E6%94%BB%E5%87%BB.png" >图2 6人等级-攻击 </center></td>
    </tr>
</table> 
    
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/6%E4%BA%BA%E7%AD%89%E7%BA%A7-%E9%98%B2%E5%BE%A1.png" >图3 6人等级-防御 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/6%E4%BA%BA%E7%AD%89%E7%BA%A7-%E6%B3%95%E6%9C%AF%E6%8A%97%E6%80%A7.png" >图4 6人等级-法术抗性 </center></td>
    </tr>
</table> 

我们根据4个属性来分析职业定位：
* 生命上限：星熊>银灰>推进之王>能天使≈闪灵≈艾雅法拉。总体上：地面干员>高台干员
## 4 攻防公式

### 4.1 物理伤害公式推导与分析
### 4.2 法术伤害公式推导与分析

## 5 设计一套RPG人物数值
假设游戏中有6种属性：力量、体质、敏捷、精神、幸运、智力，有5种职业：武将、弓手、刺客、道士、术士。请设计各职业属性的初始值，并简单说明理由。
