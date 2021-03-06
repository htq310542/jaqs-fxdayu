# Optimizer

## 介绍
Optimizer是optimizer模块中的一个核心类，提供了因子算法参数优化的功能

*** 步骤 ***
1. 实例化Optimizer
2. 进行因子计算和参数优化

# step 1 实例化Optimizer

## __init__

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.__init__(*args, **kwargs) `

**简要描述：**

- 初始化Optimizer

**参数：**


|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|dataview|是 |jaqs.data.Dataview|包含了因子相关的所有标的证券及因子计算所要用到的所有字段的数据集|
|formula|否 |string|需要优化的公式：如'(open - Delay(close, l1)) / Delay(close, l2)'|
|params|否 |dict|需要优化的参数范围：如{"LEN1"：range(1,10,1),"LEN2":range(1,10,1)}|
|name|否 |string|信号的名称|
|price |是，price与ret二选一  |pandas.DataFrame|因子涉及到的股票的价格数据，用于作为进出场价用于计算收益,日期为索引，股票品种为columns|
|ret |是，price与ret二选一  |pandas.DataFrame| 因子涉及到的股票的持有期收益，日期为索引，股票品种为columns|
|benchmark_price | 否  |pandas.DataFrame or pandas.Series|基准价格，日期为索引。在price参数不为空的情况下，该参数生效，用于计算因子涉及到的股票的持有期**相对收益**--相对基准。默认为空，为空时计算的收益为**绝对收益**。|
|high |否  |pandas.DataFrame|因子涉及到的股票的最高价数据,用于计算持有期潜在最大上涨收益,日期为索引，股票品种为columns,默认为空|
|low |否  |pandas.DataFrame|因子涉及到的股票的最低价数据,用于计算持有期潜在最大下跌收益,日期为索引，股票品种为columns,默认为空|
|period |否  |int|持有周期,默认为5,即持有5天|
|n_quantiles |否  |int|根据每日因子值的大小分成n_quantiles组,默认为5,即将因子每天分成5组|
|mask |否  |pandas.DataFrame|一张由bool值组成的表格,日期为索引，股票品种为columns，表示在做因子分析时是否要对某期的某个品种过滤。对应位置为True则**过滤**（剔除）——不纳入因子分析考虑。默认为空，不执行过滤操作|
|can_enter |否  |pandas.DataFrame|一张由bool值组成的表格,日期为索引，股票品种为columns，表示某期的某个品种是否可以买入(进场)。对应位置为True则可以买入。默认为空，任何时间任何品种均可买入|
|can_exit |否  |pandas.DataFrame|一张由bool值组成的表格,日期为索引，股票品种为columns，表示某期的某个品种是否可以卖出(出场)。对应位置为True则可以卖出。默认为空，任何时间任何品种均可卖出|
|forward |否  |bool|收益对齐方式,forward=True则在当期因子下对齐下一期实现的收益；forward=False则在当期实现收益下对齐上一期的因子值。默认为True|
|commission |否 |float|手续费比例,每次换仓收取的手续费百分比,默认为万分之八0.0008|
|is_event |否 |bool|是否是事件(0/1因子),默认为否|
|is_quarterly |否 |bool|是否是季度因子,默认为否|

**示例：**


```python
import warnings
warnings.filterwarnings('ignore')
```


```python
from jaqs_fxdayu.research import Optimizer
from jaqs_fxdayu.data import DataView

# 加载dataview数据集
dv = DataView()
dataview_folder = './data'
dv.load_dataview(dataview_folder)

# step 1：实例化Optimizer
optimizer = Optimizer(dataview=dv,
                      formula='- Correlation(vwap_adj, volume, LEN)',
                      params={"LEN":range(2,5,1)},
                      name='divert',
                      price=dv.get_ts('close_adj'),
                      high=dv.get_ts('high_adj'),
                      low=dv.get_ts('low_adj'),
                      benchmark_price=None,#=None求绝对收益 #=price_bench求相对收益
                      period=30,
                      n_quantiles=5,
                      commission=0.0008,#手续费 默认0.0008
                      is_event=False,#是否是事件(0/1因子)
                      is_quarterly=False)#是否是季度因子 默认为False
