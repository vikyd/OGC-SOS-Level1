# GetObservationById

用于根据观测数据ID获取传感器的观测数据。


# 请求

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<sos:GetObservationById 
	xmlns:sos="http://www.opengis.net/sos/1.0" 
	xmlns:om="http://www.opengis.net/om/1.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="
	 http://www.opengis.net/sos/1.0
	 http://schemas.opengis.net/sos/1.0.0/sosAll.xsd" 
	service="SOS" version="1.0.0" srsName="EPSG:4326">
	
	<sos:ObservationId>o_3</sos:ObservationId>
	
	<sos:resultModel>om:Observation</sos:resultModel>
	
</sos:GetObservationById>
```


# 请求解释

**参数：ObservationId**

观测数据在SOS中的ID。

在52nSOS 3.x中，此ID仅表示一个传感器的一个观测属性在一个时刻的观测值。

而按照标准，此ID可以是任意逻辑集合的观测数据的唯一标识符。

**从何处可以获取此ID？**

此ID只能在InsertObservation后才能看到。
但一个ID对应的InsertObservation只有一次。InsertObservation之后不记得了怎么办？
按52nSOS 3.x，目前是没有办法的，只能从数据库表中寻找对应的字段项`observation_id`。

若是SOS相对成熟，则此ID有可能被精心处理，并像发破解key或新浪微博的短链接那样，
无约定规则，但确实能对应到你想要的数据。

**参数：resultModel**

结果返回的格式。
除`om:Observation`外，还可选`om:Measurement`等。


# 响应

响应遵循OM标准，参考GetObservation的响应。

也可直接在example中看示例。


# 响应解释

参考GetObservation。


# 谁来执行这个操作
用户或自动处理的程序。


# 注意的事
* 目前的52nSOS 3.x来说，Observation的ID可遇不可求，所以GetObservationById看起来有点不实用
，不过这也可能只是暂时的现象而已。



