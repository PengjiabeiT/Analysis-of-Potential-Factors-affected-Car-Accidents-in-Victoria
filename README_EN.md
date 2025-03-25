# Analysis of Potential Factors affected Car Accidents in Victoria
[![CN](https://img.shields.io/badge/语言-中文-red.svg)](README.md)
[![EN](https://img.shields.io/badge/Language-English-blue.svg)](README_EN.md)
## Overview

This project aims to analyze potential factors affecting car accidents in Victoria, including region, accident severity, population, and weather. Through data analysis and visualization, it helps relevant departments formulate effective traffic safety strategies.

## Important Links

- **Report**: [Analysis of Potential Factors-affected Car Accidents in Victoria](https://www.overleaf.com/4751181365djrvrfzzxqrt#c886a0)
- **Confluence Page**: [CCC_A2](https://felikskong.atlassian.net/wiki/spaces/CCCA2/overview?homepageId=295444)

## Data Sources

- **Population in Victoria**: [SUDO](https://sudo.eresearch.unimelb.edu.au/)
- **Geographical Information of Victoria**: [SUDO](https://sudo.eresearch.unimelb.edu.au/)
- **Victoria Road Crash Data**: [Victorian Government Data Directory](https://vicroadsopendatastorehouse.vicroads.vic.gov.au/opendata/Road_Safety/RCIS%20Documents/Metadata%20-%20Victoria%20Road%20Crash%20data.pdf)
- **Weather Conditions in Last Two Years**: [BOM](http://www.bom.gov.au/)

## Scenarios and Figures

- The number and severity of accidents in VIC LGA areas
- The population VS. the number of car accidents
- The rainfall and speed on the number of accidents
- The severity of the car accidents
- The road conditions VS. the number of car accidents

## RESTful API Endpoints

- Get population for each local government area (LGA) in Victoria: `http://127.0.0.1:9090/search/population`
- Get all accident details from 2022 to 2023: `http://127.0.0.1:9090/search/accidents`
- Get coordinates and LGA name for all accidents: `http://127.0.0.1:9090/search/accident_locations`
- Get geographic information of Victoria: `http://127.0.0.1:9090/search/geoinfo`
- Get information of accident road surface: `http://127.0.0.1:9090/search/roadcondition`
- Search weather in a period: `http://127.0.0.1:9090/searchweather/Startdate/Enddate`

## Data Upload Functions

### Streaming from Open API

- Scrape weather data of 2023 from BOM and insert into ES: `curl -X PUT "http://127.0.0.1:9090/put/accidents"`
- Scrape accident details from 2022 to 2023 and upload: `curl -X PUT "http://127.0.0.1:9090/put/weather"`

### Static Data Upload

- Insert accident locations: `curl -X PUT "http://127.0.0.1:9090/put/accident_locations" -H "Content-Type: application/json" -d @data/upload_location.json`
- Insert geographic information: `curl -X PUT "http://127.0.0.1:9090/put/geoinfo" -H "Content-Type: application/json" -d @data/upload_geoinfo.json`
- Insert population data: `curl -X PUT "http://127.0.0.1:9090/put/population" -H "Content-Type: application/json" -d @data/upload_population.json`
- Insert road condition data: `curl -X PUT "http://127.0.0.1:9090/put/roadcondition" -H "Content-Type: application/json" -d @data/upload_road_condition.json`
- Insert weather information for 2022: `curl -X PUT "http://127.0.0.1:9090/put/weather2022" -H "Content-Type: application/json" -d @data/upload_weather.json`

## Setup Instructions

1. Connect to VPN
2. Source openrc.sh: `source <path/to/openrc.sh>`
3. SSH into server: `ssh -i <path/to/.pem> -L 6443:$(openstack coe cluster show elastic -f json | jq -r '.master_addresses[]'):6443 ubuntu@$(openstack server show bastion -c addresses -f json | jq -r '.addresses["qh2-uom-internal"][]')`
4. Elasticsearch: `kubectl port-forward service/elasticsearch-master -n elastic 9200:9200`
5. Kibana: `kubectl port-forward service/kibana-kibana -n elastic 5601:5601`
6. Fission: `kubectl port-forward service/router -n fission 9090:80`

## Testing

Run tests using unittest:
```bash
python -m unittest tests.test_api.TestAPIEndpoints.test_search_by_index