```

    Dataview loaded successfully.



```python
dv.fields
```




    ['sw1',
     'low_adj',
     'high_adj',
     'pe',
     'quarter',
     'low',
     'oper_exp',
     'open_adj',
     'trade_status',
     'close_adj',
     'index_weight',
     'pb',
     'total_oper_rev',
     'high',
     '_limit',
     'ann_date',
     'vwap',
     'vwap_adj',
     'close',
     'adjust_factor',
     'index_member',
     '_daily_adjust_factor',
     '000016.SH_member',
     '000016.SH_weight',
     'volume',
     'momentum',
     'double_SMA',
     'close-high',
     'roe',
     'd-roe']



# step 2 进行因子计算和参数优化

## dataview

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.dataview `

**简要描述：**

- 优化器用到的数据集

**示例：**


```python
optimizer.dataview
```




    <jaqs_fxdayu.data.dataview.DataView at 0x7f684819db00>



## formula

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.formula `

**简要描述：**

- 优化器所优化的因子表达式

**示例：**


```python
optimizer.formula
```




    '- Correlation(vwap_adj, volume, LEN)'



## params

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.params `

**简要描述：**

- 优化器所优化的参数范围

**示例：**


```python
optimizer.params
```




    {'LEN': range(2, 5)}



## name

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.name `

**简要描述：**

- 优化器所优化的信号名称

**示例：**


```python
optimizer.name
```




    'divert'



## period

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.period `

**简要描述：**

- 待优化因子所指定的调仓周期

**示例：**


```python
optimizer.period
```




    30



## all_signals

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.all_signals `

**简要描述：**

- 不同参数下计算得到的signal_data(关于signal_data的定义,详见文档digger部分-signal_data)所组成的字典
- 在初始化Optimizer实例时指定了formula和params后，可以通过Optimizer.get_all_signals()计算不同参数下该公式算得的所有因子值；也可以手动指定

**示例：**


```python
print(optimizer.all_signals)
```

    None


## get_all_signals

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.get_all_signals() `

**简要描述：**

- 通过Optimizer.get_all_signals()计算不同参数下该公式算得的所有因子值，并更新Optimizer.all_signals属性

**示例：**


```python
optimizer.get_all_signals()
```

    Nan Data Count (should be zero) : 0;  Percentage of effective data: 92%
    Nan Data Count (should be zero) : 0;  Percentage of effective data: 94%
    Nan Data Count (should be zero) : 0;  Percentage of effective data: 93%



```python
print(optimizer.all_signals.keys())
print(optimizer.all_signals["divert{'LEN': 2}"].head())
```

    dict_keys(["divert{'LEN': 2}", "divert{'LEN': 3}", "divert{'LEN': 4}"])
                          signal    return  upside_ret  downside_ret  quantile
    trade_date symbol                                                         
    20170503   000002.SZ    -1.0  0.109486    0.208108     -1.000800         3
               000008.SZ    -1.0 -0.071442    0.000463     -0.135901         2
               000009.SZ    -1.0 -0.089585    0.009714     -0.170193         2
               000027.SZ    -1.0 -0.016835    0.075002     -0.082433         3
               000039.SZ    -1.0  0.074825    0.103575     -0.098925         1


## all_signals_perf

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.all_signals_perf `

**简要描述：**

- 不同参数下计算得到的signal_data(关于signal_data的定义,详见文档digger部分-signal_data)的绩效表现所组成的字典
- 在Optimizer.all_signals不为空的情况下，可以通过Optimizer.get_all_signals_perf()计算Optimizer.all_signals中不同因子的表现；
- 在执行过Optimizer.get_all_signals_perf()后才能获取

**返回:**

dict of performance - 不同因子表现所组成的字典
其中每个performance（因子表现）也是一个字典，由ic、ret、space三个key构成，分别对应ic分析表、收益分析表、潜在收益空间分析表(关于这三张表的说明，详见文档-analysis)

**示例：**


```python
print(optimizer.all_signals_perf)
```

    None


## get_all_signals_perf

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.get_all_signals_perf(in_sample_range=None) `

