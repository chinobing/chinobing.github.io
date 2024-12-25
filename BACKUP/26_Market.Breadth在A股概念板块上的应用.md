---
title: Market Breadth在A股概念板块上的应用
categories:
- 研究

date: 2024-12-24

---

# [Market Breadth在A股概念板块上的应用](https://github.com/chinobing/chinobing.github.io/issues/26)

> [!NOTE]
> 昨天又遇到一个资本大鳄，基本每次只炒一只票，而且持股不超过2天。 这两年行情那么差居然可以实现每年翻倍，这简直是游资的手法。
> 按照他所说的，`只看股票的资金面： 概念、换手率、净特大单情况。`
> _Originally posted by @chinobing in https://github.com/chinobing/chinobing.github.io/issues/16#issuecomment-2556108473_

上次之后我一直比较关注这个游资在饭局上谈起他正在操作的一只股票，首先这股票属于`铜缆高速连接`的概念板块里面，而且19号到现在整个板块的换手率都搞得很。结果就是，这只股票起码涨了22%。  其实这只票涨多少不是我所关心的，而且他提供的方法感觉好像有点意思。 `炒热点`这种东西其实就是击鼓传花，节奏一定要快，见好就收。 要不然很容易变成接盘侠。

但如何判断自己是否最后接棒的人？我这几天一直在想，能不能通过计算概念板块的 `market breadth` 来判断呢？其次，在这个板块中再找换手率高的、市值低的盘子来操作，这样是不是也可以？

![image](https://github.com/user-attachments/assets/9f394429-a4a3-4181-91fb-9c5aee03ca41)

但使用 `market breadth`也有个问题，毕竟这跟原来的意义已经不大一样。
- 这个买卖阀值是啥决定的？
- 既然是追热点，不应该是到了`sell zone`继续追涨吗？
- `market breadth`设置的天数问题，上面是用了20天，但是否应该设置更短的时间？



# 回测（Backtesting.py）
由于概念板块的整体换手率数据是按日获取的，没有办法获得2024年12月20日之前的历史数据，所以这里只能假设`铜缆高速连接`的概念板块近半年、甚至一年内都是一直保持高热度。 这里选择`铜缆高速连接`单个概念板块整体数据用于计算market breadth交易指标，然后选取了板块中的其中一只股票`沃尔核材（002130）`作为交易标的。
## 策略一
交易策略：
- 交易策略其实非常简单，类似布林带操作，这里设置upper_band为80， lower_band为20， 只要板块超过upper_band就卖出，跌破lower_band就买入；
- 回测时间为2024-06-07至2024-12-24；
- `market breadth`的周期为20日SMA
- 回测框架用Backtesting.py

结果是什么呢？ `连Buy & Hold策略都不如`。

---
![image](https://github.com/user-attachments/assets/be6709ad-2887-4419-90ae-a3b6490336ae)

## 策略二
交易策略：
- 与上述策略相反，追热点，这里的逻辑是追涨；
- 设置upper_band为80， lower_band为20， 只要板块超过upper_band就买入，跌破lower_band就卖出；

结果是比上面的要好一丢丢，但是还是`比不过 Buy & Hold策略`。

---
![image](https://github.com/user-attachments/assets/b94fb7a3-0ada-418d-a238-2c6fb5cd2bb7)

---

接着我又尝试不用20,80作为阀值，改用遍历的形式：
```python
stats = bt.optimize(
    upper_band=range(70, 90, 5),
    lower_band=range(10, 30, 5),
    maximize='Equity Final [$]',
    method = 'grid',
    max_tries=56,
    random_state=0,
    return_heatmap=True)
```
最后得到最优的结果：
```python
<Strategy SignalStrategy(upper_band=70,lower_band=15)>
```
---
![image](https://github.com/user-attachments/assets/d843cec8-6641-4865-94fe-1e97f00492c3)

![image](https://github.com/user-attachments/assets/979ee2d6-5972-4150-ad66-523698b32804)

还是那句话，`结果是比上面的要好一丢丢，但是还是比不过 Buy & Hold策略`。

# 短期结论
> [!IMPORTANT]
>  这种看换手率、看资金流入的炒股方式本质上就是追热点。既然是追热点，我们大概率应该在超买区（sell zone）继续买入达到追热点的效果。 上面只用了单一只股票`沃尔核材（002130）`作为回测，说句实话，也没有多大的参考意义。
>
> 买卖阀值这东西比较随意，但大概率是30以下，70以上作为阀值。

可以继续探讨的方向：
- `market breadth`设置的天数问题；
- 交易标的的市值高低的影响；
- 资金净流入对股票持续上涨的影响；


---
未完，待续。
---