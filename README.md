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
    
我们以能天使的数据为例子，进一步分析建模方法。我们汇总能天使全等级数据，隐藏了精英化导致的相同数据的等级，得到图1和图2。然后我们把法术抗性这一属性曲线去掉（因为全等级均为0），然后每一个阶段都用趋势线逼近，得到如下的图3、4、5。其余干员的数据也可以使用类似的方法进行分析。  
    
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E6%9B%B2%E7%BA%BF.png" >图1 能天使属性成长曲线</center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E5%B1%9E%E6%80%A7%E6%88%90%E9%95%BF%E9%87%8F%E5%88%86%E5%B8%83.png" >图2 能天使属性成长量分布 </center></td>
    </tr>
</table>       
   
<table>
    <tr>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B10%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%20%E8%B6%8B%E5%8A%BF%E7%BA%BF.png" >图3 能天使精英0 等级-属性 趋势线 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B11%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%20%E8%B6%8B%E5%8A%BF%E7%BA%BF.png" >图4 能天使精英1 等级-属性 趋势线 </center></td>
        <td ><center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E7%B2%BE%E8%8B%B12%20%E7%AD%89%E7%BA%A7-%E5%B1%9E%E6%80%A7%20%E8%B6%8B%E5%8A%BF%E7%BA%BF.png" >图5 能天使精英2 等级-属性 趋势线 </center></td>
    </tr>
</table>    
    
* 可以从多个等级成长量的图以及能天使的数据的趋势线发现，属性的增长曲线**等价于一个线性函数**，或者说，是一个**多段组合起来的分段线性函数**，增长速度等价于线性函数。这样设计的目的是防止数据增长过快，数值不会出现膨胀现象。如果使用指数函数类型的模型（多见于一些高数值成长类型的游戏），会出现数值膨胀现象。而明日方舟作为一款中低数值成长的游戏，**使用线性模型可以有效防止数值膨胀**，减少高低级玩家数值的差异。 
 
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

我们根据4个属性来分析职业定位：（以下的大于和约等于为总体情况下,主要考虑曲线后期）
* 生命上限：星熊>>银灰>推进之王>能天使≈闪灵≈艾雅法拉；总体上：地面干员>高台干员
* 攻击：银灰>艾雅法拉>能天使≈闪灵≈推进之王>星熊
* 防御：星熊>>银灰≈推进之王>能天使≈闪灵≈艾雅法拉
* 法术抗性：艾雅法拉>银灰>能天使=闪灵=推进之王=星熊=0
    
一般情况下，先锋、重装、近卫为**地面干员**，是要面接敌人的；狙击、术师、医疗为**高台干员**，主要负责输出。术师、部分近卫和部分重装拥有法术抗性，其余职业的一般情况下法术抗性均为0。根据数据可以发现游戏的职业定位：  
* **重装**：从数据上可以看出，星熊在生命上限和防御都远高于其余职业，是一个高生命与防御力的职业，但是相对的攻击力处于最低，职业定位为防御为主。
* **近卫**：银灰在生命上限和防御比星熊低，但比其余职业的高，攻击力比高台输出略高一些，职业定位为具有良好的站场输出能力。
* **先锋**：推进之王生命上限和防御比近卫略低一些，攻击力与一般狙击接近，且拥有回复部署费用，职业定位为开场回复部署费用和一定的前期站场能力。   
* **术师**：艾雅法拉具有一定法抗，进行法术攻击，生命上限和防御最低，输出比较高，职业定位为自身脆弱但有高法术输出。
* **狙击**：能天使与艾雅法拉在生命上限和防御数值上接近，进行物理攻击，职业定位为自身脆弱但有高物理输出。
* **医疗**：闪灵负责治疗其余干员，生命上限和防御较低，攻击力（即治疗量）只比重装高一些，职业定位为自身脆弱，回复其他干员生命。  
    
## 4 攻防公式

