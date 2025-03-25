# 维多利亚州车祸潜在影响因素分析项目

[![CN](https://img.shields.io/badge/语言-中文-red.svg)](README.md)
[![EN](https://img.shields.io/badge/Language-English-blue.svg)](README_EN.md)

## 项目概述

本项目旨在分析维多利亚州车祸的潜在影响因素，包括地区、车祸严重程度、人口和天气等多个维度。通过数据分析和可视化，帮助相关部门制定有效的交通安全策略。

## 重要链接

- **报告**：[维多利亚州车祸潜在影响因素分析](https://www.overleaf.com/4751181365djrvrfzzxqrt#c886a0)
- **Confluence页面**：[CCC_A2](https://felikskong.atlassian.net/wiki/spaces/CCCA2/overview?homepageId=295444)

## 数据来源

- **维多利亚州人口**：[SUDO](https://sudo.eresearch.unimelb.edu.au/)
- **维多利亚州地理信息**：[SUDO](https://sudo.eresearch.unimelb.edu.au/)
- **维多利亚州道路事故数据**：[维多利亚州政府数据目录](https://vicroadsopendatastorehouse.vicroads.vic.gov.au/opendata/Road_Safety/RCIS%20Documents/Metadata%20-%20Victoria%20Road%20Crash%20data.pdf)
- **过去两年的天气状况**：[BOM](http://www.bom.gov.au/)

## 场景与图表

- VIC LGA地区的事故数量和严重程度
- 人口与车祸数量的关系
- 降雨量和速度对事故数量的影响
- 车祸的严重程度
- 道路状况与车祸数量的关系

## RESTful API端点

- 获取维多利亚州每个地方政府区域（LGA）的人口信息：`http://127.0.0.1:9090/search/population`
- 获取2022至2023年的所有事故详情：`http://127.0.0.1:9090/search/accidents`
- 获取所有事故的坐标和LGA名称：`http://127.0.0.1:9090/search/accident_locations`
- 获取维多利亚州的地理信息：`http://127.0.0.1:9090/search/geoinfo`
- 获取事故路面信息：`http://127.0.0.1:9090/search/roadcondition`
- 在一段时间内搜索天气：`http://127.0.0.1:9090/searchweather/Startdate/Enddate`

## 数据上传功能

### 从开放API进行数据流上传

- 从BOM抓取2023年的天气数据并插入ES：`curl -X PUT "http://127.0.0.1:9090/put/accidents"`
- 从2022到2023年抓取事故详情并上传：`curl -X PUT "http://127.0.0.1:9090/put/weather"`

### 静态数据上传

- 插入事故地点：`curl -X PUT "http://127.0.0.1:9090/put/accident_locations" -H "Content-Type: application/json" -d @data/upload_location.json`
- 插入地理信息：`curl -X PUT "http://127.0.0.1:9090/put/geoinfo" -H "Content-Type: application/json" -d @data/upload_geoinfo.json`
- 插入人口数据：`curl -X PUT "http://127.0.0.1:9090/put/population" -H "Content-Type: application/json" -d @data/upload_population.json`
- 插入道路状况数据：`curl -X PUT "http://127.0.0.1:9090/put/roadcondition" -H "Content-Type: application/json" -d @data/upload_road_condition.json`
- 插入2022年的天气信息：`curl -X PUT "http://127.0.0.1:9090/put/weather2022" -H "Content-Type: application/json" -d @data/upload_weather.json`

## 设置说明

1. 连接VPN
2. 源openrc.sh：`source <path/to/openrc.sh>`
3. SSH进入服务器：`ssh -i <path/to/.pem> -L 6443:$(openstack coe cluster show elastic -f json | jq -r '.master_addresses[]'):6443 ubuntu@$(openstack server show bastion -c addresses -f json | jq -r '.addresses["qh2-uom-internal"][]')`
4. Elasticsearch：`kubectl port-forward service/elasticsearch-master -n elastic 9200:9200`
5. Kibana：`kubectl port-forward service/kibana-kibana -n elastic 5601:5601`
6. Fission：`kubectl port-forward service/router -n fission 9090:80`

## 测试

使用unittest运行测试：
```bash
python -m unittest tests.test_api.TestAPIEndpoints.test_search_by_index
```
