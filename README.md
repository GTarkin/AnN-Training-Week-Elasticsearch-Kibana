# AnN-Training-Week-Elasticsearch-Kibana

This is the example project used for the Elasticsearch and Kibana introduction in the Adabas & Natural Training Week 2018. To start Elasticsearch and Kibana and import the sample data set perform the following instructions.

### Prerequisites:

For running this example following prerequisites need to be met. You have to:
1. have **docker** installed
2. have **docker-compose** installed
3. possibly increase the number of maximum available memory mapped areas a process might have on linux platforms
4. have **elasticsearch-dump** installed

##### 1. Install **docker**
Install **docker** according to the instructions provided [here](https://docs.docker.com/install/#cloud).

##### 2. Install **docker-compose**
Install **docker-compose** according to the instructions provided [here](https://docs.docker.com/compose/install/#install-compose).

##### 3. Increase maximum number of memory map areas
Increase the maximum number of memory map areas a process may have. Memory map areas are used as a side-effect of calling malloc, directly by mmap and mprotect, and also when loading shared libraries.

While most applications need less than a thousand maps, certain programs, particularly malloc debuggers, may consume lots of them, e.g., up to one or two maps per allocation. 

Elasticsearch needs some more. Increase the number of memory maps with
```sh
sudo sysctl -w vm.max_map_count=262144
```
temporarily. 

You can also make this change permanent by adding following line to your `/etc/sysctl.conf` file. 
```
vm.max_map_count=262144
``` 

##### 4. Install elasticsearch-dump
```sh
$ docker pull taskrabbit/elasticsearch-dump
```

### Using this example:
##### Starting Elasticsearch and Kibana
Check out to a local directory with:
```sh
$ git clone https://github.com/GTarkin/AnN-Training-Week-Elasticsearch-Kibana.git
```

Navigate into the directory:
```
$ cd AnN-Training-Week-Elasticsearch-Kibana
```

Fire up Elasticsearch and Kibana in the background:
```sh
$ docker-compose up -d
```

Check if Elasticsearch and Kibana are both started:
```sh
$ docker-compose ps
```
The output should be something around the lines of:
```sh
                       Name                                     Command               State                Ports              
------------------------------------------------------------------------------------------------------------------------------
anntrainingweekelasticsearchkibana_elasticsearch_1   /bin/bash bin/es-docker          Up      0.0.0.0:9200->9200/tcp, 9300/tcp
anntrainingweekelasticsearchkibana_kibana_1          /bin/sh -c /usr/local/bin/ ...   Up      0.0.0.0:5601->5601/tcp          

```

Check if Elasticsearch is accepting requests:
```sh
$ curl localhost:9200
```

Example output:
```sh
$ curl localhost:9200
{
  "name" : "eFXIRzL",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "ulrn5Lh3TiWYOlihaCcE0A",
  "version" : {
    "number" : "5.2.1",
    "build_hash" : "db0d481",
    "build_date" : "2017-02-09T22:05:32.386Z",
    "build_snapshot" : false,
    "lucene_version" : "6.4.1"
  },
  "tagline" : "You Know, for Search"
}
$ 
```

Check if Kibana is up and running:
```sh
$ curl localhost:5601
```

Example output:
```sh
$ curl localhost:5601
<script>var hashRoute = '/app/kibana';
var defaultRoute = '/app/kibana';

var hash = window.location.hash;
if (hash.length) {
  window.location = hashRoute + hash;
} else {
  window.location = defaultRoute;
}</script>
$ 
```

You can now access Kibana here: [localhost:5601](localhost:5601)

##### Importing the data

Import the provided log data to Elasticsearch
```sh
docker run --rm -ti \
  -v "$(pwd)"/data:/tmp \
  --net="host" \
  taskrabbit/elasticsearch-dump \
  --input=/tmp/adabas_analytics-2018-04-05_mapping.json \
  --output=http://localhost:9200/adabas_analytics-2018-04-05 \
  --type=mapping
```

```sh
docker run --rm -ti \
  -v "$(pwd)"/data:/tmp \
  --net="host" \
  taskrabbit/elasticsearch-dump \
  --input=/tmp/adabas_analytics-2018-04-05_data.json \
  --output=http://localhost:9200/adabas_analytics-2018-04-05 \
  --type=data
```

Now the data is imported and you can browse it in Kibana.
