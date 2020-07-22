#### Arthas排查

注意：

```
<groupId>com.idfconnect.devtools</groupId>
<artifactId>idfc-proguard-maven-plugin</artifactId>
<version>1.0.1</version>
功能：混淆(Obfuscate)：使用a，b，c，d这样简短而无意义的名称，对类、字段和方法进行重命名。
```

SOAM采用proguard导致代码重命名，无法线上直接追溯。



trace com.goldwind.scada.warn.business.WarnStyleBusiness extendUIWarnInfo

```java
[arthas@68670]$trace com.goldwind.scada.warn.business.WarnStyleBusiness extendUIWarnInfo
结果：
+---[0.001164ms] com.goldwind.scada.warn.UIWarnInfo:getSystemid() #84
+---[7.08E-4ms] com.goldwind.scada.warn.UIWarnInfo:getWfid() #85
+---[6.58E-4ms] com.goldwind.scada.warn.UIWarnInfo:getDeviceid() #86
+---[5.7E-4ms] com.goldwind.scada.warn.UIWarnInfo:getLevelid() #87
+---[5.63E-4ms] com.goldwind.scada.warn.UIWarnInfo:getTypeid() #88
+---[5.79E-4ms] com.goldwind.scada.warn.UIWarnInfo:getInfo() #89
+---[6.23E-4ms] com.goldwind.scada.warn.UIWarnInfo:getObjectType() #90
+---[6.1E-4ms] com.goldwind.scada.warn.UIWarnInfo:getIecval() #91
+---[min=4.17E-4ms,max=7.65E-4ms,total=0.002054ms,count=4] com.goldwind.scada.warn.logic.VoiceExtension:getNoid() #95
+---[min=0.019055ms,max=0.137117ms,total=0.197572ms,count=4] com.goldwind.datalogic.business.b:a() #93
`---[6.6E-4ms] com.goldwind.scada.warn.logic.VoiceExtension:copyValue() #97



[arthas@68670]$watch com.goldwind.datalogic.business.b a "{params,returnObj}" -x 2 "params.length==10"
结果：
    ts=2020-05-12 16:54:50; [cost=0.546171ms] result=@ArrayList[
        @Object[][
            @String[632523],
            @String[101],
            @String[632523620],
            @String[2],
            @String[7],
            @String[WTRF.PhV.Ra.F32.CA2],
            @String[1],
            @String[312.4],
            @Filtertype[Style],
            @String[8931],
        ],
        @Boolean[true],
    ]
```









```
[arthas@80800]$ trace com.goldwind.softadapter.dao.a.j b
Press Q or Ctrl+C to abort.
Affect(class-cnt:2 , method-cnt:23) cost in 574 ms.
`---ts=2020-05-12 17:04:37;thread_name=https-jsse-nio2-8102-exec-26;id=40;is_daemon=true;priority=5;TCCL=org.apache.catalina.loader.ParallelWebappClassLoader@3c141960
`---[2.79644ms] com.goldwind.softadapter.dao.a.a.i:b()
+---[min=0.00191ms,max=0.014402ms,total=0.016312ms,count=2] com.goldwind.softadapter.model.product.RunLogType:getTypeId() #4180
+---[0.004568ms] com.goldwind.softadapter.model.product.RunLogType:getSystemId() #4181
+---[min=0.001746ms,max=0.004059ms,total=0.005805ms,count=2] com.goldwind.softadapter.model.product.RunLogType:getDescrCn() #4181
+---[0.005222ms] com.goldwind.softadapter.model.product.RunLogType:getObjectCn() #4181
+---[0.004052ms] com.goldwind.softadapter.model.product.RunLogType:getObjectEn() #4181
+---[0.003464ms] com.goldwind.softadapter.model.product.RunLogType:getObjectType() #4182
+---[0.004117ms] com.goldwind.softadapter.model.product.RunLogType:getIsCustom() #4182
+---[0.004014ms] com.goldwind.softadapter.model.product.RunLogType:getLevelId() #4182
+---[0.004196ms] com.goldwind.softadapter.model.product.RunLogType:getIecSort() #4182
+---[0.003796ms] com.goldwind.softadapter.model.product.RunLogType:getModelcnf() #4182
+---[0.003497ms] com.goldwind.softadapter.model.product.RunLogType:getOrdernum() #4182
+---[0.004385ms] com.goldwind.softadapter.model.product.RunLogType:getIecflag() #4182
+---[2.280668ms] com.goldwind.softadapter.dao.a.a.i:a() #4184 [throws Exception]
`---[0.004805ms] throw:com.goldwind.dataaccess.exception.DataAsException() #234
```





