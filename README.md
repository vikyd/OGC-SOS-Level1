# OGC-SOS-Level1
通俗描述OGC SOS规范是做什么用的。

不管OGC标准是否继续流行，反正有个通俗些的中文文档来理解，能节省时间就好了。

SOS：Sensor Observation Service（中文：传感器观测服务）

# 目的
OGC标准的文档都是英文版的，而且都大致可算是专业性的标准文档，要短时间内看懂有难度。

但难度主要不在于专业性，而在于：
- **标准是英文的;**
- **并且标准的描行文有些晦涩难懂;**

因此，这里想以通俗的方式讲述OGC SOS标准是怎么一回事，尽量排除非本质东西的干扰，还原标准本身。

至于OGC SOS标准本身是否通用性大或实际应用广，那是另外的话题了。


# SOS是什么
**SOS**是用来发布**传感器观测数据**的一个服务标准，基于**XML**格式和HTTP协议来封装请求、响应及观测数据。传感器可通过XML将观测数据存储到SOS，用户可用XML构建时间、空间、传感器、观测内容的请求来获取所需的观测数据。

传感器观测数据的存储、发布、获取并不是新鲜东东，SOS只是OGC组织以标准的形式喊的一句口号命令：来吧，遵循我的格式和接口，你们就可以相互获取数据了。

而实际上，SOS并不像USB3.0相对于USB2.0那样提高了速度，SOS就是一个约定的格式和接口而已。

用实际例子类比的话：

**一个淘宝上的商品，出厂后，如何存入仓库，买家如何购买商品，商品在运输过程中如何包装。**

**SOS关心以下内容**：
- 商品：传感器的观测数据
- 淘宝上的网页描述：传感器的描述信息
- 仓库：OS的后台数据库（`任意数据库`）
- 包装：XML
  - 观测数据：`OGC OM`
  - 传感器描述：`OGC SensorML`
- 运输方式：HTTP（`基本POST、GET获取更上层的WebService`）
- 入库方式：
  - 插入观测数据（`SOS InsertObservation操作`）
  - 传感器描述信息（`SOS RegisterSensor操作`）
- 买家购买（或说出库方式）：
  - 获取具体商品（`SOS GetObservation操作`）
    - 指定商品的厂家、型号、生产日期、款式（`指定观测数据的传感器、观测内容（温度、湿度之类）、时间范围、空间范围`）
  - 获取商品描述信息（`SOS DescribeSensor操作`）


**SOS不关心**：
- 商品如何生产出来的
- 商品运输时走哪条路
- 商品在淘宝店仓库怎么放置的
- 商品从生产、上架、拍下、送达用户手中速度


# SOS的版本
目前版本有：1.0、2.0。

本文以1.0为主要说明，2.0的后续再补充。


# SOS 1.0的操作
一般来说，SOS的操作都是一个XML文件（或说字符串）（遵循对应的[sos xml schema]约束）。

用户/传感器把XML以HTTP的方式（POST/GET/WebService都可以）发送SOS服务的地址（如http://localhost/sos），SOS处理后，会同样以XML形式响应给用户/传感器。不管是插入数据，还是查询数据，流程均如此，这应该算是体现通用性的一点。

**主要操作**：
- [RegisterSensor]
- [InsertObservation]
- [DescribeSensor]
- [GetObservation]
- [GetCapabilities]

**次要操作**：
- [GetObservationById]
- [GetFeatureOfInterest]
- [GetFeatureOfInterestTime]
- [GetResult]
- [DescribeFeatureType]
- [DescribeObservationType]
- [DescribeResultModel]


# 实例
参考上面各操作的示例[example]。

还有[52nSOS XML示例]。


# SOS引用的标准
准确说，应该是SOS引用到的OGC标准。因为OGC有不少基于XML的标准，时间、空间、地图等等方面都有。

SOS不可能所有的东西都自己定义（一来麻烦工作量大，二来会与OGC现有的标准重复、冲突），所以，SOS只定义了操作接口，至于实体内容就直接引用OGC其他标准的内容。这其实也与XML Schema可相互引用的优点有关。

主要引用的标准：
* [OM]
* [SensorML]
* [GML]


# SOS软件
实现SOS的开源软件主要是[52north]组织在维护，有两个主要版本：

* [52nSOS 3.x]
  * 支持SOS 1.0规范主要操作，SOS 2.0少部分操作。
* [52nSOS 4.x]
  * 支持SOS 1.0和2.0主要操作，界面优化不少，持续更新。
  * 
甚至还有[52nSOS for ArcGIS Server]。

其他组织也有实现，但感觉有点小打小闹。


# SOS与OGC SWE
SOS和SOS所引用到的OM、SensorML，均属于[SWE]系列标准的其中之一。

[SWE]包括（按规范主页顺序）：
* OM
* PUCK
* SensorML
* SOS
* SPS
* SWE Common Data Model
* SWE Service Model


[sos xml schema]:http://schemas.opengis.net/sos/

[OM]:http://www.opengeospatial.org/standards/om
[SensorML]:http://www.opengeospatial.org/standards/sensorml
[GML]:http://www.opengeospatial.org/standards/gml
[SWE]:http://www.opengeospatial.org/domain/swe

[example]:https://github.com/Viky-zhang/OGC-SOS-Level1/tree/master/example

[RegisterSensor]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/RegisterSensor.md
[InsertObservation]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/InsertObservation.md
[DescribeSensor]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/DescribeSensor.md
[GetObservation]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/GetObservation.md
[GetCapabilities]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/GetCapabilities.md

[GetObservationById]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/GetObservationById.md
[GetFeatureOfInterest]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/GetFeatureOfInterest.md
[GetFeatureOfInterestTime]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/GetFeatureOfInterestTime.md
[GetResult]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/GetResult.md
[DescribeFeatureType]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/DescribeFeatureType.md
[DescribeObservationType]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/DescribeObservationType.md
[DescribeResultModel]:https://github.com/Viky-zhang/OGC-SOS-Level1/blob/master/operation/DescribeResultModel.md

[52north]:http://52north.org/communities/sensorweb/sos
[52nSOS 3.x]:http://52north.org/communities/sensorweb/sos/codeRepository.html
[52nSOS 4.x]:https://github.com/52North/SOS
[52nSOS for ArcGIS Server]:http://52north.org/communities/sensorweb/sosSOE

[52nSOS XML示例]:https://wiki.52north.org/bin/view/SensorWeb/SosOperations


