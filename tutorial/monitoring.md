# Monitoring your physical infrastructure with Synse

Recent trends in automation are making it faster, easier and more reliable to operate applications and data centers. Containerization and the devops movement are all about automating tasks that used to be manual. So far, these have stopped at the software layer and treated underlying hardware as if it was homogeneous. In the real world, that isn’t the case. Synse provides the missing piece to automate your data center from top to bottom.

With Synse, there is now a simple HTTP interface (either RESTful or GraphQL) to read data from the sensors, devices and servers in your data center. This can be used to integrate with and drive automation systems. When a server gets too warm, the workloads on it can be migrated somewhere else without any human intervention. In the past, this would require writing custom implementations against all kinds of protocols such as IPMI and SNMP.

# Exposing your Infrastructure with Synse

As most of us don’t have a pile of spare servers sitting around ready to play with, Synse comes with a built-in an emulator that lets you try everything out in a safe environment.

To start playing with Synse requires just three simple steps:

1. Install dependencies. Synse requires both [docker][docker] and [docker-compose][docker-compose] to run.
1. Get the code for [synse-server][synse-server].

    ```bash
    git clone https://github.com/vapor-ware/synse-server.git
    ```

1. Start synse-server and the emulator.

    ```bash
    docker-compose -f compose/emulator.yml up -d
    ```

These steps will configure synse-server to pull data from an IPMI emulator. You’ll end up with 6 racks that have 6 servers each. You can check this out from the command line:

```bash
curl http://localhost:5000/synse/1.4/scan
```

The response is a list of all the configured servers and what sensors they export. It will look like this:

```json
{
  "racks": [
    {
      "boards": [
        {
          "board_id": "40000015",
          "devices": [
            {
              "device_id": "0100",
              "device_info": "power",
              "device_type": "power"
            }
          ],
          "hostnames": [
            "ipmi-emulator-1-1"
          ],
          "ip_addresses": [
            "ipmi-emulator-1-1"
          ]
        }
      ],
      "rack_id": "rack_1"
    }
  ]
}
```

You’ll notice that while this is a list of all the sensors and devices, there are no readings. It is possible to get these via. the REST API. Take a look at the [docs][synse-server-docs] for more details.

# Exploring your Infrastructure with GraphQL

Writing scripts or running lots of CURL commands gets old quickly. GraphQL provides a simple interface for querying your infrastructure, exporing what's there and getting an idea of what you can do. To run synse-graphql, you'll need to:

1. Get the [code][synse-graphql].

    ```bash
    git clone https://github.com/vapor-ware/synse-graphql.git
    ```

2. Setup docker networking:

    ```bash
    docker network create synse
    docker network connect synse synse-server
    ```

3. Startup synse-graphql.

    ```bash
    docker run -d --rm \
        --name=synse-graphql \
        --network=synse \
        -e SYNSE_SERVER=synse-server:5000 \
        -p 5001:5001 \
        vaporio/synse-graphql
    ```

There is a built-in web interface that makes it easy to look at the schema and run queries. Go to `http://localhost:5001/graphql` to pull it up. From here, you'll be able to write some queries against the emulated data center and see what data is available. A sample query that returns all the temperature data is:

```graphql
{
  racks {
    id
    boards {
      id
      devices(device_type:"temperature") {
        id
        info
        device_type
        ... on TemperatureDevice {
          temperature_c
        }
      }
    }
  }
}
```

Paste that query into the left panel of GraphiQL and click run. You’ll see the result, which looks something like this in the right panel:

```graphql
{
  "data": {
    "racks": [
      {
        "id": "rack_6",
        "boards": [
          {
            "id": "40000023",
            "devices": [
              {
                "id": "0011",
                "info": "System Temp",
                "device_type": "temperature",
                "temperature_c": 49
              },
              {
                "id": "0012",
                "info": "CPU Temp",
                "device_type": "temperature",
                "temperature_c": 41
              }
            ]
          }
        ]
      }
    ]
  }
}
```

The emulator provides a CPU and System temperature sensor for each server. These readings are randomized, so if you run the query again the result will be a little different.

