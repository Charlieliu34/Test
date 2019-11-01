# 壹、交易

## 一、Request-Reply

  Client**发送 Request，等待 Server 回复 Reply。client 透过此联机登入、查询帐务。**

### 1.登入

**a.登入指令**

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

**b.登入回复**

* 登入验证成功

  ```text
  {
    "Reply":"LOGIN",
  "Success":"OK",
    "SessionKey":"XXXXXXXXX",
  "SubPort":"n"
  }
  ```

  注 1: SessionKey，之后所有 Request 指令一律需要带 SessionKey。SessionKey 错误，指令不执行。

注 2: SubPort 提供 client 用来建立第二条 ZeroMQ Pub/Sub 联机，用来接收主推回报。

* 登入验证失败

  ```text
  {
    "Reply":"LOGIN",
  "Success":"Fail",
    "SessionKey":""
  "ErrMsg":""
  }
  ```

**c.登出指令**

```text
{
 "Request":"LOGOUT",
 "SessionKey":"XXXXXXXXX"
}
```

### 2.资金账号

a.资金账号查询

```text
{
 "Request":"ACCOUNTS",
 "SessionKey":"XXXXXXXXX"
}
```

b.资金账号回复

```text
{
    "DataType":"ACCOUNTS",
  "Success":"OK",

  "Accounts":
  [
    {

      "BrokerID":"123456",
      "Account":"XXX",
      "UserName":"XXX",
             "AccountName":"XXX",
       ...
    },
    {

      "BrokerID":"123456",
      "Account":"XXX",
      "UserName":"XXX",
             "AccountName":"XXX",
       ...
    }
  ]
}
```

註1:資金帳號欄位於後面章節[資金帳號主推](chapter2.md)說明。

