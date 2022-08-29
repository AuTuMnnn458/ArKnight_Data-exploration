# ArKnight_Data-exploration
本文主要通过拆解游戏明日方舟中部分系统的数值，反向推导与构建游戏在人物数值上所使用的数学模型，并研究、学习和讨论该游戏在人物数值上的设计思路。在本文的最后一部分，有作者使用相似模型构造出一个新的RPG游戏的人物数值。本文数据大量参考自[PRTS-玩家自由构筑的明日方舟中文Wiki](https://prts.wiki/w/%E9%A6%96%E9%A1%B5)。本文中有大量excel表格数据，**一般只展示前五行与最后五行数据**，全体数据的excel文件见。
## 1 基础介绍
* 明日方舟：《明日方舟》是由鹰角网络自主开发运营的一款策略向即时战略塔防游戏，于2019年5月1日公测。
* 博士：游戏中玩家称为“博士”。博士等级由声望决定。声望，即博士经验值，在作战结束时发放，积累一定数量后可以提升博士等级。一般地，作战通关时（剿灭作战则为行动结束时）按10倍实际消耗理智的比例（1:10）给予声望。如果是三星或四星通关，声望获取提升至1.2倍（即变为1:12）。
* 干员：在游戏中，供玩家差遣的可操控角色称为“干员”，他们不仅性格各异，与敌人作战时也有各自的专长。游戏初期，这些角色被分为先锋、近卫、重装、狙击、特种、医疗、术师、辅助共计八个职业，现游戏版本中每个职业亦被细分为多个分支职业。

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
<center>
<figure>
<img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%AD%89%E7%BA%A7-%E5%8D%87%E7%BA%A7%E6%89%80%E9%9C%80%E5%A3%B0%E6%9C%9B.png" />
·
·
·
<img src="https://github.com/AuTuMnnn458/ArKnight_Data-exploration/blob/main/pictures/%E7%AD%89%E7%BA%A7-%E7%B4%AF%E8%AE%A1%E5%A3%B0%E6%9C%9B.png" />
</figure>
</center>


### 2.2 干员升级经验

## 3 干员属性
### 3.1 多个职业干员的属性数据
### 3.2 干员属性与职业定位分析

## 4 攻防公式
### 4.1 物理伤害公式推导与分析
### 4.2 法术伤害公式推导与分析

## 5 设计一套RPG人物数值
假设游戏中有6种属性：力量、体质、敏捷、精神、幸运、智力，有5种职业：武将、弓手、刺客、道士、术士。请设计各职业属性的初始值，并简单说明理由。
