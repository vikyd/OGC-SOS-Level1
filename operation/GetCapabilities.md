# GetCapabilities
这个SOS 1.0的操作是用于获取SOS中的元数据信息：传感器列表、观测属性列表、观测位置列表、可用操作列表、服务联系人等。

一般，遇到一个新的SOS，若想从中获取观测数据，最先执行的操作可能就是这个GetCapabilities，
之后才是DescribeSensor和GetObservation。

以下示例XML基于52nSOS 3.x(不再额外说明)，因为不同SOS实现可能略有差异。


# 请求（无Section）

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<GetCapabilities 
	xmlns="http://www.opengis.net/sos/1.0"
	xmlns:ows="http://www.opengis.net/ows/1.1"
	xmlns:ogc="http://www.opengis.net/ogc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.opengis.net/sos/1.0
	http://schemas.opengis.net/sos/1.0.0/sosGetCapabilities.xsd"
	service="SOS">
	
	<ows:AcceptVersions>
		<ows:Version>1.0.0</ows:Version>
	</ows:AcceptVersions>
	
</GetCapabilities>
```


# 请求（仅Contents Section）

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<GetCapabilities 
	xmlns="http://www.opengis.net/sos/1.0"
	xmlns:ows="http://www.opengis.net/ows/1.1"
	xmlns:ogc="http://www.opengis.net/ogc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.opengis.net/sos/1.0
	http://schemas.opengis.net/sos/1.0.0/sosGetCapabilities.xsd"
	service="SOS">
	
	<ows:AcceptVersions>
		<ows:Version>1.0.0</ows:Version>
	</ows:AcceptVersions>
	
	<ows:Sections>	
		<ows:Section>Contents</ows:Section>
	</ows:Sections>

</GetCapabilities>
```


# 请求（所有Section）

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<GetCapabilities 
	xmlns="http://www.opengis.net/sos/1.0"
	xmlns:ows="http://www.opengis.net/ows/1.1"
	xmlns:ogc="http://www.opengis.net/ogc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.opengis.net/sos/1.0
	http://schemas.opengis.net/sos/1.0.0/sosGetCapabilities.xsd"
	service="SOS">
	
	<ows:AcceptVersions>
		<ows:Version>1.0.0</ows:Version>
	</ows:AcceptVersions>
	
	<ows:Sections>	
		<ows:Section>OperationsMetadata</ows:Section>	
		<ows:Section>ServiceIdentification</ows:Section>
		<ows:Section>ServiceProvider</ows:Section>
		<ows:Section>Filter_Capabilities</ows:Section>
		<ows:Section>Contents</ows:Section>
	</ows:Sections>

</GetCapabilities>
```


# 请求解释
**版本号**

版本号，表示返回的元数据信息应以SOS 1.0或2.0或其他可能的版本的形式返回。
一般情况下，保持1.0默认也可以。
``` xml
<ows:AcceptVersions>
	<ows:Version>1.0.0</ows:Version>
</ows:AcceptVersions>
```

**Section（元数据的类型）**

Section可以有多个，主要有：
* Contents
* OperationsMetadata
* Filter_Capabilities
* ServiceIdentification
* ServiceProvider

GetCapabilities时可选用其中的0 - n个Section：
* 0个：与全选一样，返回所有元数据信息。
* 1-n个：仅返回这些指定的元数据信息。


**实际**
一般若只需获取传感器的列表、观测属性列表，那么只获取Contents的Section可能是最快速的，
因为Contents包含了必要的内容，且返回数据量较小。


# 响应（仅ContentsSection）
获取成功的响应：
``` xml
<sos:Capabilities 
	xmlns:sos="http://www.opengis.net/sos/1.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:gml="http://www.opengis.net/gml" 
	xmlns:xlink="http://www.w3.org/1999/xlink" 
	xsi:schemaLocation="http://www.opengis.net/sos/1.0 http://schemas.opengis.net/sos/1.0.0/sosAll.xsd"
	version="1.0.0" >
    <sos:Contents>
        <sos:ObservationOfferingList>
            <sos:ObservationOffering gml:id="ABC">
			
                <gml:name>ABC组织机构</gml:name>
				
                <gml:boundedBy>
                    <gml:Envelope srsName="urn:ogc:def:crs:EPSG::4326">
                        <gml:lowerCorner>30.535296 114.369321</gml:lowerCorner>
                        <gml:upperCorner>30.535296 114.369321</gml:upperCorner>
                    </gml:Envelope>
                </gml:boundedBy>
				
                <sos:time>
                    <gml:TimePeriod xsi:type="gml:TimePeriodType">
                        <gml:beginPosition>2011-01-01T00:00:00.000+08:00</gml:beginPosition>
                        <gml:endPosition>2011-01-01T00:02:00.000+08:00</gml:endPosition>
                    </gml:TimePeriod>
                </sos:time>
				
                <sos:procedure xlink:href="urn:ABC:object:feature:Platform:Station:Weather:sta-a001" />
                <sos:procedure xlink:href="urn:ABC:object:feature:Platform:Station:Weather:sta-a002" />
                <sos:procedure xlink:href="urn:ABC:object:feature:Platform:car01" />
                <sos:procedure xlink:href="urn:ABC:object:feature:Platform:car02" />
				
                <sos:observedProperty xlink:href="urn:ABC:def:phenomenon:ABC:1.0.0:Temperature" />
                <sos:observedProperty xlink:href="urn:ABC:def:phenomenon:ABC:1.0.0:Speed" />
                <sos:observedProperty xlink:href="urn:ABC:def:phenomenon:ABC:1.0.0:Direction" />
                <sos:observedProperty xlink:href="urn:ogc:def:phenomenon:OGC:1.0.30:position" />
                <sos:observedProperty xlink:href="urn:ABC:def:phenomenon:ABC:1.0.0:GPSState" />
				
                <sos:featureOfInterest xlink:href="sta-a001" />
				
                <sos:responseFormat>text/xml;subtype="om/1.0.0"</sos:responseFormat>
                <sos:responseFormat>application/zip</sos:responseFormat>
				
                <sos:resultModel xmlns:ns="http://www.opengis.net/om/1.0">ns:Observation</sos:resultModel>
                <sos:resultModel xmlns:ns="http://www.opengis.net/om/1.0">ns:Measurement</sos:resultModel>
                <sos:resultModel xmlns:ns="http://www.opengis.net/om/1.0">ns:CategoryObservation</sos:resultModel>
                <sos:resultModel xmlns:ns="http://www.opengis.net/om/1.0">ns:GeometryObservation</sos:resultModel>
				
                <sos:responseMode>inline</sos:responseMode>
                <sos:responseMode>resultTemplate</sos:responseMode>
				
            </sos:ObservationOffering>
        </sos:ObservationOfferingList>
    </sos:Contents>
</sos:Capabilities>
```


# 响应（所有Section）
获取成功的响应：
参考example中的文件。


# 响应解释
参照Section的名称大致可知道具体意思，按需获取即可。

获取传感器列表，一般建议仅使用Contents Section。


# 谁来执行这个操作
用户或自动处理的程序。


# 注意的事
* GetCapabilities不能获取到传感器的所谓类型，因为SOS标准中无定义，传感器类型本身也无明确界线。
* GetCapabilities不能直接一步获取到`传感器->观测属性`这样的树状关系。