**简要描述：**

- 在Optimizer.all_signals不为空的情况下，通过Optimizer.get_all_signals_perf()计算Optimizer.all_signals中不同因子的表现,并更新Optimizer.all_signals_perf属性；

**参数:**

|字段|必选|类型|说明|
|:----    |:---|:----- |-----   |
|in_sample_range |否|list of int|因子表现计算的时间范围,如[20140101,20160101] 表示计算因子表现所涵盖的数据范围只在20140101到20160101之间。默认为None,在全样本上计算因子表现|


**示例：**


```python
optimizer.get_all_signals_perf()
```


```python
print(optimizer.all_signals_perf.keys())
print(optimizer.all_signals_perf["divert{'LEN': 2}"].keys())
optimizer.all_signals_perf["divert{'LEN': 2}"]["ic"]
```

    dict_keys(["divert{'LEN': 2}", "divert{'LEN': 3}", "divert{'LEN': 4}"])
    dict_keys(['ic', 'ret', 'space', 'signal_name'])





<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>return_ic</th>
      <th>upside_ret_ic</th>
      <th>downside_ret_ic</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>IC Mean</th>
      <td>0.018350</td>
      <td>0.009832</td>
      <td>0.032779</td>
    </tr>
    <tr>
      <th>IC Std.</th>
      <td>0.067582</td>
      <td>0.074829</td>
      <td>0.069326</td>
    </tr>
    <tr>
      <th>t-stat(IC)</th>
      <td>2.367057</td>
      <td>1.145460</td>
      <td>4.121944</td>
    </tr>
    <tr>
      <th>p-value(IC)</th>
      <td>0.020511</td>
      <td>0.255660</td>
      <td>0.000096</td>
    </tr>
    <tr>
      <th>IC Skew</th>
      <td>-0.010015</td>
      <td>0.147546</td>
      <td>0.189683</td>
    </tr>
    <tr>
      <th>IC Kurtosis</th>
      <td>-0.779674</td>
      <td>-0.615603</td>
      <td>0.070359</td>
    </tr>
    <tr>
      <th>Ann. IR</th>
      <td>0.271520</td>
      <td>0.131393</td>
      <td>0.472819</td>
    </tr>
  </tbody>
</table>
</div>



## enumerate_optimizer

- ` jaqs_fxdayu.research.signaldigger.optimizer.Optimizer.enumerate_optimizer(target_type="long_ret",target="Ann. IR",ascending=False,in_sample_range=None) `

**简要描述：**

- 枚举优化。按照指定的参数优化范围遍历每一种可能性，并给出最佳绩效下的排序结果

**参数:**

|字段|必选|类型|说明|
|:----    |:---|:----- |-----   |
|target_type |是|string|待优化的目标类型，有ic类、持有收益类、收益空间类三个大类，下分小类，具体类型见下|
|target |是|string|待优化的目标绩效指标，有ic类、持有收益类、收益空间类三个大类，下分小类，具体类型见下|
|ascending |否|bool|输出结果是否升序排列，默认为False--降序排列(指标越大排名越前)|
|in_sample_range |否|list of int|样本内优化范围 默认为None,在全样本上优化|

#### 优化目标的详细介绍
目前，所有可优化的目标均围绕analysis模块中提供的三张绩效表——ic分析表、收益分析表、潜在收益空间分析表(关于这三张表的详细定义，参考文档-analysis)。

#### target_type:
* ic类:
  return_ic/upside_ret_ic/downside_ret_ic
* 持有收益类
  long_ret/short_ret/long_short_ret/top_quantile_ret/bottom_quantile_ret/tmb_ret
* 收益空间类
  long_space/short_space/long_short_space/top_quantile_space/bottom_quantile_space/tmb_space

