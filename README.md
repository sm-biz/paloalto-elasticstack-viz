Palo Alto Networks Firewall Visualization using Elastic Stack

## Dashboards

The projects includes nine dashboards, that have been pre-built from the included visualisations. 

Overview Dashboard | Threat Dashboard | Traffic Dashboard
------------ | ------------- | -------------
[![Dashboard - Overview](https://i.imgur.com/xxl0XCfm.png)](https://i.imgur.com/xxl0XCf.png) | [![Dashboard - Threats](https://i.imgur.com/obE4dIbm.png)](https://i.imgur.com/obE4dIb.png) | [![Dashboard - Traffic](https://i.imgur.com/xuxsmnom.png)](https://i.imgur.com/xuxsmno.png)

In addition to the above, there are dashboards for;
* Applications
* Threat Highlights
* URL Filtering
* Blocked URLs
* System Logs & Events
* Config Overview

By default, the dashboards are configured for the dark theme.
Once installed, you can change them to the light theme, add/delete/rearrange individual visualisations or create your own dashboards
The dashboards can also be configured to run full-screen and auto-refresh, perfect for office screenboards

## Background

This project aims to provide a simple way to extract and visualise syslog data from Palo Alto Networks firewalls.
It utilises the free Elastic Stack from [www.elastic.co](https://www.elastic.co/elk-stack) as the base platform data & viz platform, and provides a pipeline configuration and index templates for the following logs;

* Traffic
* Threat/URL
* Config
* System

A full suite of visualisations and dashboards is included
  

**Elastic Stack**

[(from the site)](https://www.elastic.co/elk-stack): What is ELK? "ELK" is the acronym for three open source projects: Elasticsearch, Logstash, and Kibana. Elasticsearch is a search and analytics engine. Logstash is a server-side data processing pipeline that ingests data from multiple sources simultaneously, transforms it, and then sends it to a "stash" like Elasticsearch. Kibana lets users visualize data with charts and graphs in Elasticsearch.

In short, the Elastic Stack provides a simple, scalable & robust platform ingesting syslog entries from a PANW Firewall and displaying the output. The required configuration for LogStash & ElasticSearch is provided here, along with a number of pre-built visualisations for Kibana. You can build your own, additional visualisations using the Kibana interface quite easily. All of the base visualisatons in this project were built in a single day.

Elastic Stack also includes a built-in syslog server, which greatly simplifies the deployment of the solution as a whole.
Using only the Elastic Stack pipeline configuration file, we have everything required for an all-in-one solution
  

**Credit**

Much of this project was created based on the following pages from awesome people, who should be given much applause;
* [Shadow-Box's ELK template for Traffic & Threat Logs](https://github.com/shadow-box/Palo-Alto-Networks-ELK-Stack)
* [ELK + PALO ALTO NETWORKS](https://anderikistan.com/2016/03/26/elk-palo-alto-networks/) by [Ian Anderson](https://twitter.com/anderikistan)

## Tutorial

This project was built on Ubuntu 16.04 LTS, using the latest Elastic Stack 6.1 (with integrated syslog server) and a PA-220 Firewall.
nginx was used to secure authentication to Kibana via reverse-proxy

For those unfamilar with any part of this technology stack, I have created a full tutorial on installing & configuring Elastic Stack, including security the platform & installing the visualisations. :blue_book: The tutorial is [available here](https://github.com/sm-biz/paloalto-elasticstack-viz/wiki)

## Existing Install

Otherwise, if you're comfortable with the technology stack mentioned above, then all you need to do is;

- Download the files from this repo
  - PAN-OS.conf
  - traffic_template_mapping-v1.json
  - threat_template_mapping-v1.json
  - searches-base.json
  - visualisations-base.json
  - dashboards-base.json

- Install Elastic Stack 6.1
  - ElasticSearch
  - Kibana
  - LogStash
- Edit 'PAN-OS.conf'
  - **Set your timezone correctly** *(Very important)*
  - Copy the file into your **conf** directory. For Ubuntu/Debian this is "/etc/logstash/conf.d/", other directories are [available here](https://www.elastic.co/guide/en/logstash/current/dir-layout.html)

- Upload the two pre-built index templates with additional GeoIP fields
```
curl -XPUT http://<your-elasticsearch-server>:9200/_template/panos-traffic*?pretty -H 'Content-Type: application/json' -d @traffic_template_mapping-v1.json
curl -XPUT http://<your-elasticsearch-server>:9200/_template/panos-threat*?pretty -H 'Content-Type: application/json' -d @threat_template_mapping-v1.json
```    
- Restart Elastic Search & LogStash
- Configure your PANW Firewall(s) to send syslog messages to your Elastic Stack server
  - UDP 5514
  - Format BSD
  - Facility LOG_USER
  
- Ensure that your firewall generates at least one traffic, threat, system & config syslog entry each
  - You may have to trigger a threat log entry. Follow [this guide](https://live.paloaltonetworks.com/t5/Management-Articles/How-to-Test-Threat-Prevention-Using-a-Web-Browser/ta-p/62073) from Palo Alto for instructions
  - After committing to set your syslog server, you will need to do another committ (any change) to actually send a config log message
  
- Once the data is rolling, login to Kibana and create the 4 new index patterns, all with a Time Filter field of '@timestamp'
  - panos-traffic
  - panos-threat
  - panos-system
  - panos-config

- And lastly, import the saved object files (in this orders)
  - searches-base.json
  - visualisations-base.json
  - dashboards-base.json
  
And that's it! Once you have some logs in the system, you should see the dashboards start to fill up
  
 
## References

* [Elastic Stack Documentation:](https://www.elastic.co/guide/en/elasticsearch/reference/6.1/index.html) For more information on the Elastic Stack, including the official install guides
* [Basics of Visualisation:](https://www.elastic.co/guide/en/kibana/6.1/tutorial-visualizing.html) To get started making your own visualisations and dashboards
