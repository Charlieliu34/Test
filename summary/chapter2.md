# 贰、行情

## 一、Request-Reply

  Client **发送 Request，等待 Server 回复 Reply。client 透过此联机登入即发送订阅报价指令。**

### 1.账号登入

a.登入指令

```text
{
  "Request":"LOGIN",
  "Param":
  {
    "SystemName":"xxxxx",
    "ServiceKey":"xxxxx"
  }
}
```

b.登入回复

* 登入验证成功 

  ```text
  {
    "Reply":"LOGIN",
  "Success":"OK",
    "SessionKey":"XXXXXXXXX",
  "SubPort":n
  }
  ```

注1:SessionKey，之后所有Request指令一律需要带SessionKey。SessionKey错误，指令部执行。

注2:SubPort提供client用来建立第二条ZeroMQ Pub/Sub联机，用来接收主推行情。



* 登入验证失败

  ```text
  {
    "Reply":"LOGIN",
  "Success":"Fail",
    "SessionKey":""
  }
  ```

c.注销指令

```text
{
  "Request":"LOGOUT",
}
```

### 2.全部合约查询

a.全部合约查询

```text
{
  "Request":"QUERYALLINSTRUMENT",
  "SessionKey":"XXXXXXXXX"
  "Type":"Future"
}
```

**Type:Future or Options or Stock**

  
 b.全部合约回复

```text
{
    "Reply":"QUERYALLINSTRUMENT",
  "Success":"OK",
  "Instruments":
  {"CHS" : "期货", 
   "CHT" : "期货", 
   "ENG" : "ALGOSTARS_EXG", 
   "EXGID" : "", 
   "Node" : 
   [
      {"CHS" : "中国金融交易所", 
       "CHT" : "中国金融交易所", 
       "ENG" : "CFFEX", 
       "EXGID" : null, 
       "Node" : 
         [
             {"CHS" : "热门月", 
              "CHT" : "热门月", 
              "ENG" : "HOT",
              "Contracts" : 
              ["TC.F.CFFEX.IF.201906", 
               "TC.F.CFFEX.IH.201906", 
               "TC.F.CFFEX.IC.201906", 
               "TC.F.CFFEX.TF.201906", 
               "TC.F.CFFEX.T.201909"], 
              "ExpirationDate" : 
              ["20190621", 
               "20190621", 
               "20190621", 
               "20190614", 
               "20190916"], 
              "InstrumentID" : 
              ["IF1906", 
               "IH1906", 
               "IC1906",…]
             …}
         …]
      …}
   …]
  …}
}
```

### 3.商品交易设定查询

a.商品交易设定查询

```text
{
  "Request":"QUERYINSTRUMENTINFO",
  "SessionKey":"XXXXXXXXX",
  "Symbol":"TC.F.CFFEX.IF.201903"
}
```

b.商品交易设定回复

```text
{
    "Reply":"QUERYINSTRUMENTINFO",
  "Success":"OK",
  "Info":
{
"TC.F.CFFEX.IF":{"Currency":"CNY","Denominator":"1","EXG":"CFFEX","EXG.CTP":"CFFEX","EXG.SIM":"CFFEX","Group.CHS":"指数","Group.CHT":"指数","Group.ENG":"Equities","I3_TickSize":"0.2","Multiplier.ESNH":"1","Multiplier.ESXYA":"1","Multiplier.GQ2":"1","Name.CHS":"沪深300","Name.CHT":"沪深300","Name.ENG":"CSI 300","pinyin":"hs300","OpenCloseTime":"09:30~11:30;13:00~15:00","Symbol":"IF","Symbol.CTP":"IF"...}
  }
}
```

### 4.订阅报价

a.订阅指令

```text
{
  "Request":"SUBQUOTE",
  "SessionKey":"XXXXXXXXX"
"Param":
  {
    "Symbol":"TC.F.CFFEX.IF.201903",
    "SubDataType":"REALTIME",
     ...
  }
}
```

b.订阅回复

```text
{
    "Reply":"SUBQUOTE",
  "Success":"OK"
}
```

c.解除订阅指令

