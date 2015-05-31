# InsertObservation
这个SOS 1.0的操作是用于传感器向SOS插入观测数据（OM格式）。

以下示例XML基于52nSOS 3.x(不再额外说明)，因为不同SOS实现可能略有差异。


# 请求

以下是一个SOS 1.0的InsertObservation请求（或说发送）示例：

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<InsertObservation 
	 service="SOS" version="1.0.0"
	xmlns="http://www.opengis.net/sos/1.0" 
	xmlns:ows="http://www.opengis.net/ows/1.1" 
	xmlns:ogc="http://www.opengis.net/ogc" 
	xmlns:om="http://www.opengis.net/om/1.0" 
	xmlns:sos="http://www.opengis.net/sos/1.0" 
	xmlns:sa="http://www.opengis.net/sampling/1.0" 
	xmlns:gml="http://www.opengis.net/gml" 
	xmlns:swe="http://www.opengis.net/swe/1.0.1" 
	xmlns:xlink="http://www.w3.org/1999/xlink" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="
	 http://www.opengis.net/sos/1.0
	 http://schemas.opengis.net/sos/1.0.0/sosInsert.xsd
	 http://www.opengis.net/sampling/1.0
	 http://schemas.opengis.net/sampling/1.0.0/sampling.xsd">
	 
	<AssignedSensorId>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</AssignedSensorId>
	
	<om:Observation>
	
		<om:samplingTime>
			<gml:TimeInstant>
				<gml:timePosition>2011-01-01T00:00:00.000+08:00</gml:timePosition>
			</gml:TimeInstant>
		</om:samplingTime>
		
		<om:procedure xlink:href="urn:ABC:object:feature:Platform:Station:Weather:sta-a001"/>
		
		<om:observedProperty>
			<swe:CompositePhenomenon gml:id="cpid0" dimension="1">
				<gml:name>resultComponents</gml:name>
				<swe:component xlink:href="http://www.opengis.net/def/uom/ISO-8601/0/Gregorian"/>
				<swe:component xlink:href="urn:ABC:def:phenomenon:ABC:1.0.0:Temperature"/>
				<swe:component xlink:href="urn:ABC:def:phenomenon:ABC:1.0.0:Speed"/>				
				<swe:component xlink:href="urn:ABC:def:phenomenon:ABC:1.0.0:Direction"/>
			</swe:CompositePhenomenon>
		</om:observedProperty>
		
		<om:featureOfInterest>
			<sa:SamplingPoint gml:id="sta-a001">
				<gml:name>station a001</gml:name>
				<sa:sampledFeature xlink:href=""/>
				<sa:position>
					<gml:Point srsName="urn:ogc:def:crs:EPSG::4326">
						<gml:pos>30.535296 114.369321</gml:pos>
					</gml:Point>
				</sa:position>
			</sa:SamplingPoint>
		</om:featureOfInterest>
		
		<om:result>
			<swe:DataArray>
				<swe:elementCount>
					<swe:Count>
						<swe:value>1</swe:value>
					</swe:Count>
				</swe:elementCount>
				<swe:elementType name="Components">
					<swe:DataRecord>
						<swe:field name="Time">
							<swe:Time definition="http://www.opengis.net/def/uom/ISO-8601/0/Gregorian"/>
						</swe:field>
						<swe:field name="AirTemperature">
							<swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Temperature">
								<swe:uom code="Cel"/>
							</swe:Quantity>
						</swe:field>						
						<swe:field name="WindSpeed">
							<swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Speed">
								<swe:uom code="m/s"/>
							</swe:Quantity>
						</swe:field>
						<swe:field name="WindDirection">
							<swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Direction">
								<swe:uom code="degree"/>
							</swe:Quantity>							
						</swe:field>						
					</swe:DataRecord>
				</swe:elementType>
				<swe:encoding>
					<swe:TextBlock decimalSeparator="." tokenSeparator="," blockSeparator=";"/>
				</swe:encoding>
				<swe:values>
				2011-01-01T00:00:00.000+08:00,-0.6,11.8,226;
				</swe:values>
			</swe:DataArray>
		</om:result>
		
	</om:Observation>
</InsertObservation>
```

# 请求解释
**InsertObservation的主体框架**

一句话：InsertObservation的主体框架 = 一个`AssignedSensorId` + 一个`OM`。

实际上，`OM`中也已有传感器ID，`AssignedSensorId`略显多余。

**AssignedSensorId**

表明插入的观测数据是这个传感器的。

``` xml
<AssignedSensorId>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</AssignedSensorId>
```

**OM**

OM是OGC的标准，与SOS、SensorML同属于SWE框架下的标准。

``` xml
<om:Observation>
...
</om:Observation>
```

# 响应
成功插入观测数据的响应：
``` xml
<sos:InsertObservationResponse xmlns:sos="http://www.opengis.net/sos/1.0">
    <sos:AssignedObservationId>o_3</sos:AssignedObservationId>
</sos:InsertObservationResponse>
```


# 响应解释
响应是一个观测数据的ID，此ID表示可通过[GetObservationById]操作来获取本次插入的观测数据。

实际上，52nSOS 3.x存储时，此ID仅表示最后一个观测值的ID，而非真的本次插入的所有数据的ID，算是一个简化吧。


# 谁来执行这个操作
一般是传感器执行此操作（或其中间件程序）。


# 注意的事
* 可一次性插入多个观测数据。
* 52nSOS 3.x中，若是一次性插入多个观测数据，那如果其中一个数据已经存在在SOS（其他还没存在），
将会导致整个插入观测数据失败。这算是一个实现机制问题吧，不是对或错的问题，只是52nSOS 3.x这么处理而已。


[GetObservationById]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/GetObservationById.md

