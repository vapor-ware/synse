# Monitoring your physical infrastructure with Synse

Recent trends in automation are making it faster, easier, and more reliable to
operate applications and data centers. Containerization and the devops movement
are all about automating tasks that used to be manual. So far, these have stopped
at the software layer and treated underlying hardware as if it was homogeneous.
In the real world, that isn’t the case. Synse provides the missing piece to automate
your data center from top to bottom.

With Synse, there is now a simple HTTP interface (either JSON or GraphQL) to read
data from the sensors, devices, and servers in your data center. This can be used
to integrate with and drive automation systems. When a server gets too warm, the
workloads on it can be migrated somewhere else without any human intervention.
In the past, this would require writing custom implementations against all kinds
of protocols such as IPMI and SNMP.

This tutorial will go through the basics of getting Synse started up and hooked up
to prometheus for monitoring and grafana for an example dashboard to visualize the
data.

Each section will go through this step by step. If you want to just get everything
started up all at once, see the tutorial's corresponding [compose file](../compose/monitoring.yml).

# Video Walkthrough

[![monitoring-with-synse](https://github.com/vapor-ware/synse/raw/master/img/monitoring-keyframe.png)][monitoring-with-synse]

# Exposing your Infrastructure with Synse

As most of us don’t have a pile of spare servers or sensors sitting around ready to
play with, Synse Server comes with a built-in an emulator that lets you try everything
out in a safe, hardware dependency-free environment.

It takes just three simple steps to start playing with Synse Server:

1. Install dependencies. Synse requires [docker][docker] to run (and having 
   [docker-compose][docker-compose] is also recommended for easy deployments).
   
1. Get the code for [synse-server][synse-server]. This can be done by cloning
   the repo and building the image

    ```bash
    git clone https://github.com/vapor-ware/synse-server.git
    cd synse-server
    make docker
    ```
   
   Or more simply by pulling down the latest Synse Server docker image from DockerHub
   
    ```bash
    docker pull vaporio/synse-server
    ``` 
    
1. Start Synse Server with its built-in emulator.

    ```bash
    docker run -d \
       -p 5000:5000 \
       --name synse-server \
       vaporio/synse-server enable-emulator
    ```

These steps will configure Synse Server to pull its data from an [emulator plugin][emulator-plugin]
which features a single rack with a single board populated with a collection of
emulated devices. You can check this out from the command line:

```bash
curl http://localhost:5000/synse/v2/scan
```

The response is a list of all the configured devices, their device types, and
their location information (e.g. which rack and board they exist on). It will
look something like this:

```json
{
  "racks": [
    {
      "id": "rack-1",
      "boards": [
        {
          "id": "vec",
          "devices": [
            {
              "id": "db1e5deb43d9d0af6d80885e74362913",
              "info": "Synse Temperature Sensor 3",
              "type": "temperature"
            },
            {
              "id": "29d1a03e8cddfbf1cf68e14e60e5f5cc",
              "info": "Synse Airflow Sensor",
              "type": "airflow"
            },
            {
              "id": "f3dd2a56b588f181c5c782f45467b214",
              "info": "Synse Pressure Sensor 1",
              "type": "pressure"
            },
            {
              "id": "f52d29fecf05a195af13f14c7306cfed",
              "info": "Synse LED",
              "type": "led"
            },
            {
              "id": "eb9a56f95b5bd6d9b51996ccd0f2329c",
              "info": "Synse Fan",
              "type": "fan"
            },
            {
              "id": "bbadeee4d96ca38ffbcaabb3d8837526",
              "info": "Synse Humidity Sensor",
              "type": "humidity"
            }
          ]
        }
      ]
    }
  ]
}
```

For more information on how to use Synse Server's API for reading, writing, and getting
more device info, take a look at the [user guide][synse-server-docs] and
[api docs][synse-server-api-docs].

# Exploring your Infrastructure with GraphQL

Writing scripts or running lots of CURL commands gets old quickly. GraphQL provides a
simple interface for querying your infrastructure, exploring what's there and getting
an idea of what you can do. To run synse-graphql, you'll need to:

1. Get the [code][synse-graphql]. This can be done by cloning
   the repo and building the image

    ```bash
    git clone https://github.com/vapor-ware/synse-graphql.git
    cd synse-graphql
    make build
    ```
   
   Or more simply by pulling down the latest Synse GraphQL docker image from DockerHub
   
    ```bash
    docker pull vaporio/synse-graphql
    ``` 

2. Setup docker networking and attach Synse Server (which was started up in the previous section):

    ```bash
    docker network create synse
    docker network connect synse synse-server
    ```

3. Startup synse-graphql and connect it to the same docker network.

    ```bash
    docker run -d --rm \
        --name=synse-graphql \
        --network=synse \
        -p 5050:5050 \
        vaporio/synse-graphql
    ```

There is a built-in web interface that makes it easy to look at the schema and
run queries. Go to [`http://localhost:5050/graphql`](http://localhost:5050/graphql)
to pull it up. From there, you'll be able to write some queries against the emulated
devices and see what data is available. A sample query that returns all the temperature
data is:

```graphql
{
  racks {
    id
    boards {
      id
      devices(device_type: "temperature") {
        id
        info
        device_type
        readings {
          reading_type
          value
        }
      }
    }
  }
}

```

Paste that query into the left panel of GraphiQL and click run. You’ll see the result,
which looks something like this in the right panel:

```graphql
{
  "data": {
    "racks": [
      {
        "id": "rack-1",
        "boards": [
          {
            "id": "vec",
            "devices": [
              {
                "id": "0fe8f06229aa9a01ef6032d1ddaf18a5",
                "info": "Synse Temperature Sensor 3",
                "device_type": "temperature",
                "readings": [
                  {
                    "reading_type": "temperature",
                    "value": "3"
                  }
                ]
              },
              {
                "id": "45ffe8f7f7a2b0ae970b687abd06f9e6",
                "info": "Synse Temperature Sensor 1",
                "device_type": "temperature",
                "readings": [
                  {
                    "reading_type": "temperature",
                    "value": "57"
                  }
                ]
              },
              {
                "id": "8f7ac60be5c8a3815ce89753de138edf",
                "info": "Synse Temperature Sensor 5",
                "device_type": "temperature",
                "readings": [
                  {
                    "reading_type": "temperature",
                    "value": "88"
                  }
                ]
              },
              {
                "id": "3ee84834c79c5a124d858e237e81e186",
                "info": "Synse Temperature Sensor 2",
                "device_type": "temperature",
                "readings": [
                  {
                    "reading_type": "temperature",
                    "value": "68"
                  }
                ]
              },
              {
                "id": "f441d97b2f6545ef3001a688489e820a",
                "info": "Synse Temperature Sensor 4",
                "device_type": "temperature",
                "readings": [
                  {
                    "reading_type": "temperature",
                    "value": "31"
                  }
                ]
              }
            ]
          }
        ]
      }
    ]
  }
}
```

The emulator plugin provides multiple temperature sensors. The reading values are
randomized, so if you run the query again the result will be a little different. Notice
also that each device provides a list of readings -- this is because some devices may
provide more than one data point. For example, an `led` type device should provide both
a *state* reading and a *color* reading. You can see this by changing `device_type: "temperature"`
in the query to `device_type: "led"`.

# Monitoring your Infrastructure with Prometheus

Now that we have a way to get data out of our infrastructure, let’s hook up 
Prometheus for some monitoring to give visibility into trends and provide alerting.
We chose Prometheus as our default because it’s a widely adopted open source
monitoring tool that is part of the CNCF. It would be just as easy to integrate
with whatever tool you prefer, including commercial tools like DataDog and Splunk.

Prometheus ingests data via exporters. The synse-graphql service provides one of
these exporters by default. To verify that it is up and running as part of synse-graphql,
you can hit the same endpoint that Prometheus would to gather metrics:

```bash
curl http://localhost:5050/metrics
```

Once the Prometheus exporter starts up, it will immediately query Synse Server via
the GraphQL API to fetch the sensor data for your data center. Currently, it looks for
temperature, pressure, airflow, fan, humidity, and LED devices to get readings from.
This can take a minute or two to finish. The metrics output will become quite a bit longer
once the query has completed.

Next, we will run prometheus. We’ll be using docker to run prometheus. If you’d like
to get this setup more seriously, check out the [getting started docs][prometheus-getting-started].
To run Prometheus:

1. Startup prometheus. Here, `$PWD` is the directory of this tutorial, i.e. `synse/tutorial`

    ```bash
    docker run -d --rm \
        --name=prometheus \
        --network=synse \
        -p 9090:9090 \
        -v $PWD/../compose/config/monitoring/prometheus.yml:/etc/prometheus/prometheus.yml \
        prom/prometheus
    ```

> **Note**:
> It may take a minute for prometheus to ingest the data, so if you aren't seeing any
> data right away, give it some time and try again. 

To see some of the data we’re now collecting and monitoring:

1. Visit [`http://localhost:9090/graph`](http://localhost:9090/graph) in your browser.
1. Add `device_temperature_gauge_temperature` to the `Expression` area.
1. Click execute.

The graph that you’re viewing now has the temperature for every sensor. As this isn’t
particularly useful, it is possible to aggregate the data and get a better understanding
of what’s happening at the data center level. A query to do this is:

```
avg(device_temperature_gauge_temperature) by (rack_id,device_info)
```

Check out some of the [prometheus documentation][prometheus-docs] for more advanced
possibilities like calculating 95th percentiles and standard deviations.

# Visualizing your Infrastructure with Grafana

Infrastructure needs a dashboard. Pairing prometheus with grafana gets you there.
Just like prometheus, we’ll be using docker to run grafana.

To configure grafana:

1. Startup Grafana

    ```bash
    docker run -d --rm \
        --name=grafana \
        --network=synse \
        -p 3000:3000 \
        grafana/grafana
    ```

1. Visit [`http://localhost:3000`](http://localhost:3000) in your browser.
1. Login with admin/admin
1. Click `Add data source`
1. Fill out this form as follows:

    ```
    Name: synse
    Type: Prometheus
    Url: http://prometheus:9090/
    ```

1. Click save and test. If there is a problem here, go back and check on Prometheus
   to make sure it is running.

To create a Synse dashboard:

1. Visit [`http://localhost:3000/dashboard/import`](http://localhost:3000/dashboard/import) in your browser
1. Add `6723` to `Grafana.com Dashboard` (see also: https://grafana.com/dashboards/6723) and press the `Load` button
1. Select `synse` as the prometheus data source
1. Click `Import`

Now, as the Synse Prometheus exporter exports Synse Server data, the dashboard
should update to reflect the emulator data. Since the emulator data is randomized,
the views in this example dashboard are not useful, but going though and setting up
the Synse services enables you to play around with it and set it up against your
own infrastructure.

[docker]: https://docs.docker.com/engine/installation/
[docker-compose]: https://docs.docker.com/compose/install/
[synse-server]: https://github.com/vapor-ware/synse-server
[synse-server-docs]: http://synse-server.readthedocs.io/en/latest/
[synse-server-api-docs]: https://vapor-ware.github.io/synse-server/
[emulator-plugin]: https://github.com/vapor-ware/synse-emulator-plugin
[synse-graphql]: https://github.com/vapor-ware/synse-graphql
[synse-graphql-config]: https://github.com/vapor-ware/synse-graphql/tree/master/config
[prometheus-getting-started]: https://prometheus.io/docs/introduction/getting_started/
[prometheus-docs]: https://prometheus.io/docs/querying/basics/
[monitoring-with-synse]: https://drive.google.com/file/d/1y6AydmJ_CjwUjA2sJiiEhOVAf8_4WJsx/view