```text
{
  "Request":"UNSUBQUOTE",
  "SessionKey":"XXXXXXXXX"
"Param":
  {
    "Symbol":"TC.F.CFFEX.IF.201903",
    "SubDataType":"REALTIME",
     ...
  }
}
```

注1:报价订阅成功，会在Pub/Sub联机收到行情。

<table>
  <thead>
    <tr>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Symbol</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5408;&#x7EA6;&#x4EE3;&#x7801;</td>
    </tr>
    <tr>
      <td style="text-align:left">SubDataType</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x5B9E;&#x65F6;&#x884C;&#x60C5; (REALTIME)</p>
        <p>&#x5386;&#x53F2;ticks (TICKS)</p>
        <p>&#x5386;&#x53F2;1K (1K)</p>
        <p>&#x5386;&#x53F2;&#x65E5;K (DK)</p>
        <p>Greeks (GREEKS)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">StartTime</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">YYMMDDHH</td>
    </tr>
    <tr>
      <td style="text-align:left">EndTime</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">YYMMDDHH</td>
    </tr>
  </tbody>
</table>### 

### 5.PING/PONG

```text
{
  "Request":"PING",
  "SessionKey":"XXXXXXXXX"
}
回复
{
   "Reply":"PONG"
}
```

## 

## 二、Publish-Subscribe

  Client **透过 pub/sub 联机，接收主推行情**

### 1.实时行情

```text
TC.F.CFFRX.IF.201903:{
    "DataType":"REALTIME",
  "Quote":
  {
     "Symbol":"TC.F.CFFRX.IF.201903",
     "TradingPrice":"3886.5",
     "TradeQuantity":"35435",
     ...
  }
}
```

|  |  |  |
| :--- | :--- | :--- |
| Symbol | BSTR | 合约代码 |
| TradeDate | BSTR | 交易日期 |
| FilledTime | BSTR | 成交时间 |
| TradingPrice | BSTR | 成交价 |
| TradeQuantity | BSTR | 成交单量 |
| Change | BSTR | 涨跌 |
| TradeVolume | BSTR | 累计成交量 |
| OpeningPrice | BSTR | 开盘价 |
| HighPrice | BSTR | 当日最高价 |
| LowPrice | BSTR | 当日最低价 |
| ClosingPrice | BSTR | 收盘价 |
| ReferencePrice | BSTR | 参考价 |
| UpperLimitPrice | BSTR | 涨停价 |
| LowerLimitPrice | BSTR | 跌停价 |
| YClosedPrice | BSTR | 昨收 |
| YTradeVolume | BSTR | 昨量 |
| OpenInterest | BSTR | 今日未平仓量 |
| YOpenInterest | BSTR | 昨日未平仓量 |
| FlagOfBuySell | BSTR | 内外盘 |
| OpenTime | BSTR | 开盘时间 |
| CloseTime | BSTR | 收盘时间 |
| Bid | BSTR | 买价\#1 |
| Bid1 | BSTR | 买价\#2 |
| Bid2 | BSTR | 买价\#3 |
| Bid3 | BSTR | 买价\#4 |
| Bid4 | BSTR | 买价\#5 |
| Bid5 | BSTR | 买价\#6 |
| Bid6 | BSTR | 买价\#7 |
| Bid7 | BSTR | 买价\#8 |
| Bid8 | BSTR | 买价\#9 |
| Bid9 | BSTR | 买价\#10 |
| BidVolume | BSTR | 买量\#1 |
| BidVolume1 | BSTR | 买量\#2 |
| BidVolume2 | BSTR | 买量\#3 |
| BidVolume3 | BSTR | 买量\#4 |
| BidVolume4 | BSTR | 买量\#5 |
| BidVolume5 | BSTR | 买量\#6 |
| BidVolume6 | BSTR | 买量\#7 |
| BidVolume7 | BSTR | 买量\#8 |
| BidVolume8 | BSTR | 买量\#9 |
| BidVolume9 | BSTR | 买量\#10 |
| Ask | BSTR | 卖价\#1 |
| Ask1 | BSTR | 卖价\#2 |
| Ask2 | BSTR | 卖价\#3 |
| Ask3 | BSTR | 卖价\#4 |
| Ask4 | BSTR | 卖价\#5 |
| Ask5 | BSTR | 卖价\#6 |
| Ask6 | BSTR | 卖价\#7 |
| Ask7 | BSTR | 卖价\#8 |
| Ask8 | BSTR | 卖价\#9 |
| Ask9 | BSTR | 卖价\#10 |
| AskVolume | BSTR | 卖量\#1 |
| AskVolume1 | BSTR | 卖量\#2 |
| AskVolume2 | BSTR | 卖量\#3 |
| AskVolume3 | BSTR | 卖量\#4 |
| AskVolume4 | BSTR | 卖量\#5 |
| AskVolume5 | BSTR | 卖量\#6 |
| AskVolume6 | BSTR | 卖量\#7 |
| AskVolume7 | BSTR | 卖量\#8 |
| AskVolume8 | BSTR | 卖量\#9 |
| AskVolume9 | BSTR | 卖量\#10 |
| TotalBidCount | BSTR | 委托买进总笔数 |
| TotalBidVolume | BSTR | 委托买进总数量 |
| TotalAskCount | BSTR | 委托卖出总笔数 |
| TotalAskVolume | BSTR | 委托卖出总数量 |
| BuyCount | BSTR | 累进买进成交笔数 |
| SellCount | BSTR | 累进卖出成交笔数 |
| SettlementPrice | BSTR | 结算价 |
| EndDate | BSTR | 到期日 |