# Monitoring your Infrastructure with Prometheus

Now that we have a way to get data out of our infrastructure, let’s hook up Prometheus for some monitoring to give visibility into trends and provide alerting. We chose Prometheus as our default because it’s a widely adopted open source monitoring tool that is part of the CNCF. It would be just as easy to integrate with whatever tool you prefer, including commercial tools like DataDog and Splunk.

Prometheus ingests data via exporters. To get the Synse Prometheus exporter working, you’ll need to follow these steps:

1. Get the [code][synse-prometheus]

    ```bash
    git clone https://github.com/vapor-ware/synse-prometheus.git
    ```

1. Startup the exporter.

    ```bash
    docker run -d --rm \
        --name=synse-prometheus \
        --network=synse \
        -e SYNSE_GRAPHQL=synse-graphql:5001 \
        -p 9243:9243 \
        vaporio/synse-prometheus
    ```

To verify that the Prometheus exporter is up and running, you can hit the same endpoint Prometheus would to gather metrics:

```bash
curl http://localhost:9243/metrics
```

Once the Prometheus exporter starts up, it will immediately query Synse to fetch all the temperature, fan and power data for your data center. This takes a minute or two to finish. The metrics output will become quite a bit longer once the query has completed. Take a look at [the graphql query][prometheus-query] if you’re interested.

Next, we will run prometheus. We’ll be using docker to run prometheus. If you’d like to get this setup more seriously, check out the [getting started docs][prometheus-getting-started]. To run Prometheus:

1. Get into the `synse-prometheus` directory.
1. Startup prometheus.

    ```bash
    docker run -d --rm \
        --name=prometheus \
        --network=synse \
        -p 9090:9090 \
        -v $PWD/example-config.yml:/etc/prometheus/prometheus.yml \
        prom/prometheus
    ```

To see some of the data we’re now collecting and monitoring:

1. Visit `http://localhost:9090` in your browser.
1. Click on the graph button.
1. Add `device_temperature_gauge_temperature_c` to the `Expression` area.
1. Click execute.

The graph that you’re viewing now has the temperature for every sensor. As this isn’t particularly useful, it is possible to aggregate the data and get a better understanding of what’s happening at the data center level. A query to do this is:

```
avg(device_temperature_gauge_temperature_c) by (rack_id,device_info)
```

Check out some of the [prometheus documentation][prometheus-docs] for more advanced possibilities like calculating 95th percentiles and standard deviations.

# Visualizing your Infrastructure with Grafana

Infrastructure needs a dashboard. Pairing prometheus with grafana gets you there. Just like prometheus, we’ll be using docker to run grafana. There are other options as well.

To configure grafana and prometheus:

1. Startup Grafana

    ```bash
    docker run -d --rm \
        --name=grafana \
        --network=synse \
        -p 3000:3000 \
        grafana/grafana
    ```

1. Visit `http://localhost:3000` in your browser.
1. Login with admin/admin
1. Click `Add data source`
1. Fill out this form as follows:

    ```
    Name: prometheus
    Type: Prometheus
    Url: http://prometheus:9090/
    Access: proxy
    ```

1. Click save and test. If there is a problem here, go back and check on Prometheus to make sure it is running.

To create a Synse DC dashboard:

1. Visit `http://localhost:3000/dashboard/new?editview=import&orgId=1` in your browser
1. Add `2304` to `Grafana.net Dashboard`
1. Select `prometheus` as the data source
1. Click `Import`

[docker]: https://docs.docker.com/engine/installation/
[docker-compose]: https://docs.docker.com/compose/install/
[synse-server]: https://github.com/vapor-ware/synse-server
[synse-server-docs]: http://opendcre.com/
[synse-graphql]: https://github.com/vapor-ware/synse-graphql
[synse-prometheus]: https://github.com/vapor-ware/synse-prometheus
[prometheus-query]: https://github.com/vapor-ware/synse-prometheus/blob/master/synse_prometheus/prometheus.py#L22
[prometheus-getting-started]: https://prometheus.io/docs/introduction/getting_started/
[prometheus-docs]: https://prometheus.io/docs/querying/basics/