### 4.1 物理伤害公式推导与分析
明日方舟的攻防公式设计的基本框架为“伤害 = 自身攻击力 - 目标防御力”，即为常说的**减法公式**。但是考虑到自身攻击力<目标防御力的情况，会出现造成负数伤害，与逻辑相悖。使用max函数限制，使得造成的伤害保持大于0，或者大于一个固定的值，即常说的**保底伤害**。考虑到干员的各种技能中有攻击力倍率、百分比无视防御、固定值无视防御的属性，在多个干员多个技能同时造成伤害，故攻防公式应该含有上面这些属性。网上已有不少关于攻防公式的探索和研究，见[从源头解析单位数值运算方式与运算公式](https://nga.178.com/read.php?tid=21829497&rand=120)，自己也做过一些伤害测试。经过计算和数据验证，物理攻击的伤害公式为

$$ 基本物理伤害 = Max( 0.05 * 攻击力 * 攻击力倍率，攻击力 * 攻击力倍率-(1-百分比无视防御) * Max(0,(目标防御力-固定值无视防御))) $$

以精英2的能天使（0信赖1潜能）为例子，使用专精3级的3技能（即有1.1攻击力倍率），经计算，在多场景例子下的对各护甲敌人造成的伤害表如下。

| 敌人防御力 | 1级基础伤害 | 30级基础伤害 | 60级基础伤害 | 90级基础伤害 | 90级基础伤害+35%无视防御 | 90级基础伤害+210固定无视防御值 | 90级基础伤害+35%无视防御+210固定无视防御值 |
|-------|--------|---------|---------|---------|-----------------|--------------------|----------------------------|
| 0甲    | 480.7  | 518.1   | 555.5   | 594     | 594             | 594                | 594                        |
| 100甲  | 380.7  | 418.1   | 455.5   | 494     | 529             | 594                | 594                        |
| 200甲  | 280.7  | 318.1   | 355.5   | 394     | 464             | 594                | 594                        |
| 300甲  | 180.7  | 218.1   | 255.5   | 294     | 399             | 504                | 535.5                      |
| 400甲  | 80.7   | 118.1   | 155.5   | 194     | 334             | 404                | 470.5                      |
| 500甲  | 24.035 | 25.905  | 55.5    | 94      | 269             | 304                | 405.5                      |
| 750甲  | 24.035 | 25.905  | 27.775  | 29.7    | 106.5           | 54                 | 243                        |
| 800甲  | 24.035 | 25.905  | 27.775  | 29.7    | 74              | 29.7               | 210.5                      |
| 1200甲 | 24.035 | 25.905  | 27.775  | 29.7    | 29.7            | 29.7               | 29.7                       |
| 1500甲 | 24.035 | 25.905  | 27.775  | 29.7    | 29.7            | 29.7               | 29.7                       |

<center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%83%BD%E5%A4%A9%E4%BD%BF%E4%BC%A4%E5%AE%B3.png" width = "700px")</center>

* 这种攻防公式核心思路与减法公式相同，均为“伤害=攻击力-防御”，但有效的防止了减法公式所带来与逻辑冲突的负值。值得一提的是，公式中表示保底伤害使用的倍率为0.05，这是一个自定超参数。从图中可以看出能天使面对低甲敌人时能保持高伤害，但是面对500甲左右的敌人时，伤害曲线会急速下降，一般把这种情况称为**未破甲**，面对1500甲的敌人即使有无视防御加成依然无法破甲。能天使的攻击力，敌人的防御值，以及各种buff与debuff共同决定了**破甲线**。

### 4.2 法术伤害公式推导与分析
法术伤害公式与物理伤害公式设计思路相同，但是法术伤害无视敌人护甲，限制法术伤害的是敌人的另一属性：法术抵抗。依然是用max函数限制，防止出现干员无视防御数值>敌人法术抗性所带来的负值，以及百分比无视法抗过高导致的负值。法术攻击的伤害公式为

$$ 基本法术伤害=Max(0.05 * 攻击力 * 攻击力倍率，0.01 * 攻击力 * 攻击力倍率 * Max(0,100-(1-百分比无视法抗) * A) $$
    
$$ A = Max(0,目标法术抗性-固定值无视法抗) $$

下面以精英2的艾雅法拉（0信赖1潜能）为例子，在不同的法术穿透（即无视法抗）的情况下，面对不同法术抗性的敌人时的2技能专精3(3.7攻击力倍率)的伤害。
    
| 法抗 | 1级基础伤害  | 30级基础伤害 | 60级基础伤害 | 90级基础伤害 | 90级基础伤害+25%无视法抗 | 90级基础伤害+10固定无视法抗 | 90级基础伤害+25%无视法抗+10固定无视法抗 | 90级基础伤害+25%无视法抗+40固定无视法抗 |
|----|---------|---------|---------|---------|-----------------|------------------|--------------------------|--------------------------|
| 0  | 1979.5  | 2112.7  | 2249.6  | 2386.5  | 2386.5          | 2386.5           | 2386.5                   | 2386.5                   |
| 10 | 1781.55 | 1901.43 | 2024.64 | 2147.85 | 2207.5125       | 2386.5           | 2386.5                   | 2386.5                   |
| 20 | 1583.6  | 1690.16 | 1799.68 | 1909.2  | 2028.525        | 2147.85          | 2207.5125                | 2386.5                   |
| 30 | 1385.65 | 1478.89 | 1574.72 | 1670.55 | 1849.5375       | 1909.2           | 2028.525                 | 2386.5                   |
| 40 | 1187.7  | 1267.62 | 1349.76 | 1431.9  | 1670.55         | 1670.55          | 1849.5375                | 2386.5                   |
| 50 | 989.75  | 1056.35 | 1124.8  | 1193.25 | 1491.5625       | 1431.9           | 1670.55                  | 2207.5125                |
| 60 | 791.8   | 845.08  | 899.84  | 954.6   | 1312.575        | 1193.25          | 1491.5625                | 2028.525                 |
| 70 | 593.85  | 633.81  | 674.88  | 715.95  | 1133.5875       | 954.6            | 1312.575                 | 1849.5375                |
| 80 | 395.9   | 422.54  | 449.92  | 477.3   | 954.6           | 715.95           | 1133.5875                | 1670.55                  |
| 90 | 197.95  | 211.27  | 224.96  | 238.65  | 775.6125        | 477.3            | 954.6                    | 1491.5625                |

<center><img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E8%89%BE%E9%9B%85%E6%B3%95%E6%8B%89%E4%BC%A4%E5%AE%B3.png" width = "700px")</center>   
    
* 从伤害公式可以看到，固定无视法抗与百分比无视法抗对于伤害的影响一般来说都是线性的。法术伤害与物理伤害不同，固定无视法抗与百分比无视法抗来源也比物理减防来源要少。对于法术伤害与法抗的设计为线性函数也为合理设计。
    
## 5 设计一套RPG人物数值
假设游戏中有6种属性：力量、体质、敏捷、精神、幸运、智力，有5种职业：武将、弓手、刺客、道士、术士。请设计各职业属性的初始值，并简单说明理由。
