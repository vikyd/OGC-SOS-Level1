# GetObservation
用于获取传感器的观测数据。

可根据传感器ID、观测属性ID、时间范围、空间范围进行获取。

GetObservation可以算是SOS中**最最重要**的操作，也是相对复杂的操作
（因为你需要考虑如何**按需**去`Get`观测数据）。

以下示例XML基于52nSOS 3.x(不再额外说明)，因为不同SOS实现可能略有差异。


# 请求（完整版）

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<GetObservation 
	xmlns="http://www.opengis.net/sos/1.0"
	xmlns:ows="http://www.opengis.net/ows/1.1"
	xmlns:gml="http://www.opengis.net/gml"
	xmlns:ogc="http://www.opengis.net/ogc"
	xmlns:om="http://www.opengis.net/om/1.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
	 http://www.opengis.net/sos/1.0
	 http://schemas.opengis.net/sos/1.0.0/sosGetObservation.xsd"
	service="SOS" version="1.0.0">
	
	<offering>ABC</offering>
	
	<eventTime>
		<ogc:TM_Equals>
			<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
			<gml:TimePeriod>
				<gml:beginPosition>2011-01-01T00:01:00.000+08:00</gml:beginPosition>
				<gml:endPosition>2011-01-01T00:02:00.000+08:00</gml:endPosition>
			</gml:TimePeriod>
		</ogc:TM_Equals>
	</eventTime>
	
	<procedure>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</procedure>
	<procedure>urn:ABC:object:feature:Platform:Station:Weather:sta-a002</procedure>
	
	<observedProperty>urn:ogc:def:phenomenon:OGC:1.0.30:position</observedProperty>
	<observedProperty>urn:ABC:def:phenomenon:ABC:1.0.0:Temperature</observedProperty>    
	<observedProperty>urn:ABC:def:phenomenon:ABC:1.0.0:Speed</observedProperty>
	<observedProperty>urn:ABC:def:phenomenon:ABC:1.0.0:Direction</observedProperty>
	
	<featureOfInterest>
		<ogc:Intersects>
			<ogc:PropertyName>urn:ogc:data:location</ogc:PropertyName>
			<gml:Polygon srsName="urn:ogc:def:crs:EPSG::4326">
				<gml:exterior>
					<gml:LinearRing>
						<gml:posList>
							29.985941 113.69685 31.364 113.69685 31.364 115.07628 29.985941 115.07628 29.985941 113.69685
						</gml:posList>
					</gml:LinearRing>
				</gml:exterior>
			</gml:Polygon>
		</ogc:Intersects>
	</featureOfInterest>
    	
	<responseFormat>text/xml;subtype="om/1.0.0"</responseFormat>

</GetObservation>
```

# 请求解释
**参数：offering（1）**

这是一个SOS强制要有的参数。
在52nSOS 3.x中，offering是观测属性中的一部分定义内容，可以理解为**机构**，
这里`ABC`就是RegisterSensor时，outputs中观测属性的一部分定义。

一般一个SOS中应只使用一个offering（非官方）。盖因offering在SOS 1.0标准中有点怪怪的，甚至显得有点多余。

因此，当所有观测属性中的offering都是一样时，GetObservation时才会比较方便。

``` xml
<offering>ABC</offering>
```

**参数：时间过滤（0 - n）**

指定所需观测数据的时间范围。此参数引用了OGC Filter和OGC GML标准。

这是一个可选参数，可以没有，或有多个：
* 没有指定此参数：表示所有时间的观测数据都返回（一般会较慢）。
* 指定了此参数：表示仅返回所指定的时间范围的观测数据。

主要有两个主要注意的地方：
* 时间拓扑（只想到`拓扑`这个词了），如`TM_Equals`、`TM_After`。
* 时间值，如`2011-01-01T00:01:00.000+08:00`，遵循[ISO 8601]时间格式与交换标准。
  * `2011-01-01T00:01:00.000+08:00`表示：
    * 时区：东八区`+08:00`
    * 时间：2011年01月01日00时01分00秒000毫秒

例如，本例中
* `ogc:TM_Equals`表示包含时间的端点（可替换成`TM_Before`，`TM_After`，`TM_During`，`TM_Begins`等），
* `gml:TimePeriod`表示时间段（引用gml的_TimeObject元素，可替换成时间点`TimeInstant`等），
* `gml:beginPosition`表示时间的起始点，
* `gml:endPosition`表示时间的结束点。

表示`等于`（含两端）从`2011-01-01T00:01:00.000+08:00`到`2011-01-01T00:02:00.000+08:00`的时间范围：
``` xml
<eventTime>
	<ogc:TM_Equals>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimePeriod>
			<gml:beginPosition>2011-01-01T00:01:00.000+08:00</gml:beginPosition>
			<gml:endPosition>2011-01-01T00:02:00.000+08:00</gml:endPosition>
		</gml:TimePeriod>
	</ogc:TM_Equals>
