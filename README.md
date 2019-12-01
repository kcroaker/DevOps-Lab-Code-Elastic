# DevOps-Lab-Code-Elastic
An incomplete solution 

## Solution

Log File -> Logstash -> Grok -> Elasticsearch

A Logfile is loaded by Logstash. The pipeline for the logstash configuration is defined in

```
./logstash/pipelines/histogram-input.conf
```

* The pipeline watches a file defined in the input section of the pipeline. 
    - The file is mapped to a file that is external to the docker container 
* The Grok filter is used to extract out desired fields from the logline.
    - The intention was going to be to use Logstash's GEOIP filter to turn the column of IP Addresses into locations
* The resulting record is then output to an index in Elasticsearch

### work not completed, but should have been included
* A future configuration would be to either use filebeats to push to Logstash or use a logging plugin to public logs directly to logstash
* A Kibana dashboard would be built for Max temp forcasts for each of the locations
* A Kibana bashboard could also be built for rendering the histogram of max temprature 

## Logstash - Grok Pattern

Building the Grok Pattern: http://grokconstructor.appspot.com/do/construction

Testing the Grok Pattern: http://grokconstructor.appspot.com/do/match

Resulting Pattern: 

```
%{TIMESTAMP_ISO8601:LOG_DATE}%{SPACE}%{INT:ID}%{SPACE}%{UUID:UUID_1}%{SPACE}%{NOTSPACE}?%{SPACE}%{NOTSPACE}?%{SPACE}%{NOTSPACE}%{SPACE}%{BASE16FLOAT}%{SPACE}%{WORD:WORD_1}%{SPACE}%{WORD:WORD_2}%{SPACE}%{WORD:WORD_3}%{SPACE}%{NOTSPACE:WORD_4}%{SPACE}%{INT}%{SPACE}%{INT}%{SPACE}%{INT}%{SPACE}%{WORD:BOOL_1}%{SPACE}%{TIMESTAMP_ISO8601:FORECAST_DATE}%{SPACE}%{NOTSPACE}?%{SPACE}%{INT}?%{SPACE}%{NOTSPACE}?%{SPACE}%{WORD}%{SPACE}%{INT:FORECAST}%{SPACE}%{NOTSPACE}%{SPACE}%{BASE16FLOAT:CONFIDENCE}%{SPACE}%{NOTSPACE:LOCATION}%{SPACE}%{NOTSPACE:JSON_1}%{SPACE}%{WORD:BOOL_2}%{SPACE}%{INT}?%{SPACE}%{BASE16FLOAT}%{SPACE}%{INT}?%{SPACE}[a-z,]*%{SPACE}%{NOTSPACE:JSON_2}%{SPACE}%{NOTSPACE}?%{SPACE}%{INT:INT_2}?%{SPACE}%{BASE16FLOAT:FLOAT_1}%{SPACE}%{BASE16FLOAT:FLOAT_1}%{SPACE}%{BASE16FLOAT:FLOAT_1}%{SPACE}%{INT:INT_3}%{SPACE}%{INT:INT_4}%{GREEDYDATA}
```

Matching against sample lines:
![screenshot of successful grok pattern matching](https://raw.githubusercontent.com/kcroaker/DevOps-Lab-Code-Elastic/master/grok.pattern.matching.jpg)

## Running the solution

To run the solution you need to build the logstash container:

```
docker-compose build logstash
```

Once this is built you can run the solution with the following command:

```
docker-compose up -d
```

Elasticsearch is listening on http://localhost:9200
Kibana is listening on http://localhost:5601
Logstash API is listening on http://localhost:9600