### 

### 2.Greeks

```text
TC.F.CFFRX.IF.201903:{
    "DataType":"GREEKS",
  "Quote":
  {
     "Symbol":"TC.F.CFFRX.IF.201903",
     "TradingPrice":"3886.5",
     "TradeQuantity":"35435",
     ...
  }
}
```

|  |  |  |
| :--- | :--- | :--- |
| TradingHours | BSTR | 交易时间 |
| TradingDay | BSTR | 交易日 |
| Last | BSTR | 最新价 |
| Vol | BSTR | 合约交易量 |
| Bid | BSTR | 买价 |
| Ask | BSTR | 卖价 |
| ImpVol | BSTR | IV |
| BIV | BSTR | BIV |
| SIV | BSTR | SIV |
| Delta | BSTR | Delta |
| Gamma | BSTR | Gamma |
| Vega | BSTR | Vega |
| Theta | BSTR | Theta |
| Rho | BSTR | Rho |
| TheoVal | BSTR | 理论价 |
| ExtVal | BSTR | 时间价值 |
| Margin | BSTR | 保证金 |
| OI | BSTR | 未平仓量 |
| PreOI | BSTR | 昨日未平仓量 |
| IntVal | BSTR | 内含价值 |
| PreImpVol | BSTR | 昨日IV |
| VIX | BSTR | VIX值 |
| UndVol | BSTR | 目标交易量 |
| UndOI | BSTR | 目标未平仓量 |
| HV\_W4 | BSTR | HV-w4 |
| HV\_W8 | BSTR | HV-w8 |
| HV\_W13 | BSTR | HV-w13 |
| HV\_W26 | BSTR | HV-w26 |
| HV\_W52 | BSTR | HV-w52 |
| UnderlyingAsset | BSTR | 目标 \(using GetGreeksString\) |
| VIX1 | BSTR | VIX1 \(using GetGreeksValue\) |
| VIX2 | BSTR | VIX2 \(using GetGreeksValue\) |
| CallOI | BSTR | Call的未平仓量 \(using GetGreeksValue\) |
| CallVol | BSTR | Call的交易量 \(using GetGreeksValue\) |
| PutOI | BSTR | Put的未平仓量 \(using GetGreeksValue\) |
| PutVol | BSTR | Put的交易量 \(using GetGreeksValue\) |

### 

### 3.DaVinci 讯号

```text
{
    "DataType":"DavinciSignal",
  "Data":
  {
    "Symbol":"XXX",
    "Account":"XXX",
     ...
  }
}
```