#### target:
* ic类 
  "IC Mean", "IC Std.", "t-stat(IC)", "p-value(IC)", "IC Skew", "IC Kurtosis", "Ann. IR"
* 持有收益类 
  't-stat', "p-value", "skewness", "kurtosis", "Ann. Ret", "Ann. Vol", "Ann. IR", "occurance"
* 收益空间类 
  'Up_sp Mean','Up_sp Std','Up_sp IR','Up_sp Pct5', 'Up_sp Pct25 ','Up_sp Pct50 ', 'Up_sp Pct75','Up_sp Pct95','Up_sp Occur','Down_sp Mean','Down_sp Std', 'Down_sp IR', 'Down_sp Pct5','Down_sp Pct25 ','Down_sp Pct50 ','Down_sp Pct75', 'Down_sp Pct95','Down_sp Occur'
  
  
**返回:**

list of performance - 绩效的排序结果（只计算了样本内的绩效）
其中每个performance（因子表现）是一个字典，由ic、ret、space三个key构成，分别对应ic分析表、收益分析表、潜在收益空间分析表(关于这三张表的说明，详见文档-analysis)


**示例：**


```python
ret_best = optimizer.enumerate_optimizer(target_type="top_quantile_ret",#优化目标类型 
                                         target="Ann. IR",#优化目标     
                                         in_sample_range=[20170501,20170801],#样本内范围
                                         ascending=False)
```


```python
print(len(ret_best))
print(ret_best[0].keys())
print(ret_best[0]["signal_name"])
ret_best[0]["ret"]
```

    3
    dict_keys(['ic', 'ret', 'space', 'signal_name'])
    divert{'LEN': 3}





<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>long_ret</th>
      <th>short_ret</th>
      <th>long_short_ret</th>
      <th>top_quantile_ret</th>
      <th>bottom_quantile_ret</th>
      <th>tmb_ret</th>
      <th>all_sample_ret</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>t-stat</th>
      <td>13.663182</td>
      <td>-16.705837</td>
      <td>2.070200</td>
      <td>29.122529</td>
      <td>23.165669</td>
      <td>2.551231</td>
      <td>58.301138</td>
    </tr>
    <tr>
      <th>p-value</th>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.042610</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.013220</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>skewness</th>
      <td>0.180808</td>
      <td>-0.028991</td>
      <td>0.149466</td>
      <td>1.212846</td>
      <td>1.283619</td>
      <td>0.193644</td>
      <td>1.294608</td>
    </tr>
    <tr>
      <th>kurtosis</th>
      <td>-0.543815</td>
      <td>-0.253210</td>
      <td>-0.102499</td>
      <td>5.032953</td>
      <td>4.293791</td>
      <td>-0.182180</td>
      <td>5.098249</td>
    </tr>
    <tr>
      <th>Ann. Ret</th>
      <td>0.332104</td>
      <td>-0.299027</td>
      <td>0.017125</td>
      <td>0.344567</td>
      <td>0.287333</td>
      <td>0.056858</td>
      <td>0.317683</td>
    </tr>
    <tr>
      <th>Ann. Vol</th>
      <td>0.067386</td>
      <td>0.049624</td>
      <td>0.022933</td>
      <td>0.260520</td>
      <td>0.274885</td>
      <td>0.061787</td>
      <td>0.269129</td>
    </tr>
    <tr>
      <th>Ann. IR</th>
      <td>4.928367</td>
      <td>-6.025866</td>
      <td>0.746730</td>
      <td>1.322611</td>
      <td>1.045285</td>
      <td>0.920240</td>
      <td>1.180412</td>
    </tr>
    <tr>
      <th>occurance</th>
      <td>63.000000</td>
      <td>63.000000</td>
      <td>63.000000</td>
      <td>3912.000000</td>
      <td>3963.000000</td>
      <td>63.000000</td>
      <td>19679.000000</td>
    </tr>
  </tbody>
</table>
</div>


