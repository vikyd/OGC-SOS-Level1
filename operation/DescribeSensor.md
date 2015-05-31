# DescribeSensor
这个SOS 1.0的操作是用于用户从SOS中获取传感器的描述信息（一般是SensorML）。

以下示例XML基于52nSOS 3.x(不再额外说明)，因为不同SOS实现可能略有差异。


# 请求

以下是一个SOS 1.0的DescribeSensor请求示例：

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<DescribeSensor 
	xmlns="http://www.opengis.net/sos/1.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
	 http://www.opengis.net/sos/1.0
	 http://schemas.opengis.net/sos/1.0.0/sosDescribeSensor.xsd"
	outputFormat="text/xml;subtype=&quot;sensorML/1.0.1&quot;"
    version="1.0.0" service="SOS">
	
	<procedure>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</procedure>
	
</DescribeSensor>
```

# 请求解释
**传感器ID**
表示需要获取的传感器描述信息。

一个DescribeSensor请求仅且只能指定1个传感器ID。
``` xml
<procedure>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</procedure>
```

**输出格式**
这个格式一般保持默认即可，因为SOS中一般使用SensorML作为传感器描述格式。
``` xml
outputFormat="text/xml;subtype=&quot;sensorML/1.0.1&quot;"
```

# 响应
成功获取传感器描述信息的响应：
``` xml
<sml:SensorML 
	xmlns="http://www.opengis.net/sos/1.0" 
	xmlns:swe="http://www.opengis.net/swe/1.0.1" 
	xmlns:ows="http://www.opengeospatial.net/ows" 
	xmlns:xlink="http://www.w3.org/1999/xlink" 
	xmlns:gml="http://www.opengis.net/gml" 
	xmlns:ogc="http://www.opengis.net/ogc"
	xmlns:om="http://www.opengis.net/om/1.0" 
	xmlns:sml="http://www.opengis.net/sensorML/1.0.1" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	version="1.0.1">
    <sml:member>
        <sml:System>
            <sml:identification>
                <sml:IdentifierList>
                    <sml:identifier>
                        <sml:Term definition="urn:ogc:def:identifier:OGC:uniqueID">
                            <sml:value>urn:ABC:object:feature:Platform:Station:Weather:sta-a001</sml:value>
                        </sml:Term>
                    </sml:identifier>
                </sml:IdentifierList>
            </sml:identification>
            <sml:validTime>
                <gml:TimePeriod>
                    <gml:beginPosition>2011-01-01T00:00:00.000+08:00</gml:beginPosition>
                    <gml:endPosition>2011-01-01T00:02:00.000+08:00</gml:endPosition>
                </gml:TimePeriod>
            </sml:validTime>
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
                            <swe:uom code="Cel" /></swe:Quantity>
                    </sml:output>
                    <sml:output name="WindSpeed">
                        <swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Speed">
                            <gml:metaDataProperty>
                                <offering>
                                    <id>ABC</id>
                                    <name>ABC组织机构</name>
                                </offering>
                            </gml:metaDataProperty>
                            <swe:uom code="m/s" /></swe:Quantity>
                    </sml:output>
                    <sml:output name="WindDirection">
                        <swe:Quantity definition="urn:ABC:def:phenomenon:ABC:1.0.0:Direction">
                            <gml:metaDataProperty>
                                <offering>
                                    <id>ABC</id>
                                    <name>ABC组织机构</name>
                                </offering>
                            </gml:metaDataProperty>
                            <swe:uom code="degree" /></swe:Quantity>
                    </sml:output>
                </sml:OutputList>
            </sml:outputs>
        </sml:System>
    </sml:member>
</sml:SensorML>
```


# 响应解释
DescribeSensor的响应就是一个完整的SensorML XML，内容与此传感器注册时一致

若是传感器ID在SOS中不存在，则会返回相应的`ows:ExceptionReport `提示。


# 谁来执行这个操作
用户或自动处理的程序。


# 注意的事
* 若想要获取此传感器观测数据，但不知此传感器的观测属性，则可通过DescribeSensor获取，再进行下一步的GetObservation。
* 一个DescribeSensor请求仅且只能指定1个传感器ID。

