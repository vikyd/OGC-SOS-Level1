# GetFeatureOfInterest
用于获取传感器的观测位置，此位置通常是一个GML表示的空间值。

（观测位置、观测区域，傻傻分不清楚~~~
其实不是分不清楚，感觉有点像是标准说了，很明了，
但又没有对应的明确的措施）

此操作一般不常用。


# 请求

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<GetFeatureOfInterest 
	xmlns="http://www.opengis.net/sos/1.0" 
	xmlns:ows="http://www.opengeospatial.net/ows" 
	xmlns:gml="http://www.opengis.net/gml" 
	xmlns:ogc="http://www.opengis.net/ogc" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xsi:schemaLocation="
	 http://www.opengis.net/sos/1.0
	 http://schemas.opengis.net/sos/1.0.0/sosGetFeatureOfInterest.xsd"
	service="SOS" version="1.0.0" >
 
	<FeatureOfInterestId>sta-a001</FeatureOfInterestId>
	
</GetFeatureOfInterest>
```


# 请求解释
**FeatureOfInterestId**

此ID是InsertObservation中`featureOfInterest`的ID（`<sa:SamplingPoint gml:id="sta-a001">`）。


# 响应

``` xml
<sa:SamplingPoint 
	xmlns:sa="http://www.opengis.net/sampling/1.0" 
	xmlns:gml="http://www.opengis.net/gml" 
	xmlns:xlink="http://www.w3.org/1999/xlink" 
	gml:id="sta-a001">
	
    <gml:name>station a001</gml:name>
	
    <sa:sampledFeature xlink:href="urn:ogc:def:nil:OGC:unknown" />
	
    <sa:position>
        <gml:Point gml:id="point_sf_1">
            <gml:pos srsName="urn:ogc:def:crs:EPSG::4326">30.535296 114.369321</gml:pos>
        </gml:Point>
    </sa:position>
	
</sa:SamplingPoint>
```


# 响应解释

返回的主要就是一个gml的空间值。


# 谁来执行这个操作
用户或自动处理的程序。


# 注意的事
* 无






