### 3.全部合约查询

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
   "CHT" : "期貨", 
   "ENG" : "ALGOSTARS_EXG", 
   "EXGID" : "", 
   "Node" : 
   [
      {"CHS" : "中国金融交易所", 
       "CHT" : "中國金融交易所", 
       "ENG" : "CFFEX", 
       "EXGID" : null, 
       "Node" : 
         [
             {"CHS" : "热门月", 
              "CHT" : "熱門月", 
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

### 4.商品交易设定查询

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
"TC.F.CFFEX.IF":{"Currency":"CNY","Denominator":"1","EXG":"CFFEX","EXG.CTP":"CFFEX","EXG.SIM":"CFFEX","Group.CHS":"指数","Group.CHT":"指數","Group.ENG":"Equities","I3_TickSize":"0.2","Multiplier.ESNH":"1","Multiplier.ESXYA":"1","Multiplier.GQ2":"1","Name.CHS":"沪深300","Name.CHT":"滬深300","Name.ENG":"CSI 300","pinyin":"hs300","OpenCloseTime":"09:30~11:30;13:00~15:00","Symbol":"IF","Symbol.CTP":"IF"...}
  }
}
```

### 5.回报回补

a.回补回报

```text
{
  "Request":"RESTOREREPORT",
  "SessionKey":"XXXXXXXXX",
    "QryIndex":""
}
```

b.回补回报回复

```text
{
    "Reply":"RESTOREREPORT",
  "Success":"OK",
  "Orders":
    [
      {"ReportID":"123456",
       "Account":"XXX",
       "BrokerID":"XXX",
               "Symbol":"XXX",
               "Side":"n",
        "QryIndex":"n",
       …},
      {"ReportID":"123456",
       "Account":"XXX",
       "BrokerID":"XXX",
               "Symbol":"XXX",
               "Side":"n",
        "QryIndex":"n",
       …}
   ]
}
```

註1:回報欄位於後面章節[回報主推](chapter2.md)說明。

### 6.期货/期权下单

a.下单指令

```text
{
  "Request":"NEWORDER",
  "SessionKey":"XXXXXXXXX",
  "Param":
  {    
    "BrokerID":"xxxxx",
    "Account":"xxxxx",
    "Symbol":"TC.F.xxx.xxx.201710",
    "Price":"xxxxx",
    "StopPrie":"xxxxx",
    "Side":"n",
    ...
  }
}
```

b.下单回复

```text
{
    "Reply":"NEWORDER",
  "Success":"OK"
}
```

注1: 代码一律采用TC代码。

注2: TimeInForce、OrderType、PositionEffect 列出所有可下单形式，请确认上手可支持下单形式，下单时代上正确的type。值接送上手Synthetic="0"，若要TCore洗价Synthetic="1"。

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
      <td style="text-align:left">BrokerID</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5238;&#x5546;</td>
    </tr>
    <tr>
      <td style="text-align:left">Account</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x8D26;&#x53F7;</td>
    </tr>
    <tr>
      <td style="text-align:left">Symbol</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x5546;&#x54C1;ID (&#x5305;&#x542B;&#x590D;&#x5F0F;&#x5546;&#x54C1;ID)</p>
        <p>TC.F2.TWF.FITX.201604^FITX.201606</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Price</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x59D4;&#x6258;&#x4EF7;&#x683C;</p>
        <p>or</p>
        <p>&#x5B57;&#x6BB5; + or - &#x4EF7;&#x683C;</p>
        <p>&#x5B57;&#x6BB5;&#x4E3A;&#x56FA;&#x5B9A;&#x5B57;, &#x53EF;&#x4E3A;LAST
          or BID or ASK or MID or FILLED</p>
        <p>FILLED&#x4E3A;OTO, OTOCO&#x4F7F;&#x7528;, &#x5176;&#x4ED6;&#x5355;&#x4E0D;&#x53EF;&#x4F7F;&#x7528;FILLED</p>
        <p>ex. FILLED-15</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">StopPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x505C;&#x635F;&#x4EF7;</p>
        <p>or</p>
        <p>&#x5B57;&#x6BB5; + or - &#x4EF7;&#x683C;</p>
        <p>&#x5B57;&#x6BB5;&#x4E3A;&#x56FA;&#x5B9A;&#x5B57;, &#x53EF;&#x4E3A;LAST
          or BID or ASK or MID or FILLED</p>
        <p>FILLED&#x4E3A;OTO, OTOCO&#x4F7F;&#x7528;, &#x5176;&#x4ED6;&#x5355;&#x4E0D;&#x53EF;&#x4F7F;&#x7528;FILLED</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Side</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4E70;&#x5356;&#x522B; (&#x590D;&#x5F0F;&#x5355;&#x4E3A;&#x6574;&#x4F53;&#x4E70;&#x5356;&#x65B9;&#x5411;)
        1:Buy 2:Sell</td>
    </tr>
    <tr>
      <td style="text-align:left">TimeInForce</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>0 : None</p>
        <p>1 : ROD Day order</p>
        <p>2 : IOC | FAK Immediate or Cancel | Fill and Kill</p>
        <p>3 : FOK Fill or Kill</p>
        <p>4 : GTC Good-Till-Cancel</p>
        <p>5 : GTD Good-Till-Date</p>
        <p>6 : OPG &#x76D8;&#x524D;&#x9884;&#x7EA6;&#x5355;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">OrderType</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>0 : None Unknown</p>
        <p>1 : Market order (MARKET)</p>
        <p>2 : Limit order (LIMIT)</p>
        <p>3 : Stop order (STOP)</p>
        <p>4 : Stop limit order (STPLMT)</p>
        <p>5 : Trailing Stop (TSTOP)</p>
        <p>6 : Trailing StopLimit (TSTPLMT)</p>
        <p>7 : Market if Touched Order (MIT)</p>
        <p>8 : Limit if Touched Order (LIT)</p>
        <p>9 : Trailing Limit (TLMT)</p>
        <p>10 : &#x5BF9;&#x65B9;&#x4EF7;(HIT)</p>
        <p>11 : &#x672C;&#x65B9;&#x4EF7;(JOIN)</p>
        <p>12 : DOM-Triggered Stop ( DTS )</p>
        <p>13 : DOM-Triggered Stop Limit ( DTSL )</p>
        <p>14 : Breakeven (BE )</p>
        <p>15: &#x4E2D;&#x95F4;&#x4EF7;(MID)</p>
        <p>20 : &#x6700;&#x4F18;&#x4EF7; (BST)</p>
        <p>21 : &#x6700;&#x4F18;&#x4EF7;&#x8F6C;&#x9650;&#x4EF7; (BSTL)</p>
        <p>22 : &#x4E94;&#x6863;&#x5E02;&#x4EF7; (5LvlMKT)</p>
        <p>23 : &#x4E94;&#x6863;&#x5E02;&#x4EF7;&#x8F6C;&#x9650;&#x4EF7; (5LvlMTL)</p>
        <p>24 : &#x5E02;&#x4EF7;&#x8F6C;&#x9650;&#x4EF7; (MTL)</p>
        <p>25: &#x4E00;&#x5B9A;&#x8303;&#x56F4;&#x5E02;&#x4EF7;(MWP)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">ContingentSymbol</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x8FFD;&#x8E2A;Symbol</p>
        <p>&#x4EC5;Synthetic = 1</p>
        <p>OrderType=7 &#x6216; OrderType=8&#x4F7F;&#x7528;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TrailingField</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;Synthetic = 1</p>
        <p>OrderType=5 &#x6216; OrderType=6&#x4F7F;&#x7528;</p>
        <p>0 : None</p>
        <p>1 : Last</p>
        <p>2 : Bid</p>
        <p>3 : Ask</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TrailingType</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;Synthetic = 1</p>
        <p>OrderType=5 &#x6216; OrderType=6&#x4F7F;&#x7528;</p>
        <p>0 : None</p>
        <p>1 : &#x4F9D;&#x4EF7;&#x683C;</p>
        <p>2 : &#x4F9D;TrailingPrice&#x767E;&#x5206;&#x6BD4;</p>
        <p>3 : &#x4F9D;TickSize&#x51E0;&#x8DF3;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TrailingAmount</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;Synthetic = 1</p>
        <p>OrderType=5 &#x6216; OrderType=6&#x4F7F;&#x7528;</p>
        <p>TrailingType=1&#x65F6;, TrailingAmount&#x4E3A;&#x767E;&#x5206;&#x6BD4;</p>
        <p>TrailingType=2&#x65F6;, TrailingAmount&#x4E3A;&#x70B9;&#x6570;</p>
        <p>TrailingType=3&#x65F6;, TrailingAmount&#x4E3A;&#x51E0;&#x8DF3;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TouchPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;Synthetic = 1</p>
        <p>OrderType=7 &#x6216; OrderType=8&#x4F7F;&#x7528;</p>
        <p>&#x89E6;&#x53D1;&#x4EF7;</p>
        <p>or</p>
        <p>&#x5B57;&#x6BB5; + or - &#x4EF7;&#x683C;</p>
        <p>&#x5B57;&#x6BB5;&#x4E3A;&#x56FA;&#x5B9A;&#x5B57;, &#x53EF;&#x4E3A;LAST
          or BID or ASK or FILLED</p>
        <p>FILLED&#x4E3A;OTO, OTOCO&#x4F7F;&#x7528;, &#x5176;&#x4ED6;&#x5355;&#x4E0D;&#x53EF;&#x4F7F;&#x7528;FILLED</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TouchField</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;Synthetic = 1</p>
        <p>OrderType=3 &#x6216; OrderType=4 &#x6216; OrderType=7 &#x6216; OrderType=8&#x6216;OrderType=12
          &#x6216; OrderType=13&#x4F7F;&#x7528;</p>
        <p>0 : None</p>
        <p>1 : Last</p>
        <p>2 : Bid</p>
        <p>3 : Ask</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TouchCondition</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;Synthetic = 1</p>
        <p>OrderType=7 &#x6216; OrderType=8&#x4F7F;&#x7528;</p>
        <p>0 : touch or &#x7A7F;&#x4EF7;</p>
        <p>1 : Greater</p>
        <p>2 : GreaterEqual</p>
        <p>3 : Equl</p>
        <p>4 : LessEqual</p>
        <p>5 : Less</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">OrderQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4E0B;&#x5355;&#x53E3;&#x6570;</td>
    </tr>
    <tr>
      <td style="text-align:left">PositionEffect</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>0 : Open Open position</p>
        <p>1 : Close Close position</p>
        <p>2 : &#x5E73;&#x4ECA;</p>
        <p>3 : &#x5E73;&#x6628;</p>
        <p>4 : Auto Auto select Open/Cloe position</p>
        <p>5 : &#x7279;&#x6B8A;&#x81EA;&#x52A8;&#x5355; &#x6709;&#x6628;&#x4ED3;&#x5148;&#x5E73;&#x6628;
          &#x5176;&#x5B83;&#x4E00;&#x5F8B;&#x8F6C;&#x5F00;&#x4ED3;</p>
        <p>10 : &#x5907;&#x5151;&#x5F00;&#x4ED3;</p>
        <p>11: &#x5907;&#x5151;&#x5E73;&#x4ED3;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">DayTrade</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>0 : None</p>
        <p>1 : &#x5F53;&#x51B2;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Synthetic</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>0 : None</p>
        <p>1 : Synthetic</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">GroupType</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>0 : None</p>
        <p>1 : Normal</p>
        <p>2 : OCO</p>
        <p>3 : OTO</p>
        <p>4 : OTOCO</p>
        <p>5 : OTOs(&#x5355;&#x7EBF;)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">GroupID</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">GroupID or OCOID or OTOID or OTOCOID or OTOSID</td>
    </tr>
    <tr>
      <td style="text-align:left">ChasePrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x8FFD;&#x4EF7;</p>
        <p>&#x683C;&#x5F0F;&#x4E3A;[&#x6BCF;&#x6B21;&#x589E;&#x52A0;&#x4EF7;&#x683C;|&#x8FFD;&#x4EF7;&#x51E0;&#x6B21;|&#x6BCF;&#x6B21;&#x8FFD;&#x4EF7;&#x51E0;&#x79D2;|&#x6700;&#x540E;&#x4E0B;&#x5E02;&#x4EF7;&#x6216;&#x5220;&#x5355;&#x6216;&#x6302;&#x5355;(M
          or C or L)|PriceType]</p>
        <p>&#x9650;&#x4EF7;&#x8FFD;&#x4EF7;</p>
        <p>&#x683C;&#x5F0F;&#x4E3A;[0|&#x8FFD;&#x4EF7;&#x9650;&#x5236;&#x51E0;&#x8DF3;|-11
          (Chase Trailing) or -12 (Chase if touch)|&#x6700;&#x540E;&#x4E0B;&#x5E02;&#x4EF7;&#x6216;&#x5220;&#x5355;&#x6216;&#x6302;&#x5355;(M
          or C or L)|PriceType]</p>
        <p>&#x6216;&#x662F;[1|&#x8FFD;&#x4EF7;&#x51E0;&#x6B21;|-11 (Chase Trailing)
          or -12 (Chase if touch)|&#x6700;&#x540E;&#x4E0B;&#x5E02;&#x4EF7;&#x6216;&#x5220;&#x5355;&#x6216;&#x6302;&#x5355;(M
          or C or L)|PriceType]</p>
        <p>a.&#x82E5; &#x6BCF;&#x6B21;&#x589E;&#x52A0;&#x4EF7;&#x683C; &lt; 1000
          &#x8868;&#x793A;&#x51E0;&#x8DF3;Ticks</p>
        <p>b.&#x8FFD;&#x4EF7;&#x51E0;&#x79D2;=-11 (Chase Trailing)</p>
        <p>c.&#x8FFD;&#x4EF7;&#x51E0;&#x79D2;=-12 (Chase if touch)</p>
        <p>d.Price Type=(LTP, BID, ASK)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">SlicedType</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4E0D;&#x652F;&#x63F4;&#x7FA4;&#x7EC4;&#x5355;</p>
        <p>0 : None 5 : AllSliced After PriceTrigger</p>
        <p>1 : Iceberg</p>
        <p>2 : TimeSliced</p>
        <p>3 : VolumeSliced</p>
        <p>4 : AllSliced</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">DiscloseQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;SlicedType =1 &#x6216; SlicedType =2 &#x6216;SlicedType =3 &#x6216;SlicedType
          =4&#x6216;SlicedType =5&#x4F7F;&#x7528;</p>
        <p>&#x9010;&#x7B14;&#x63ED;&#x9732;&#x6570;&#x91CF;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Variance</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;SlicedType =1 &#x6216; SlicedType =2&#x6216; SlicedType =3&#x4F7F;&#x7528;</p>
        <p>&#x9010;&#x7B14;&#x53D8;&#x52A8;&#x6BD4;&#x4F8B;%</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Interval</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;SlicedType =2&#x6216; SlicedType =3&#x4F7F;&#x7528;</p>
        <p>SlicedType =2 : &#x95F4;&#x9694;&#x65F6;&#x95F4;&#xFF0C;ms</p>
        <p>SlicedType =3 : &#x95F4;&#x9694;&#x6210;&#x4EA4;&#x91CF;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">LeftoverAction</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;SlicedType =2&#x6216; SlicedType =3&#x4F7F;&#x7528;</p>
        <p>0 : Leave &#x7EE7;&#x7EED;&#x6302;&#x7740;(&#x9884;&#x8BBE;)</p>
        <p>1 : Merge &#x53D6;&#x6D88;&#x8BE5;&#x7B14;&#xFF0C;&#x5269;&#x4F59;&#x6570;&#x91CF;&#x52A0;&#x5165;&#x4E0B;&#x7B14;&#x4E0B;&#x5355;</p>
        <p>2 : Market &#x8F6C;&#x5E02;&#x4EF7;&#x5355;</p>
        <p>3 : Payup &#x8FFD;&#x4EF7;(&#x672A;&#x5B9E;&#x505A;)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">SlicedPriceField</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>0 -&gt; Fixed price mode</p>
        <p>&#x975E;0 -&gt; Relative price mode</p>
        <p>0 : None</p>
        <p>1 : Last</p>
        <p>2 : Bid</p>
        <p>3 : Ask</p>
        <p>4 : &#x5BF9;&#x65B9;&#x4EF7;</p>
        <p>5 : &#x672C;&#x65B9;&#x4EF7;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">SlicedTicks</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>SlicedPriceField != 0</p>
        <p>TickSize&#x51E0;&#x8DF3;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Breakeven</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;OrderType=14&#x4F7F;&#x7528;</p>
        <p>Breakeven&#x89E6;&#x53D1;&#x4EF7;</p>
        <p>or</p>
        <p>&#x5B57;&#x6BB5; + or - &#x4EF7;&#x683C;</p>
        <p>&#x5B57;&#x6BB5;&#x4E3A;&#x56FA;&#x5B9A;&#x5B57;, &#x53EF;&#x4E3A;LAST
          or BID or ASK or FILLED</p>
        <p>FILLED&#x4E3A;OTO, OTOCO&#x4F7F;&#x7528;, &#x5176;&#x4ED6;&#x5355;&#x4E0D;&#x53EF;&#x4F7F;&#x7528;FILLED</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">BreakevenOffset</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;OrderType=14&#x4F7F;&#x7528;</p>
        <p>Breakeven&#x505C;&#x635F;&#x4EF7;</p>
        <p>or</p>
        <p>&#x5B57;&#x6BB5; + or - &#x4EF7;&#x683C;</p>
        <p>&#x5B57;&#x6BB5;&#x4E3A;&#x56FA;&#x5B9A;&#x5B57;, &#x53EF;&#x4E3A;LAST
          or BID or ASK or FILLED</p>
        <p>FILLED&#x4E3A;OTO, OTOCO&#x4F7F;&#x7528;, &#x5176;&#x4ED6;&#x5355;&#x4E0D;&#x53EF;&#x4F7F;&#x7528;FILLED</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Threshold</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4EC5;OrderType=12&#x6216;OrderType=13&#x4F7F;&#x7528;</td>
    </tr>
    <tr>
      <td style="text-align:left">ExtCommands</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x5EF6;&#x4F38;&#x4E0B;&#x5355;&#x53C2;&#x6570; &#x591A;&#x4E2A;&#x53C2;&#x6570;&#x8BF7;&#x7528;&#xFF1B;&#x9694;&#x5F00;</p>
        <p>1.&#x81EA;&#x52A8;&#x5355;&#x53CD;&#x5411;&#x5EF6;&#x8FDF; &#x8BF7;&#x5E26;</p>
        <p>DelayTransPosition=3000</p>
        <p>&#x8868;&#x5EF6;&#x8FDF;3&#x79D2;</p>
        <p>2.&#x4E0B;&#x5355;&#x524D; &#x5C06;&#x5E73;&#x4ED3;&#x6302;&#x5355;&#x90FD;&#x5220;&#x5355;</p>
        <p>CancelCloseWorking=1 or 2</p>
        <p>3.&#x4E0B;&#x5355;&#x901F;&#x5EA6;&#x662F;&#x5426;&#x8C03;&#x6162;&#x4E0E;&#x8BBE;&#x5B9A;&#x76F8;&#x540C;</p>
        <p>FitOrderFreq=1</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Consecutive</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">1 : &#x8FDE;&#x7EED;&#x9001;&#x5355; 2:TW&#x8FDE;&#x7EED;IOC</td>
    </tr>
    <tr>
      <td style="text-align:left">NumberOfRetries</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;Consecutive=1&#x4F7F;&#x7528;</p>
        <p>&#x6301;&#x7EED;&#x9001;&#x5355;&#x51E0;&#x6B21;, NumberOfRetries&gt;=9999
          &#x8868;&#x793A;&#x65E0;&#x7EBF;&#x6B21;&#x6570;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">RetryInterval</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4EC5;Consecutive=1&#x4F7F;&#x7528;</p>
        <p>&#x6BCF;&#x6B21;&#x9001;&#x5355;&#x95F4;&#x9694;ms, RetryInterval=0&#x8868;&#x662F;&#x7ACB;&#x5373;&#x9001;&#x5355;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Strategy</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7B56;&#x7565;&#x540D;&#x79F0;</td>
    </tr>
    <tr>
      <td style="text-align:left">UserKey1</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5C06;&#x53EF;&#x5728;&#x56DE;&#x62A5;&#x7684;UserKey1&#x5B57;&#x6BB5;&#x5F97;&#x5230;&#x76F8;&#x540C;&#x7684;&#x5B57;&#x7B26;&#x4E32;</td>
    </tr>
  </tbody>
</table>

| **下单错误码** |  |
| :---: | :--- |
| **-10** | Unknow Error |
| **-11** | 买卖别不对 |
| **-12** | 复式单商品代码解晰错误 |
| **-13** | 下单账号, 不可下此交易所商品 |
| **-14** | 下单错误, 不支持的价格 或 OrderType 或 TimeInForce |
| **-15** | 不支援证券下单 |
| **-20** | 联机未建立 |
| **-22** | 价格的TickSize错误 |
| **-23** | 下单数量超过该商品的上下限 |
| **-24** | 下单数量错误 |
| **-25** | 价格不能小于和等于0 \(市价类型不会去检查\) |

### 

### 7.删改单

a.改单指令

```text
{
  "Request":"REPLACEORDER",
  "SessionKey":"XXXXXXXXX",
  "Param":
  {
    "ReportID":"123456",
    "ReplaceExecType":"n",
    "OrderQty":"n",
    "Price":"xxxxx",
      "StopPrice":"xxxxx",
  }    
}
```

b.改单回复

```text
{
    "Reply":"REPLACEORDER",
  "Success":"OK",
  "ReportID":"123456"
}
```

c.删单指令

```text
{
  "Request":"CANCELORDER",
  "Param":
  {
    "ReportID":"123456"
  }    
}
```

d.删单回复

```text
{
    "Reply":"CANCELORDER",
  "Success":"OK",
  "ReportID":"123456"
}
```

### 8.期货/期权 资金查询

a.资金查询指令

```text
{
  "Request":"MARGINS",
  "SessionKey":"XXXXXXXXX"
"AccountMask":"XXXXXXXXX"
}
```

b.资金查询回复

```text
{
  "Reply":"MARGINS",
  "Success":"OK",
  "Margins":
    [
      {"BrokerID":"XXX",
        "Account":"XXX",
       "TransactDate":"n",
       "TransactTime":"n",
       "BeginningBalance":"XXX",
       "Commissions":"XXX",
       ...},
      {"BrokerID":"XXX",
       "Account":"XXX",
       "TransactDate":"n",
       "TransactTime":"n",
       "BeginningBalance":"XXX",
       "Commissions":"XXX",
       ...}
   ]   
}
```

注1:并非所有字段都有数据，要视上手提供多少数据。

|  |  |  |
| :--- | :--- | :--- |
| BrokerID | BSTR | 券商 \(对应下单与回报字段\) |
| BrokerName | BSTR | 券商名称 |
| Account | BSTR | 账号 \(对应下单与回报字段\) |
| AccountName | BSTR | 账号名称 |
| TransactDate | BSTR | 更新日期 UTC+0 |
| TransactTime | BSTR | 更新时间 UTC+0 |
| BeginningBalance | BSTR | 期初结存 |
| Commissions | BSTR | 交易手续费 |
| FrozenCommission | BSTR | 冻结手续费 |
| ExchangeClearinigFee | BSTR | 交易所费用与结算费用 |
| BrokerageFee | BSTR | 经纪商费用 |
| GrossPL | BSTR | 已实现损益\(未扣除费用\) |
| OptionPremium | BSTR | 选择权权利金收入 |
| CashIn | BSTR | 证券买卖当日收支 |
| NetPL | BSTR | 已实现损益\(扣除费用并加上选择权权利金收入\) |
| Deposit | BSTR | 今日入金 |
| Withdraw | BSTR | 今日出金 |
| CashActivity | BSTR | 今日出入金 |
| ExcessEquity | BSTR | 可用资金 |
| WithdrawQuota | BSTR | 可取资金 |
| EndingBalance | BSTR | 期末结存 |
| OpenTradeEquity | BSTR | 未平仓损益\(包含选择权和期货\) |
| TotalEquity | BSTR | 总权益数 |
| OptionNetMarketValue | BSTR | 选择权市值 |
| AccountValueAtMarket | BSTR | 账户市值 |
| InitialMarginRequirement | BSTR | 初始保证金 |
| MaintenanceMarginRequirement | BSTR | 维持保证金 |
| CurrMargin | BSTR | 当前保证金 |
| MarginDeficit | BSTR | 追缴保证金 |
| FrozenMargin | BSTR | 冻结保证金 |
| FrozenCash | BSTR | 冻结资金 |
| ReserveBalance | BSTR | 保底准备金 |
| Credit | BSTR | 信用额度 |
| Mortgage | BSTR | 质押金额 |
| PreMortgage | BSTR | 上次质押金额 |
| PreCredit | BSTR | 上次信用额度 |
| PreDeposit | BSTR | 上次存款额 |
| PreMargin | BSTR | 上次占用的保证金 |
| ExchangeMargin | BSTR | 交易所保证金 |
| DeliveryMargin | BSTR | 投资者交割保证金 |
| ExchangeDeliveryMargin | BSTR | 交易所交割保证金 |
| CurrencyToSystem | BSTR | 系统币别 |
| CurrencyConversionRate | BSTR | 币别转换比率\(转换到账户币别使用\) |
| CurrencyToClient | BSTR | 账户币别 |
| ConvertedAccountValueAtMkt | BSTR | 币别转换后账户市值 |
| ExerciseIncome | BSTR | 行权盈亏 到期履约损益　 |
| IncomeBalance | BSTR | 盈亏金额 |
| InterestBase | BSTR | 利息基数 |
| Interest | BSTR | 利息收入 |
| MarginLevel | BSTR | 保证金水平 风险指标 |
| UPLForOptions | BSTR | 未冲销选择权浮动损益 |
| LongOptionNetMarketValue | BSTR | 未冲销买方选择权市值 |
| ShortOptionNetMarketValue | BSTR | 未冲销卖方选择权市值 |
| FrozenpPremium | BSTR | 委托权利金 |
| MarginExcess | BSTR | 加收原始保证金 |
| AdjustedEquity | BSTR | 契约调整权益数 加/减项 |
| PreFundMortgageIn | BSTR | 上次货币质入金额 |
| PreFundMortgageOut | BSTR | 上次货币质出金额 |
| FundMortgageIn | BSTR | 货币质入金额 |
| FundMortgageOut | BSTR | 货币质出金额 |
| FundMortgageAvailable | BSTR | 货币质押余额 |
| MortgageableFund | BSTR | 可质押货币金额 |
| SpecProductMargin | BSTR | 特殊产品占用保证金 |
| SpecProductFrozenMargin | BSTR | 特殊产品冻结保证金 |
| SpecProductCommission | BSTR | 特殊产品手续费 |
| SpecProductFrozenCommission | BSTR | 特殊产品冻结手续费 |
| SpecProductPositionProfit | BSTR | 特殊产品持仓盈亏 |
| SpecProductCloseProfit | BSTR | 特殊产品平仓盈亏 |
| SpecProductPositionProfitByAlg | BSTR | 根据持仓盈亏算法计算的特殊产品持仓盈亏 |
| SpecProductExchangeMargin | BSTR | 特殊产品交易所保证金 |
| FloatProfitByDate | BSTR | 逐日浮动盈亏 |
| FloatProfitByTrade | BSTR | 逐笔浮动盈亏 |
| FutureProfitByDay | BSTR | 期货当日盈亏 |
| ReferenceRiskRate | BSTR | 参考风险度 |
| TryExcessEquity | BSTR | 试算可用资金 |
| DynamicEquity | BSTR | 动态权益 |
| MarketPremium | BSTR | 市值权益 |
| OptionPremiumCoin | BSTR | 未冲销权利金市值 |
| StockReferenceMarket | BSTR | 股票参考市值 |

### 9.期货/期权 部位查询

a.部位查询指令

```text
{
  "Request":"POSITIONS",
  "SessionKey":"XXXXXXXXX",
    "AccountMask":"XXXXXXXXX",
    "QryIndex":"n"
}
```

b.资金查询回复

```text
{
  "Reply":"POSITIONS",
  "Success":"OK",
  "Positions":
    [
      {"BrokerID":"XXX",
        "Account":"XXX",
        "TransactDate":"n",
                "TransactTime":"n",
                "Quantity":"n",
                "SumLongQty":"n",
                "SumShortQty":"n",
        "QryIndex":"n",
       ...},
      {"BrokerID":"XXX",
       "Account":"XXX",
       "TransactDate":"n",
       "TransactTime":"n",
                "Quantity":"n",
                "SumLongQty":"n",
                "SumShortQty":"n",
        "QryIndex":"n",
       ...}
   ]   
}
```

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
      <td style="text-align:left">BrokerID</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5238;&#x5546;</td>
    </tr>
    <tr>
      <td style="text-align:left">BrokerName</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5238;&#x5546;&#x540D;&#x79F0;</td>
    </tr>
    <tr>
      <td style="text-align:left">Account</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x8D26;&#x53F7;</td>
    </tr>
    <tr>
      <td style="text-align:left">AccountName</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x8D26;&#x53F7;&#x540D;&#x79F0;</td>
    </tr>
    <tr>
      <td style="text-align:left">TransactDate</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x66F4;&#x65B0;&#x65E5;&#x671F; UTC+0</td>
    </tr>
    <tr>
      <td style="text-align:left">TransactTime</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x66F4;&#x65B0;&#x65F6;&#x95F4; UTC+0</td>
    </tr>
    <tr>
      <td style="text-align:left">Symbol</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5546;&#x54C1;TCore&#x4EE3;&#x7801;</td>
    </tr>
    <tr>
      <td style="text-align:left">Side</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x4E70;&#x5356;&#x65B9;&#x5411; 1Buy 2Sell</p>
        <p>(&#x590D;&#x5F0F;&#x5355;&#x4E3A;&#x6574;&#x4F53;&#x4E70;&#x5356;&#x65B9;&#x5411;)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Quantity</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x51C0;&#x4ED3;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">AvgPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x6210;&#x672C;&#x5747;&#x4EF7;</td>
    </tr>
    <tr>
      <td style="text-align:left">OpenPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5F00;&#x4ED3;&#x4EF7;</td>
    </tr>
    <tr>
      <td style="text-align:left">CurrencyToSystem</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7CFB;&#x7EDF;&#x5E01;&#x522B;</td>
    </tr>
    <tr>
      <td style="text-align:left">Covered</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x5907;&#x5151;&#x4ED3;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">SumLongQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x5934; &#x6301;&#x4ED3;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">SumShortQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x6301;&#x4ED3;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">TodayLongQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x5934; &#x4ECA;&#x65E5;&#x6301;&#x4ED3;</td>
    </tr>
    <tr>
      <td style="text-align:left">TodayShortQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x4ECA;&#x65E5;&#x6301;&#x4ED3;</td>
    </tr>
    <tr>
      <td style="text-align:left">YdLongQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x5934; &#x6628;&#x65E5;&#x6301;&#x4ED3;</td>
    </tr>
    <tr>
      <td style="text-align:left">YdShortQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x6628;&#x65E5;&#x6301;&#x4ED3;</td>
    </tr>
    <tr>
      <td style="text-align:left">LongAvgPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x5934; &#x6210;&#x672C;&#x5747;&#x4EF7;</td>
    </tr>
    <tr>
      <td style="text-align:left">ShortAvgPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x6210;&#x672C;&#x5747;&#x4EF7;</td>
    </tr>
    <tr>
      <td style="text-align:left">LongOpenPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x5934; &#x5F00;&#x4ED3;&#x5747;&#x4EF7;</td>
    </tr>
    <tr>
      <td style="text-align:left">ShortOpenPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x5F00;&#x4ED3;&#x5747;&#x4EF7;</td>
    </tr>
    <tr>
      <td style="text-align:left">WorkingLong</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x5934; &#x59D4;&#x6258;&#x4E2D;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">WorkingShort</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x59D4;&#x6258;&#x4E2D;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">TdBuyQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4ECA;&#x4E70;&#x5165;</td>
    </tr>
    <tr>
      <td style="text-align:left">TdSellQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4ECA;&#x5356;&#x51FA;</td>
    </tr>
    <tr>
      <td style="text-align:left">TdTotalQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4ECA;&#x6210;&#x4EA4;</td>
    </tr>
    <tr>
      <td style="text-align:left">LongFrozen</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x5934; &#x51BB;&#x7ED3;</td>
    </tr>
    <tr>
      <td style="text-align:left">ShortFrozen</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x51BB;&#x7ED3;</td>
    </tr>
    <tr>
      <td style="text-align:left">Lock_ExecFrozen</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x9501;&#x5238;/&#x6267;&#x884C;&#x51BB;&#x7ED3;</td>
    </tr>
    <tr>
      <td style="text-align:left">LongAvailable</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x5934; &#x53EF;&#x5E73;&#x4ED3;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">ShortAvailable</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x5934; &#x53EF;&#x5E73;&#x4ED3;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">TdBuyAvgPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4ECA;&#x4E70;&#x6210;&#x5747;</td>
    </tr>
    <tr>
      <td style="text-align:left">TdSellAvgPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4ECA;&#x5356;&#x6210;&#x5747;</td>
    </tr>
    <tr>
      <td style="text-align:left">TdNetAvgPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4ECA;&#x51C0;&#x6210;&#x5747;</td>
    </tr>
    <tr>
      <td style="text-align:left">FloatProfitByDate</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x9010;&#x65E5;&#x6D6E;&#x52A8;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">FloatProfitByTrade</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x9010;&#x7B14;&#x6D6E;&#x52A8;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">LongFloatProfitByDate</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x9010;&#x65E5;&#x6D6E;&#x52A8;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">ShortFloatProfitByDate</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x9010;&#x65E5;&#x6D6E;&#x52A8;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">LongFloatProfitByTrade</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x591A;&#x9010;&#x7B14;&#x6D6E;&#x52A8;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">ShortFloatProfitByTrade</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x7A7A;&#x9010;&#x7B14;&#x6D6E;&#x52A8;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">CloseProfitByTrade</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x9010;&#x7B14;&#x5BF9;&#x51B2;&#x5E73;&#x4ED3;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">CloseProfitByDate</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x9010;&#x65E5;&#x76EF;&#x5E02;&#x5E73;&#x4ED3;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">TodayProfit</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5F53;&#x65E5;&#x76C8;&#x4E8F;</td>
    </tr>
    <tr>
      <td style="text-align:left">MarketPrice</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5E02;&#x503C;&#x6743;&#x76CA;</td>
    </tr>
    <tr>
      <td style="text-align:left">OpenCost</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5F00;&#x4ED3;&#x6210;&#x672C;</td>
    </tr>
    <tr>
      <td style="text-align:left">PositionCost</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x6301;&#x4ED3;&#x6210;&#x672C;</td>
    </tr>
    <tr>
      <td style="text-align:left">ExchangeRate</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x4EA4;&#x6613;&#x6240;&#x6C47;&#x7387;</td>
    </tr>
    <tr>
      <td style="text-align:left">YdOrgNetQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5F53;&#x65E5;&#x8D77;&#x59CB; &#x6628;&#x51C0;&#x4ED3;&#x6570;&#x91CF;(&#x6B63;&#x8D1F;)</td>
    </tr>
    <tr>
      <td style="text-align:left">YdOrgLongQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5F53;&#x65E5;&#x8D77;&#x59CB; &#x6628;&#x591A;&#x4ED3;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">YdOrgShortQty</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5F53;&#x65E5;&#x8D77;&#x59CB; &#x6628;&#x7A7A;&#x4ED3;&#x6570;&#x91CF;</td>
    </tr>
  </tbody>
</table>### 10.PING/PONG

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

## 二、Publish-Subscribe

  Client **透过 pub/sub 联机，接收资金账号变动及主推回报**

### 1.资金账号主推

```text
{
    "DataType":"ACCOUNTS",
    "Accounts":
  [
    {

      "BrokerID":"123456",
      "Account":"XXX",
      "UserName":"XXX",
             "AccountName":"XXX",
       ...
    },
    {

      "BrokerID":"123456",
      "Account":"XXX",
      "UserName":"XXX",
             "AccountName":"XXX",
       ...
    }
  ]
}
```

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
      <td style="text-align:left">BrokerID</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5238;&#x5546; (&#x5BF9;&#x5E94;&#x4E0B;&#x5355;&#x4E0E;&#x56DE;&#x62A5;&#x5B57;&#x6BB5;)</td>
    </tr>
    <tr>
      <td style="text-align:left">Account</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x8D26;&#x53F7; (&#x5BF9;&#x5E94;&#x4E0B;&#x5355;&#x4E0E;&#x56DE;&#x62A5;&#x5B57;&#x6BB5;)</td>
    </tr>
    <tr>
      <td style="text-align:left">UserName</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x767B;&#x5165;&#x8D26;&#x53F7;&#x540D;&#x79F0;</td>
    </tr>
    <tr>
      <td style="text-align:left">AccountName</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x8D26;&#x53F7;&#x540D;&#x79F0;</td>
    </tr>
    <tr>
      <td style="text-align:left">AccountMask</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">BrokerID-Account</td>
    </tr>
    <tr>
      <td style="text-align:left">BrokerName</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x5238;&#x5546;&#x540D;&#x79F0;</td>
    </tr>
    <tr>
      <td style="text-align:left">Status</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">
        <p>&#x8D26;&#x53F7;&#x72B6;&#x6001;(0:&#x5C1A;&#x672A;&#x767B;&#x5165; 1:&#x767B;&#x5165;&#x4E2D;</p>
        <p>2:&#x767B;&#x5165;&#x5B8C;&#x6210;)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">AccountType</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">S/F/O</td>
    </tr>
    <tr>
      <td style="text-align:left">OrderExchange</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x53EF;&#x4E0B;&#x5355;&#x4EA4;&#x6613;&#x6240;</td>
    </tr>
    <tr>
      <td style="text-align:left">Level</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">ETF&#x671F;&#x6743;, &#x4F7F;&#x7528;&#x8005;&#x7EA7;&#x522B;</td>
    </tr>
    <tr>
      <td style="text-align:left">AccountReleated</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">&#x8BC1;&#x5238;/&#x671F;&#x8D27;&#x8D26;&#x53F7; &#x8FDE;&#x7ED3;ID</td>
    </tr>
    <tr>
      <td style="text-align:left">Region</td>
      <td style="text-align:left">BSTR</td>
      <td style="text-align:left">1:CN 2:TW 4:OB</td>
    </tr>
  </tbody>
</table>### 2.回报主推

```text
{
    "DataType":"EXECUTIONREPORT",
  "Report":
  {
    "ReportID":"123456",
    "Account":"XXX",
    "BrokerID":"XXX",
         "Symbol":"XXX",
         "Side":"n",
     ...
  }
}
```

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
      <td style="text-align:left">ReportID</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">TCore&#x56DE;&#x62A5;&#x7F16;&#x53F7;</td>
    </tr>
    <tr>
      <td style="text-align:left">Account</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x8D26;&#x53F7; (&#x5BF9;&#x5E94;&#x4E0B;&#x5355;&#x4E0E;&#x56DE;&#x62A5;&#x5B57;&#x6BB5;)</td>
    </tr>
    <tr>
      <td style="text-align:left">BrokerID</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5238;&#x5546;</td>
    </tr>
    <tr>
      <td style="text-align:left">Symbol</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5546;&#x54C1;TCore&#x4EE3;&#x7801;</td>
    </tr>
    <tr>
      <td style="text-align:left">Side</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x4E70;&#x5356;&#x522B; 1:Buy 2:Sell</p>
        <p>(&#x590D;&#x5F0F;&#x5355;&#x4E3A;&#x6574;&#x4F53;&#x4E70;&#x5356;&#x65B9;&#x5411;)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">OrderID</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x59D4;&#x6258;&#x4E66;&#x53F7;</td>
    </tr>
    <tr>
      <td style="text-align:left">OriginalQty</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">new order&#x65F6;&#x5019;&#x7684;&#x59D4;&#x6258;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">OrderQty</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5B9E;&#x9645;&#x59D4;&#x6258;&#x6570;&#x91CF;=&#x6210;&#x4EA4;&#x6570;&#x91CF;+&#x5269;&#x4F59;&#x6709;&#x6548;&#x59D4;&#x6258;&#x53E3;&#x6570;</td>
    </tr>
    <tr>
      <td style="text-align:left">Price</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x59D4;&#x6258;&#x4EF7;&#x683C;</td>
    </tr>
    <tr>
      <td style="text-align:left">StopPrice</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x505C;&#x635F;&#x4EF7;</td>
    </tr>
    <tr>
      <td style="text-align:left">OrderType</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>0 : None Unknown</p>
        <p>1 : Market order</p>
        <p>2 : Limit order</p>
        <p>3 : Stop order</p>
        <p>4 : Stop limit order</p>
        <p>5 : Trailing Stop</p>
        <p>6 : Trailing StopLimit</p>
        <p>7 : Market if Touched Order</p>
        <p>8 : Limit if Touched Order</p>
        <p>9 : Trailing Limit (TLMT)</p>
        <p>10 : &#x5BF9;&#x65B9;&#x4EF7;(HIT)</p>
        <p>11 : &#x672C;&#x65B9;&#x4EF7;(JOIN)</p>
        <p>12 : DOM-Triggered Stop ( DTS )</p>
        <p>13 : DOM-Triggered Stop Limit ( DTSL )</p>
        <p>20 : &#x6700;&#x4F18;&#x4EF7; (BST)</p>
        <p>21 : &#x6700;&#x4F18;&#x4EF7;&#x8F6C;&#x9650;&#x4EF7; (BSTL)</p>
        <p>22 : &#x4E94;&#x6863;&#x5E02;&#x4EF7; (5LvlMKT)</p>
        <p>23 : &#x4E94;&#x6863;&#x5E02;&#x4EF7;&#x8F6C;&#x9650;&#x4EF7; (5LvlMTL)</p>
        <p>24 : &#x5E02;&#x4EF7;&#x8F6C;&#x9650;&#x4EF7; (MTL)</p>
        <p>25: &#x4E00;&#x5B9A;&#x8303;&#x56F4;&#x5E02;&#x4EF7;(MWP)</p>
        <p>201:&#x9501;&#x5238;</p>
        <p>202:&#x89E3;&#x9501;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TradeType</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x8BC1;&#x5238;</p>
        <p>0:Normal</p>
        <p>1:&#x878D;&#x8D44;</p>
        <p>2.&#x878D;&#x5238;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">ExCode</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x8BC1;&#x5238;</p>
        <p>0:&#x73B0;&#x80A1;</p>
        <p>1:&#x96F6;&#x80A1;</p>
        <p>2:&#x76D8;&#x540E;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">PositionEffect</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>0 : Open Open position</p>
        <p>1 : Close Close position</p>
        <p>2 : &#x5E73;&#x4ECA;</p>
        <p>3 : &#x5E73;&#x6628;</p>
        <p>4 : Auto Auto select Open/Cloe position</p>
        <p>10 : &#x5907;&#x5151;&#x5F00;&#x4ED3;</p>
        <p>11: &#x5907;&#x5151;&#x5E73;&#x4ED3;</p>
        <p>201:&#x9501;&#x5238;</p>
        <p>202:&#x89E3;&#x9501;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">DayTrade</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>0 : None</p>
        <p>1 : &#x5F53;&#x51B2;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TimeInForce</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>0 : None</p>
        <p>1 : ROD Day order</p>
        <p>2 : IOC Immediate or Cancel</p>
        <p>3 : FOK Fill or Kill</p>
        <p>4 : GTC Good-Till-Cancel</p>
        <p>5 : GTD Good-Till-Date</p>
        <p>6 : OPG &#x76D8;&#x524D;&#x9884;&#x7EA6;&#x5355;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TrailingAmount</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">TrailingAmount(Last, Bid, Ask)</td>
    </tr>
    <tr>
      <td style="text-align:left">TouchCondition</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x89E6;&#x53D1;&#x6761;&#x4EF6;</p>
        <p>ex. Last &gt;= TouchPrice</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Group</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x683C;&#x5F0F;&#x4E3A;Type : GroupID</p>
        <p>Type = Normal, OCO, OTO, OTOCO</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">UserKey1</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5BF9;&#x5E94;&#x59D4;&#x6258;&#x53C2;&#x6570;&#x7684;UserKey1&#x5B57;&#x6BB5;&#x6570;&#x636E;</td>
    </tr>
    <tr>
      <td style="text-align:left">CumQty</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5DF2;&#x6210;&#x4EA4;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">LeavesQty</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x59D4;&#x6258;&#x5269;&#x4F59;&#x6709;&#x6548;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">ExecType</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x59D4;&#x6258;&#x6267;&#x884C;&#x72B6;&#x6001;</p>
        <p>-10: &#x8BF7;UI&#x9690;&#x85CF;&#x8BE5;&#x7B14;&#x56DE;&#x62A5; &#x4E0D;&#x8981;&#x663E;&#x793A;</p>
        <p>(&#x539F;&#x9884;&#x57CB;&#x5355;&#x56DE;&#x62A5; &#x4E0E; &#x9001;&#x81F3;&#x4EA4;&#x6613;&#x6240;&#x4E3B;&#x63A8;&#x56DE;&#x62A5;
          &#x65E0;&#x6CD5;&#x5408;&#x5355; &#x6240;&#x4EE5;&#x9690;&#x85CF;&#x8BE5;&#x7B14;&#x539F;&#x9884;&#x57CB;&#x5355;&#x56DE;&#x62A5;)</p>
        <p>0:&#x59D4;&#x6258;&#x6210;&#x529F;(&#x5305;&#x542B;&#x6539;&#x4EF7;&#x6539;&#x91CF;)</p>
        <p>1:&#x90E8;&#x4EFD;&#x59D4;&#x6258;&#x6210;&#x529F;&#x5176;&#x4F59;&#x5904;&#x7406;&#x4E2D;</p>
        <p>2:&#x90E8;&#x4EFD;&#x59D4;&#x6258;&#x6210;&#x529F;&#x5176;&#x4F59;&#x9519;&#x8BEF;</p>
        <p>3:&#x5168;&#x90E8;&#x6210;&#x4EA4;</p>
        <p>4:&#x90E8;&#x4EFD;&#x6210;&#x4EA4;&#x5176;&#x4F59;&#x59D4;&#x6258;&#x5904;&#x7406;&#x4E2D;</p>
        <p>5:&#x90E8;&#x4EFD;&#x6210;&#x4EA4;&#x5176;&#x4F59;&#x5220;&#x5355;</p>
        <p>6:&#x90E8;&#x5206;&#x6210;&#x4EA4;&#x5C1A;&#x6709;&#x6709;&#x6548;&#x5355;</p>
        <p>7.&#x90E8;&#x4EFD;&#x6210;&#x4EA4;&#x5176;&#x4F59;&#x9519;&#x8BEF;</p>
        <p>8:&#x5B8C;&#x5168;&#x5220;&#x5355;&#x6210;&#x529F;</p>
        <p>9.&#x90E8;&#x4EFD;&#x5220;&#x5355;&#x6210;&#x529F;</p>
        <p>10:&#x59D4;&#x6258;&#x5931;&#x8D25;</p>
        <p>11:&#x59D4;&#x6258;&#x5904;&#x7406;&#x4E2D;</p>
        <p>12:&#x5220;&#x6539;&#x5355;&#x9519;&#x8BEF;</p>
        <p>13:&#x6D17;&#x4EF7;&#x4E2D;</p>
        <p>14:ITS&#x4E3B;&#x673A;&#x5DF2;&#x6536;&#x5355;(&#x589E;&#x52A0;&#x4E2D;)</p>
        <p>15:&#x7B49;&#x5F85;&#x4E2D;(&#x4E0D;&#x53EF;&#x64A4;&#x5355;)</p>
        <p>16:&#x6D17;&#x4EF7;&#x5355;, &#x89E6;&#x4EF7;&#x9001;&#x5355;</p>
        <p>17:&#x9501;&#x5238;&#x6210;&#x529F;</p>
        <p>18:&#x9501;&#x5238;&#x5931;&#x8D25;</p>
        <p>19:&#x9501;&#x5238;&#x5DF2;&#x63D0;&#x4EA4;</p>
        <p>20:&#x7B49;&#x5F85;&#x4E2D;(&#x53EF;&#x64A4;&#x5355;)</p>
        <p>21:&#x4E0B;&#x5355;&#x9501;&#x5B9A; Block</p>
        <p>22:&#x64A4;&#x5355;&#x4F20;&#x9001;&#x4E2D;</p>
        <p>23:&#x9884;&#x7EA6;&#x5355;</p>
        <p>31:&#x6D17;&#x4EF7;&#x6682;&#x505C;&#x4E2D;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">ErrorCode</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>ExecType=10 or ExecType=12 or ExecType=18&#x9519;&#x8BEF;&#x5355;&#x65F6;
          &#x8BF7;&#x6293;&#x53D6;ErrorCode</p>
        <p>ErrorCode&lt;0 &#x8868;&#x793A;&#x6709;&#x9519;&#x8BEF;</p>
        <p>&#x82E5;ErrorCode=-777&#x4E3A;Undefine Error &#x9519;&#x8BEF;&#x5185;&#x5BB9;
          &#x8BF7;&#x770B;ExecTypeText</p>
        <p>&#x5176;&#x4ED6;ErrorCode&#x5B9A;&#x4E49;&#x5982;&#x540E;[12.&#x56DE;&#x62A5;ErrorCode&#x5B9A;&#x4E49;]</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">TriggeredPrice</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x6D17;&#x4EF7;&#x5355; &#x89E6;&#x53D1;&#x4EF7;&#x683C;</td>
    </tr>
    <tr>
      <td style="text-align:left">TransactDate</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x66F4;&#x65B0;&#x65E5;&#x671F;</td>
    </tr>
    <tr>
      <td style="text-align:left">TransactTime</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x66F4;&#x65B0;&#x65F6;&#x95F4; (UpdateTime)</td>
    </tr>
    <tr>
      <td style="text-align:left">AvgPrice</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x6210;&#x4EA4;&#x5747;&#x4EF7;</td>
    </tr>
    <tr>
      <td style="text-align:left">ExecTypeText</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x59D4;&#x6258;&#x6267;&#x884C;&#x72B6;&#x6001;&#x63CF;&#x8FF0;[server&#x9519;&#x8BEF;&#x8BAF;&#x606F;]</td>
    </tr>
    <tr>
      <td style="text-align:left">FilledOrdersCount</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x6210;&#x4EA4;&#x56DE;&#x62A5;&#x6570;&#x91CF;</td>
    </tr>
    <tr>
      <td style="text-align:left">IsRestoreData</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>0 : &#x5B9E;&#x65F6;&#x56DE;&#x62A5;</p>
        <p>1 : &#x56DE;&#x8865;&#x56DE;&#x62A5;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">HedgeFlag</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">
        <p>&#x6295;&#x673A;&#x5957;&#x4FDD;&#x6807;&#x5FD7;</p>
        <p>(1:&#x6295;&#x673A; 2:&#x5957;&#x5229; 3:&#x5957;&#x4FDD; 4:&#x5907;&#x5151;)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">OrgSource</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x4E0B;&#x5355;&#x6765;&#x6E90;&#x522B;</td>
    </tr>
    <tr>
      <td style="text-align:left">TradeDate</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x4EA4;&#x6613;&#x65E5;</td>
    </tr>
    <tr>
      <td style="text-align:left">SetPRIADJ</td>
      <td style="text-align:left">string</td>
      <td style="text-align:left">&#x5C65;&#x7EA6;&#x4EF7;&#x8C03;&#x6574;(&#x4E2D;&#x56FD;ETF&#x671F;&#x6743;&#x7B2C;&#x51E0;&#x6B21;&#x8C03;&#x6574;&#x5408;&#x7EA6;&#x5C65;&#x7EA6;&#x4EF7;)</td>
    </tr>
  </tbody>
</table>### 3.DaVinci讯号

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

