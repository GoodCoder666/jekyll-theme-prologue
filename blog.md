---
layout: blog
title: 博客
icon: fa-pencil-alt
order: 2
---

---
layout: blog
title: 【python爬虫】从腾讯API爬取美国疫情数据+制表
---
@[TOC]

最近（文章撰写时间为2020/6/1 18:40）疫情在中国情况好转，却在美国暴虐。
本篇文章将爬取腾讯提供的美国[疫情数据](https://news.qq.com/zt2020/page/feiyan.htm#/global)并制表。
# 1. 爬取数据
## 调用API接口
接口：https://api.inews.qq.com/newsqa/v1/automation/modules/list?modules=FAutoCountryMerge
观察得到的数据：
```json
{
	...,
	"data": {
		"FAutoCountryMerge": {
			...,
			"美国": {
				"showDash":false,
				"list": [
					{"date":"01.28","confirm_add":0,"confirm":5,"heal":0,"dead":0},
					...,
					{"date":"05.29","confirm_add":25069,"confirm":1768461,"heal":510713,"dead":103330},
					{"date":"05.30","confirm_add":23290,"confirm":1793530,"heal":519569,"dead":104542},
					{"date":"05.31","confirm_add":20350,"confirm":1816820,"heal":535238,"dead":105557},
					{"date":"06.01","confirm_add":20350,"confirm":1837170,"heal":599867,"dead":106195}
				]
			},
			...
		}
	}
}
```
由如上代码所示，对于一个国家，获取其疫情数据只需要使用：
```python
json['data']['FAutoCountryMerge']['<国名>']['list']
```
对于美国的数据，使用：
```python
json['data']['FAutoCountryMerge']['美国']['list']
```
### 代码
上面都是干货，下面才是真正的`code`：
```python
from requests import get

url = 'https://api.inews.qq.com/newsqa/v1/automation/modules/list?modules=FAutoCountryMerge'
data = get(url).json()['data']['FAutoCountryMerge']['美国']['list']
```
## 处理数据
在`python`中，其结果是一个`list`对象：
```python
[
	{"date":"01.28","confirm_add":0,"confirm":5,"heal":0,"dead":0},
	...,
	{"date":"05.29","confirm_add":25069,"confirm":1768461,"heal":510713,"dead":103330},
	{"date":"05.30","confirm_add":23290,"confirm":1793530,"heal":519569,"dead":104542},
	{"date":"05.31","confirm_add":20350,"confirm":1816820,"heal":535238,"dead":105557},
	{"date":"06.01","confirm_add":20350,"confirm":1837170,"heal":599867,"dead":106195}
]
```
该对象中存放美国每天的疫情数据，
`date`：从1月28日至今的日期；
`confirm_add`：该日新增确诊；
`confirm`：该日累计确诊；
`heal`：该日累计治愈；
`dead`：该日累计死亡。

### 筛选数据
数据的筛选很重要。
- `confirm_add`（该日新增确诊）明显没有用，去掉
- 应该增加一个`now_confirm`（该日现存确诊），这样能清楚地看到美国治疗中人数。
该值可以通过`confirm - heal - head`得到。

date：从1月28日至今的日期
~~confirm_add：该日新增确诊~~
confirm：该日累计确诊
heal：该日累计治愈
dead：该日累计死亡
**now_confirm: 该日现存确诊**
### 代码
由于最前面人数太少，数据会影响到最终绘图质量。
所以，我从第35个开始保存数据，当然如果您想使用所有数据，将`data[35:]`改为`data`即可。
```python
dates = []
confirms = []
now_confirms = []
heals = []
deads = []

for day_data in data[35:]:
    dates.append(day_data['date'])
    confirms.append(day_data['confirm'])
    heals.append(day_data['heal'])
    deads.append(day_data['dead'])
    now_confirms.append(confirms[-1] - heals[-1] - deads[-1])
```
# 2. 绘图
参考文章：https://www.cnblogs.com/lone5wolf/p/10870200.html
由于我在绘图方面还是个小白，所以直接贴出代码，敬请谅解。。。
```python
import matplotlib.pyplot as plt
from matplotlib.font_manager import FontProperties

# 绘制文本
plt.figure(figsize=(11.4, 7.7))

confirm_line, = plt.plot(dates, confirms, color='#8B0000')
now_confirm_line, = plt.plot(dates, now_confirms, color='red', linestyle=':')
heal_line, = plt.plot(dates, heals, color='green', linestyle='--')
dead_line, = plt.plot(dates, deads, color='black', linestyle='-.')

# 绘制图形
my_font = FontProperties(fname=r'fonts\msyh.ttc')
plt.legend(handles=[confirm_line, now_confirm_line, heal_line, dead_line], labels=['累计确诊', '现存确诊', '治愈', '死亡'], prop=my_font)
plt.xlabel('日期', fontproperties=my_font)
plt.ylabel('人数', fontproperties=my_font)
plt.title('美国2019-nCov疫情情况', fontproperties=my_font)
plt.gca().xaxis.set_major_locator(plt.MultipleLocator(7))

# 保存并显示统计图
plt.savefig('AmericaNCovData.png')
plt.show()
```
## 结果图片
![美国nCov](https://img-blog.csdnimg.cn/20200601211444772.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dyaXRlXzFtX2xpbmVz,size_16,color_FFFFFF,t_70#pic_center)
# 3. 完整代码
```python
# -*- coding: utf-8 -*-
from requests import get
import matplotlib.pyplot as plt
from matplotlib.font_manager import FontProperties

url = 'https://api.inews.qq.com/newsqa/v1/automation/modules/list?modules=FAutoCountryMerge'
data = get(url).json()['data']['FAutoCountryMerge']['美国']['list']

dates = []
confirms = []
now_confirms = []
heals = []
deads = []

for day_data in data[35:]:
    dates.append(day_data['date'])
    confirms.append(day_data['confirm'])
    heals.append(day_data['heal'])
    deads.append(day_data['dead'])
    now_confirms.append(confirms[-1] - heals[-1] - deads[-1])

# 绘制文本
plt.figure(figsize=(11.4, 7.7))

confirm_line, = plt.plot(dates, confirms, color='#8B0000')
now_confirm_line, = plt.plot(dates, now_confirms, color='red', linestyle=':')
heal_line, = plt.plot(dates, heals, color='green', linestyle='--')
dead_line, = plt.plot(dates, deads, color='black', linestyle='-.')

# 绘制图形
my_font = FontProperties(fname=r'fonts\msyh.ttc')
plt.legend(handles=[confirm_line, now_confirm_line, heal_line, dead_line], labels=['累计确诊', '现存确诊', '治愈', '死亡'], prop=my_font)
plt.xlabel('日期', fontproperties=my_font)
plt.ylabel('人数', fontproperties=my_font)
plt.title('美国2019-nCov疫情情况', fontproperties=my_font)
plt.gca().xaxis.set_major_locator(plt.MultipleLocator(7))

# 保存并显示统计图
plt.savefig('AmericaNCovData.png')
plt.show()
```
代码下载：[GitHub](https://github.com/GoodCoder666/python_crawl/blob/master/getNcovData.py)