|  |  |  |
| :--- | :--- | :--- |
| Symbol | string | 合约 |
| Account | string | 账号 |
| ReferenceVolume | string | 参考口数 |
| TotalScore | string | 总评分 |
| TotalScoreChange | string | 评分变化量 |
| FloatProfit | string | 持仓实时损益 |
| Inc | string | 损益权重分配 |
| RiskTake | string | 动用风险 |
| NewOrderQty | string | 下单数量 |
| TotalN | string | 开放交易的策略总数上限 |
| SumChannel | string | 商品类别 |

## 三、历史资料取得

  **使用 REQ/REP 联机，回补历史数据**

### 1.历史资料

a.回补指令

```text
{
  "Request":"SUBQUOTE",
  "SessionKey":"XXXXXXXXX"
"Param":
  {
    "Symbol":"TC.F.CFFEX.IF.201903",
    "SubDataType":"1K",
     ...
  }
}
```

注1:SubDataType使用TICKS or 1Kor DK，且填写StartTime、EndTime，回补历史数据。

<table>
  <thead>
    <tr>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Symbol</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5408;&#x7EA6;&#x4EE3;&#x7801;</td>
    </tr>
    <tr>
      <td style="text-align:left">SubDataType</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>1 &#x5B9E;&#x65F6;&#x884C;&#x60C5; (REALTIME)</p>
        <p>2 &#x5386;&#x53F2;ticks (TICKS)</p>
        <p>4 &#x5386;&#x53F2;1K (1K)</p>
        <p>5 &#x5386;&#x53F2;&#x65E5;K (DK)</p>
        <p>6 Greeks (GREEKS)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">StartTime</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">YYMMDDHH</td>
    </tr>
    <tr>
      <td style="text-align:left">EndTime</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">YYMMDDHH</td>
    </tr>
    <tr>
      <td style="text-align:left">GreeksType</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>

b.回补指令回复

```text
{
    "Reply":"SUBQUOTE",
  "Success":"OK"
}
```

于PUB/SUB联机，收到数据准备完成通知。



C.订阅回复

```text
TC.F.CFFRX.IF.201903:{
    "DataType":"1K",
  "StartTime":"YYMMDDHH",
  "EndTime":"YYMMDDHH",
  "Symbol":"TC.F.CFFRX.IF.201903",
  "Status":"Ready"
}
```

收报数据准备完成通之后，透过REQ/REP联机取得数据



d.取得历史资指令

```text
{
  "Request":"GETHISDATA",
  "SessionKey":"XXXXXXXXX",
"Param":
  {
    "Symbol":"TC.F.CFFEX.IF.201903",
    "SubDataType":"n",
     "QryIndex":"",
     ...
  }
}
```

注1:SubDataType、StartTime、EndTime，需与回补数据指令相同。

d.取得历史资指令

```text
TC.F.CFFRX.IF.201903:{
    "DataType":"1K",
  "HisData":
  [
     {
       "Date":"YYYYMMDD",
       "Time":"HHMMSS",
       "Open":"3882",
       "High":"3886.5",
       "QryIndex":""
       ...
     },
     {
       "Date":"YYYYMMDD",
       "Time":"HHMMSS",
       "Open":"3882",
       "High":"3886.5",
       "QryIndex":""
       ...
     }
  ]

```

1K / DK 数据字段

|  |  |  |
| :--- | :--- | :--- |
| Date | BSTR | 日期 |
| Time | BSTR | 时间 |
| Open | BSTR | 开盘价 |
| High | BSTR | 最高价 |
| Low | BSTR | 最低价 |
| Close | BSTR | 收盘价 |
| Volume | BSTR | 成交量 |
| UpTick | BSTR | 分K上涨Tick数 |
| UpVolume | BSTR | 分K上涨交易量 |
| DownTick | BSTR | 分K下跌Tick数 |
| DownVolume | BSTR | 分K下跌交易量 |
| UnchVolume | BSTR | 未变动交易量 |



Ticks 数据字段

|  |  |  |
| :--- | :--- | :--- |
| Date | BSTR | 日期 |
| FilledTime | BSTR | 成交时间 |
| Bid | BSTR | 买价 |
| Ask | BSTR | 卖价 |
| TradingPrice | BSTR | 成交价 |
| TradeQuantity | BSTR | 成交单量 |
| TradeVolume | BSTR | 总量 |