</eventTime>
```

表示`等于`（不含两端）从`2011-01-01T00:01:00.000+08:00`到`2011-01-01T00:02:00.000+08:00`的时间范围：

``` xml
<eventTime>
	<ogc:TM_During>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimePeriod>
			<gml:beginPosition>2011-01-01T00:01:00.000+08:00</gml:beginPosition>
			<gml:endPosition>2011-01-01T00:02:00.000+08:00</gml:endPosition>
		</gml:TimePeriod>
	</ogc:TM_Equals>
</eventTime>
```

表示在`2011-01-01T00:01:00.000+08:00`时间点`之前`（不含`此时间`）的时间范围：

``` xml
<eventTime>
	<ogc:TM_Before>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimeInstant>
			<gml:timePosition>2011-01-01T00:01:00.000+08:00</gml:timePosition>
		</gml:TimeInstant>
	</ogc:TM_Before>
</eventTime>
```

表示在`2011-01-01T00:01:00.000+08:00`时间点`之后`（不含`此时间`）的时间范围：

``` xml
<eventTime>
	<ogc:TM_After>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimeInstant>
			<gml:timePosition>2011-01-01T00:01:00.000+08:00</gml:timePosition>
		</gml:TimeInstant>
	</ogc:TM_After>
</eventTime>
```

表示在`2011-01-01T00:01:00.000+08:00` `此刻`的时间范围：

``` xml
<eventTime>
	<ogc:TM_Equals>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimeInstant>
			<gml:timePosition>2011-01-01T00:01:00.000+08:00</gml:timePosition>
		</gml:TimeInstant>
	</ogc:TM_Equals>
</eventTime>
```

表示获取最新值：

分两种情况理解（52nSOS 3.x中可设置配置）：
* 每个传感器分别的最新值（每个传感器的最新值时刻可能会不一样）
* 所有传感器的最新值那1个时刻的最新值（就1个总的最新时刻不管是那个传感器，有就有，没有就不返回）

``` xml
<eventTime>
	<ogc:TM_Equals>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimeInstant>
			<gml:timePosition>latest</gml:timePosition>
		</gml:TimeInstant>
	</ogc:TM_Equals>
</eventTime>
```

表示获取最旧值：

``` xml
<eventTime>
	<ogc:TM_Equals>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimeInstant>
			<gml:timePosition>getFirst</gml:timePosition>
		</gml:TimeInstant>
	</ogc:TM_Equals>
</eventTime>
```

表示按`多个时间`的并集的时间范围（一般较少这么使用）：

``` xml
<eventTime>
	<ogc:TM_Equals>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimePeriod xsi:type="gml:TimePeriodType">
			<gml:beginPosition>2011-01-01T00:00:59.000+08:00</gml:beginPosition>
			<gml:endPosition>2011-01-01T00:02:01.000+08:00</gml:endPosition>
		</gml:TimePeriod>
	</ogc:TM_Equals>
</eventTime>
<eventTime>
	<ogc:TM_Equals>
		<ogc:PropertyName>om:samplingTime</ogc:PropertyName>
		<gml:TimePeriod xsi:type="gml:TimePeriodType">
			<gml:beginPosition>2011-01-01T00:02:59.000+08:00</gml:beginPosition>
			<gml:endPosition>2011-01-01T00:04:01.000+08:00</gml:endPosition>
		</gml:TimePeriod>
	</ogc:TM_Equals>