监听TestController类testParam方法1000000次，并输入到test.log文件中

```
tt -t com.github.hashJang.test.TestController testParam -n 1000000 > test.log
```

| 表格字段  | 字段解释                                                     |
| --------- | ------------------------------------------------------------ |
| INDEX     | 时间片段记录编号，每一个编号代表着一次调用，后续tt还有很多命令都是基于此编号指定记录操作，非常重要。 |
| TIMESTAMP | 方法执行的本机时间，记录了这个时间片段所发生的本机时间       |
| COST(ms)  | 方法执行的耗时                                               |
| IS-RET    | 方法是否以正常返回的形式结束                                 |
| IS-EXP    | 方法是否以抛异常的形式结束                                   |
| OBJECT    | 执行对象的`hashCode()`，注意，曾经有人误认为是对象在JVM中的内存地址，但很遗憾他不是。但他能帮助你简单的标记当前执行方法的类实体 |
| CLASS     | 执行的类名                                                   |
| METHOD    | 执行的方法名                                                 |

   查看具体时间片

```
$ tt -i 1003
 INDEX            1003
 GMT-CREATE       2018-12-04 11:15:41
 COST(ms)         0.186073
 OBJECT           0x4b67cf4d
 CLASS            demo.MathGame
 METHOD           primeFactors
 IS-RETURN        false
 IS-EXCEPTION     true
 PARAMETERS[0]    @Integer[-564322413]
 THROW-EXCEPTION  java.lang.IllegalArgumentException: number is: -564322413, need >= 2
                      at demo.MathGame.primeFactors(MathGame.java:46)
                      at demo.MathGame.run(MathGame.java:24)
                      at demo.MathGame.main(MathGame.java:16)
 
Affect(row-cnt:1) cost in 11 ms.
```



通过-e，并且throwExp来打印出异常信息栈

```
watch com.github.hashJang.test.TestController testParam  -e -x 2 '{params[0],params[1].toString,throwExp}'
```





##### 告警登录排查

根据Bean的名称获取Spring管理的单例Bean，并进一步获取内容。

获取UserWarnManager的会话

```java
getstatic com.goldwind.scada.util.SpringUtils context 'getBean("userWarnManager").dataList.size()'
```



获取用户会话的数目

```java
getstatic com.goldwind.scada.ui.session.SessionListener sessionMap 'size()'
```



获取当前告警的数目

```java
getstatic com.goldwind.softadapter.memery.realtimemonitor.WarninfoMemery ALLWARN 'size()'
```



获取当前登录的所有用户

```java
getstatic com.goldwind.scada.ui.MonitorServer users 'ids'
```



修正后：用户告警存储在UserWarnCache缓存中

获取告警用户的数量

```java
getstatic com.goldwind.scada.warn.UserWarnCache userInitWarnMap 'size()'
```

获取指定用户告警数量

```java
getstatic com.goldwind.scada.warn.UserWarnCache userInitWarnMap 'get(302).size()'
```



获取

```
getstatic com.goldwind.scada.util.SpringUtils context 'getBean("userWarnManager").dataList.{#this.record.warnWrapper.frameMap.keySet()}' -x 2
```



获取软适配新告警的产生过程

```
watch com.goldwind.softadapter.memery.realtimemonitor.WarninfoMemery addNewWarn "params" -x 2
```



前端查看告警序列：

for(var i = 0;i<100 ;i++){console.log(store.getState().warnCurrentWarn.tableDataSource[i].sequence)}



排查性能：

查看系统启动时机，所有init的耗时

```
tt -t com.goldwind.scada.ui.warn.PreInitialize init
```







函数式表达式验证：

告警确认CurrentWarn.confirmWarn

sm java.util.function.BiFunction

trace com.goldwind.scada.dhs.warn.CurrentWarn$$Lambda$119/1610411772 apply













获取内存中的DataOrder:
