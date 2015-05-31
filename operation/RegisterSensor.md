# RegisterSensor
顾名思义，这个SOS 1.0的操作是用于注册传感器（SOS 2.0中叫InsertSensor）。

以下示例XML基于52nSOS 3.x(不再额外说明)，因为不同SOS实现可能略有差异。


# 请求

以下是一个SOS 1.0的RegisterSensor请求示例：

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<RegisterSensor service="SOS" version="1.0.0" 
	xmlns="http://www.opengis.net/sos/1.0" 
	xmlns:swe="http://www.opengis.net/swe/1.0.1" 
	xmlns:ows="http://www.opengeospatial.net/ows" 
	xmlns:xlink="http://www.w3.org/1999/xlink" 
	xmlns:gml="http://www.opengis.net/gml" 
	xmlns:ogc="http://www.opengis.net/ogc" 
	xmlns:om="http://www.opengis.net/om/1.0" 
	xmlns:sml="http://www.opengis.net/sensorML/1.0.1" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="
	 http://www.opengis.net/sos/1.0
	 http://schemas.opengis.net/sos/1.0.0/sosRegisterSensor.xsd
	 http://www.opengis.net/om/1.0
	 http://schemas.opengis.net/om/1.0.0/extensions/observationSpecialization_override.xsd">
	<SensorDescription>
		<sml:SensorML version="1.0.1">
			<sml:member>
				<sml:System xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
				
					<sml:identification>
						<sml:IdentifierList>
							<sml:identifier>
								<sml:Term definition="urn:ogc:def:identifier:OGC:uniqueID">
									<sml:value>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</sml:value>
								</sml:Term>
							</sml:identifier>
						</sml:IdentifierList>
					</sml:identification>
					
					<sml:outputs>
						<sml:OutputList>	
							<sml:output name="AirTemperature">
								<swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Temperature">
									<gml:metaDataProperty>
										<offering>
											<id>ABC</id>
											<name>ABC组织机构</name>
										</offering>
									</gml:metaDataProperty>
									<swe:uom code="Cel"/>
								</swe:Quantity>
							</sml:output>
							<sml:output name="WindSpeed">
								<swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Speed">
									<gml:metaDataProperty>
										<offering>
											<id>ABC</id>
											<name>ABC组织机构</name>
										</offering>
									</gml:metaDataProperty>
									<swe:uom code="m/s"/>
								</swe:Quantity>
							</sml:output>					
							<sml:output name="WindDirection">
								<swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Direction">
									<gml:metaDataProperty>
										<offering>
											<id>ABC</id>
											<name>ABC组织机构</name>
										</offering>
									</gml:metaDataProperty>
									<swe:uom code="degree"/>
								</swe:Quantity>
							</sml:output>
						</sml:OutputList>						
					</sml:outputs>
					
				</sml:System>
			</sml:member>
		</sml:SensorML>
	</SensorDescription>

	<ObservationTemplate>
		<om:Observation>
			<om:samplingTime/>
			<om:procedure/>
			<om:observedProperty/>
			<om:featureOfInterest/>
			<om:result/>
		</om:Observation>
	</ObservationTemplate>
	
</RegisterSensor>
```

# 请求解释
**RegisterSensor的主体内容**

``` xml
<sml:SensorML version="1.0.1">
```
RegisterSensor的主体内容基于SensorML。

**简单的、适用于52nSOS 3.x的SensorML框架**

需要两大部分：
* 传感器ID
  * 一个普通的字符串
* 观测属性
  * 即观测什么值（气温？气压？）

**传感器ID**

``` xml
<sml:Term definition="urn:ogc:def:identifier:OGC:uniqueID">
	<sml:value>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</sml:value>
</sml:Term>
```
`urn:ogc:def:identifier:OGC:uniqueID`表示此处填传感器的ID。

传感器ID是一个最普通不过的字符串了，但在一个SOS服务中一般应保持唯一。

其实如何分配一个适合的传感器ID通用于多个SOS之间，确实是一个问题，SOS并无明确规定。

这里`urn:ABC:object:feature:Platform:Station:Weather:sta-a001`只是一个比较长的ID而已，其实与`station01`
这样的ID并无太大区别。

**观测属性**

观测属性通常在SensorML的outputs元素中，可以有多个。

详细请见后面章节。




# 请求解释 - 观测属性

传感器的观测属性（暂且这么翻译吧，没想到其他替代的词语）：
传感器观测什么值？如风速、风向就可以算是`观测属性`。

每个观测属性应包含以下信息：
* 观测属性唯一ID
* 观测属性类型
* 观测属性单位（若是数值观测的话）
* 观测属性所述的Offering（ID和名称）（Offering是个奇怪的东西，后面详解）

``` xml
<sml:outputs>
	<sml:OutputList>	
	...
		<sml:output name="AirTemperature">
			<swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Temperature">
				<gml:metaDataProperty>
					<offering>
						<id>ABC</id>
						<name>ABC组织机构</name>
					</offering>
				</gml:metaDataProperty>
				<swe:uom code="Cel"/>
			</swe:Quantity>
		</sml:output>
  ...
	</sml:OutputList>						