</eventTime>
```

更详细的时间过滤方式可参考[OGC Filter]和[OGC GML]`_TimeObject`标准。
不过不是每个SOS都支持所有过滤方式，使用前需注意，
GetCapabilities中的Filter_Capabilities Section可以查看此SOS支持何种时间过滤方式。


**参数：传感器（0 - n）**

这是一个可选参数：
* 没有指定此参数：表示所有传感器的观测数据都返回（一般会较慢）。
* 指定了此参数：表示仅返回所指定的传感器的观测数据。

这里填写传感器注册时的ID（即`urn:ogc:def:identifier:OGC:uniqueID`的值）。

需解释的地方：
* 若存在传感器A，不存在传感器B，则依然能返回A的观测数据，A不会因为B不存在而不返回任何数据。

``` xml
<procedure>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</procedure>
<procedure>urn:ABC:object:feature:Platform:Station:Weather:sta-a002</procedure>
```

**观测属性（1 - n）**

这是一个SOS强制要有的参数。
可有1个或多个，表示所需的观测属性的数据。

需解释的地方：
* 若传感器A有观测属性`Temperature`没有`Speed`，则会返回传感器A的`Temperature`的观测数据，
不会因为有了`Speed`而导致不返回任何数据。

``` xml
<observedProperty>urn:ogc:def:phenomenon:OGC:1.0.30:position</observedProperty>
<observedProperty>urn:ABC:def:phenomenon:ABC:1.0.0:Temperature</observedProperty>    
<observedProperty>urn:ABC:def:phenomenon:ABC:1.0.0:Speed</observedProperty>
<observedProperty>urn:ABC:def:phenomenon:ABC:1.0.0:Direction</observedProperty>
```

**参数：空间过滤（0 - 1）**

这是一个可选参数：
* 没有指定此参数：表示符合条件的所有空间范围的观测数据都返回。
* 指定了此参数：表示仅返回所指定的空间范围的观测数据。

主要有3个主要注意的地方：
* 空间拓扑（只想到`拓扑`这个词了），如`Intersects`、`Overlaps`等。
* 空间值，如gml的点、线、面（gml中一般是**先纬度后经度**）。
* 过滤的空间对象是谁：
  * OM的`featureOfInterest`：一般指观测位置。
    * 52nSOS 3.x中，应使用`<ogc:PropertyName>urn:ogc:data:location</ogc:PropertyName>`。常用于原位传感器。
  * OM的`result`的`空间观测值`：指观测值为空间值的时候，如GPS的观测值可以是经纬度的点。
    * 52nSOS 3.x中，应使用`<ogc:PropertyName>urn:ogc:data:samplingGeometry</ogc:PropertyName>`。常用于移动传感器。


表示空间坐标系统是`urn:ogc:def:crs:EPSG::4326`，且与此`多边形（Polygon）` `相交（Intersect）`的
（面向`观测位置featureOfInterest`）空间范围
（这应算是最常用的空间过滤方式了）：

``` xml
<featureOfInterest>
	<ogc:Intersects>
		<ogc:PropertyName>urn:ogc:data:location</ogc:PropertyName>
		<gml:Polygon srsName="urn:ogc:def:crs:EPSG::4326">
			<gml:exterior>
				<gml:LinearRing>
					<gml:posList>
						29.985941 113.69685 31.364 113.69685 31.364 115.07628 29.985941 115.07628 29.985941 113.69685
					</gml:posList>
				</gml:LinearRing>
			</gml:exterior>
		</gml:Polygon>
	</ogc:Intersects>
</featureOfInterest>
```


表示空间坐标系统是`urn:ogc:def:crs:EPSG::4326`，且与此`多边形（Polygon）` `相交（Intersect）`的
（面向`OM result中空间观测值`）空间范围
（这应算是最常用的空间过滤方式了）：

``` xml
<featureOfInterest>
	<ogc:Intersects>
		<ogc:PropertyName>urn:ogc:data:samplingGeometry</ogc:PropertyName>
		<gml:Polygon srsName="urn:ogc:def:crs:EPSG::4326">
			<gml:exterior>
				<gml:LinearRing>
					<gml:posList>
						29.985941 113.69685 31.364 113.69685 31.364 115.07628 29.985941 115.07628 29.985941 113.69685
					</gml:posList>
				</gml:LinearRing>
			</gml:exterior>
		</gml:Polygon>
	</ogc:Intersects>
</featureOfInterest>
```

面 在上面例子Polygon已有描述，也可参考[GML 例子]，以下列出较常用的一些：

点：
``` xml
<gml:Point srsName="urn:ogc:def:crs:EPSG::4326">
	<gml:pos>30.535296 114.369321</gml:pos>
</gml:Point>
```

线：
``` xml
<gml:LineString srsName="urn:ogc:def:crs:EPSG::4326">
	<gml:pos>30.535296 113.369321</gml:pos>
	<gml:pos>30.535300 114.369355</gml:pos>
	<gml:pos>30.535400 115.369555</gml:pos>			
</gml:LineString>
```

包围盒`BBOX`：
``` xml
<featureOfInterest>
	<ogc:BBOX>
		<ogc:PropertyName>urn:ogc:data:location</ogc:PropertyName>
		<gml:Envelope srsName="urn:ogc:def:crs:EPSG::4326">
			<gml:lowerCorner>0.0 0.0</gml:lowerCorner>
			<gml:upperCorner>32.0 116.0</gml:upperCorner>
		</gml:Envelope>
	</ogc:BBOX>
</featureOfInterest>
```


# 响应
GetObservation的响应遵循OM标准。

详细可参考example中的例子。

若无观测数据，则有可能会是返回如下信息，表示无数据：
``` xml
<om:ObservationCollection 
	xmlns:om="http://www.opengis.net/om/1.0" 
	xmlns:gml="http://www.opengis.net/gml" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:xlink="http://www.w3.org/1999/xlink" 
	xsi:schemaLocation="
	 http://www.opengis.net/om/1.0
	 http://schemas.opengis.net/om/1.0.0/extensions/observationSpecialization_constraint.xsd
	 http://www.opengis.net/sampling/1.0
	 http://schemas.opengis.net/sampling/1.0.0/sampling.xsd"
	gml:id="oc_1421828015483" >

	<om:member xlink:href="urn:ogc:def:nil:OGC:inapplicable"/>

</om:ObservationCollection>
```


# 响应解释

OGC OM 1.0标准允许返回`om:ObservationCollection`，其中可包含多个`om:member`。

`om:ObservationCollection`可包含多个`om:member`引发一些问题：
* 应该使用1个`om:member`？还是多个`om:member`？
* 如何区分`om:member`间的区别？（即以什么方式来划分`om:member`？）

在52nSOS 3.x中是这样处理的：
* `一个传感器` 的 `观测属性` 在 `一个空间位置` 、 `一个时刻` 只能有一个 `观测值`
* 
因此，52nSOS 3.x中是【`一个传感器` 的 `观测属性` 在 `一个空间位置` 】的所有时刻观测数据划分为
1个`om:member`。

即是说：
* 有多个传感器的观测数据返回时，肯定会有多个`om:member`。
* （不管有多少个传感器）有多个空间位置返回时，也肯定会有多个`om:member`。

每个`om:member`中的`result`元素包含了传感器的观测值。
若是`swe:DataArray`结构，其值顺序遵循`<swe:elementType name="Components">`中的观测属性顺序。


# 谁来执行这个操作
用户或自动处理的程序。


# 注意的事
* GetObservation中有性能陷阱，如不指定任何传感器ID，或不指定任何时间范围，
这时将容易造成对全库搜索或返回太大量的数据。
* OM标准的实现方式有多种，支持怎样的OM方式，每个SOS可能各有不同。


[ISO 8601]:http://en.wikipedia.org/wiki/ISO_8601
[OGC Filter]:http://www.opengeospatial.org/standards/filter
[OGC GML]:http://www.opengeospatial.org/standards/gml
[GML 例子]:http://en.wikipedia.org/wiki/Geography_Markup_Language