</sml:outputs>
```


**观测属性的类型**

不同的观测属性可能对应不同的观测类型，就像Java中基础数据类型的int、String、double、char等。

这里列举常用的几种观测属性类型：
`swe:Count`、
`swe:Quantity`、
`swe:Text`、
`swe:Boolean`、
`swe:Category`、
`swe:Position`
，按SOS标准理论上还可以有很多类型，但通常一个SOS支持的类型是有限的。

**类型：swe:Count**

数量类型。
表示观测到的值是一个数量，如3辆车。是一个整数。

``` xml 
<sml:output name="CarCount">
	<swe:Count definition="urn:ABC:def:phenomenon:ABC:1.0.0:CarCount">
		<gml:metaDataProperty>
			<offering>
				<id>ABC</id>
				<name>ABC组织机构</name>
			</offering>
		</gml:metaDataProperty>
	</swe:Count>
</sml:output>	
``` 

**类型：swe:Quantity**

数值类型。
表示观测到的值是一个实数值，如温度是24.5°。
此类型包含一个单位描述元素`swe:uom`。

这是最常用的观测属性类型。

``` xml 
<sml:output name="AirTemperature">
	<swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Temperature">
		<gml:metaDataProperty>
			<offering>
				<id>ABC</id>
				<name>ABC组织机构</name>
			</offering>
		</gml:metaDataProperty>
		<swe:uom code="Cel"/>
	</swe:Quantity>
</sml:output>
``` 

**类型：swe:Text**

文本类型。
表示观测到的是一个描述性的文本，如。。。

``` xml 
<sml:output name="Speech">
	<swe:Text definition="urn:ABC:def:phenomenon:ABC:1.0.0:Speech">
		<gml:metaDataProperty>
			<offering>
				<id>ABC</id>
				<name>ABC组织机构</name>
			</offering>
		</gml:metaDataProperty>
	</swe:Text>
</sml:output>	
``` 

**类型：swe:Boolean**

布尔类型。
表示观测到的是一个true或false的值，如开关等。

``` xml 
<sml:output name="FlowGateSwitch">
	<swe:Boolean definition="urn:ABC:def:phenomenon:ABC:1.0.0:FlowGateSwitch">
		<gml:metaDataProperty>
			<offering>
				<id>ABC</id>
				<name>ABC组织机构</name>
			</offering>
		</gml:metaDataProperty>
	</swe:Boolean>
</sml:output>	
``` 

**类型：swe:Category**

枚举类型（感觉比**类别类型**听起来容易理解些）。
表示观测到的是一个组预定义值中的一个，如天气预报中的阴天、晴天、雨天等。

实际上这也是一个文本类型，区别在于是一组值中的一个，这就是为什么说是**枚举类型**，
而不说是**类别类型**了。

``` xml 
<sml:output name="GPSState">
	<swe:Category definition="urn:ABC:def:phenomenon:ABC:1.0.0:GPSState">
		<gml:metaDataProperty>
			<offering>
				<id>ABC</id>
				<name>ABC组织机构</name>
			</offering>
		</gml:metaDataProperty>
	</swe:Category>
</sml:output>	
``` 

**类型：swe:Position**

位置类型。
表示观测到的是一个位置值，如GPS、北斗的位置值。

位置值在52nSOS 3.x通常使用WKT格式来描述空间值，
如：`POINT(30.555338 114.311776)#4326`（通常是先纬度后经度）。

``` xml 
<sml:output name="position">
	<swe:Position definition="urn:ogc:def:phenomenon:OGC:1.0.30:position">
		<gml:metaDataProperty>
			<offering>
				<id>ABC</id>
				<name>ABC组织机构</name>
			</offering>
		</gml:metaDataProperty>
	</swe:Position>
</sml:output>
``` 


# 响应
成功注册的响应：
``` xml
<sos:RegisterSensorResponse 
	xmlns:sos="http://www.opengis.net/sos/1.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="
	 http://www.opengis.net/sos/1.0
	 http://schemas.opengis.net/sos/1.0.0/sosAll.xsd">
	 
    <sos:AssignedSensorId>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</sos:AssignedSensorId>
	
</sos:RegisterSensorResponse>
```


# 响应解释
`AssignedSensorId`表示成功新注册这个传感器到SOS中。

若是注册失败或传感器已注册，则会返回相应的`ows:ExceptionReport `提示。


# 谁来执行这个操作
可能第一反应会想到传感器一接上，就自动执行RegisterSensor操作，注册到SOS中，理论上可行，但实际上有难度。

目前传感器普遍不**智能**，难以完成这么**高级**的动作。

所以，一般构建此RegisterSensor XML和发送此XML到SOS中的任务，由一个传感器中间程序来完成。
这个中间程序目前主要手动编写相应的程序。这也是目前接入传感器到SOS中**最麻烦**的一步。
日后SOS流行起来的话大概会方便些吧。


# 注意的事
* RegisterSensor中的SensorML可以包含更多的东西，但在52nSOS 3.x中，可能都只会被当做普通文本存储，并且暂不能被逐个查询。

