<p align="center"><img src="img/logo.png" width="360"></p>

# Overview

Synse is a simple, scalable API for sensing and controlling data center equipment,
or more generally - physical and virtual devices. It is an open standard for highly
automated and programmatic operation and monitoring of data centers. It can integrate
with existing DCIM standards, such as IPMI, for monitoring IT equipment, and can also
integrate with critical data center systems, such as power and cooling, to provide
a single source of telemetry for data centers of any size. Synse is capable of 
monitoring and managing thousands of endpoints in both centralized and distributed
environments, and is ideally suited for building tools for remote and automated operations.

# Getting Started

- [Monitoring with Synse](tutorial/monitoring.md) - A great way to get an idea
  of the value that Synse can provide. This tutorial walks you through standing
  up an emulated environment and shows example integrations with [Prometheus][prometheus]
  and [Grafana][grafana]. We have a [video walkthrough][monitoring-with-synse] that
  follows along with this tutorial.


# The Synse Ecosystem
There are a number of components that make up the Synse Ecosystem.

- [**vapor-ware/synse-server**][synse-server]: An HTTP server providing a uniform API to interact
  with physical and virtual devices via plugin backends. This can be thought of as a 'front end'
  for Synse Plugins.

- [**vapor-ware/synse-sdk**][synse-sdk]: The official SDK (written in Go) for Synse Plugin
  development.

- [**vapor-ware/synse-server-grpc**][synse-grpc]: The gRPC API that is used for bidirectional
  communication between Synse Server and the Synse Plugins.

- [**vapor-ware/synse-cli**][synse-cli]: A CLI that allows you to easily interact with
  Synse Server and Plugins directly from the command line.

- [**vapor-ware/synse-graphql**][synse-graphql]: A GraphQL wrapper around Synse Server's
  HTTP API that provides a powerful query language enabling simple aggregations and
  operations over multiple devices. It also provides a [Prometheus][prometheus] exporter
  for the metrics it gathers.


# Synse Plugins
In addition to the core components listed above, there is a growing collection of plugins
build using the Synse SDK which extend the capabilities of Synse Server, and Synse as a platform.

- [**Emulator**][synse-emulator-plugin]: A simple plugin with no hardware
  dependencies that can serve as a plugin backend for Synse Server for development,
  testing, and just getting familiar with how Synse Server works. A version of the Synse
  Server docker images comes with this emulator plugin built-in.

- [**SNMP**][synse-snmp-plugin]: A plugin that enables SNMP capabilities for the Synse platform.

- [**IPMI**][synse-ipmi-plugin]: A plugin that enables basic IPMI functionality, including
  reading/writing power status and boot target selection.

- [**Intel AMT**][synse-amt-plugin]: A plugin for communicating with Intel AMT enabled machines.

- [**Modbus TCP/IP**][synse-modbus-ip-plugin]: A basic, general purpose plugin for modbus over
  TCP/IP.


Plugins for the Synse Platform can be found via the [`synse-plugin`][synse-plugin-tag] GitHub tag.


# Helm Charts
If you are looking to deploy Synse on [Kubernetes][kubernetes] we provide some [Helm][helm]
charts for Synse Server and some of the Synse plugins. [Our charts][synse-charts] are actively
maintained and will be added to when new plugins are created.

You can add our helm chart repo to your local helm server with
```
helm repo add synse https://charts.vapor.io
```

To see the available helm charts, you can check out our [chart repo][synse-charts], or
search for `synse` with `helm`

```
$ helm search synse
NAME               	CHART VERSION	APP VERSION	DESCRIPTION
synse/synse-server 	0.1.1        	2.2.4      	An HTTP API for the monitoring and control of physical an...
synse/emulator     	0.1.0        	2.2.0      	Emulator plugin for Synse Server.
synse/modbus       	0.2.0        	1.1.0      	Synse Modbus Over IP Plugin.
synse/snmp         	0.1.0        	           	snmp
```


[kubernetes]: https://kubernetes.io/
[helm]: https://helm.sh/
[prometheus]: https://prometheus.io/
[grafana]: https://grafana.com/
[monitoring-with-synse]: https://drive.google.com/file/d/1y6AydmJ_CjwUjA2sJiiEhOVAf8_4WJsx/view
[synse-sdk]: https://github.com/vapor-ware/synse-sdk
[synse-cli]: https://github.com/vapor-ware/synse-cli
[synse-grpc]: https://github.com/vapor-ware/synse-server-grpc
[synse-server]: https://github.com/vapor-ware/synse-server
[synse-graphql]: https://github.com/vapor-ware/synse-graphql
[synse-cli]: https://github.com/vapor-ware/synse-cli
[synse-snmp-plugin]: https://github.com/vapor-ware/synse-snmp-plugin
[synse-emulator-plugin]: https://github.com/vapor-ware/synse-emulator-plugin
[synse-modbus-ip-plugin]: https://github.com/vapor-ware/synse-modbus-ip-plugin
[synse-amt-plugin]: https://github.com/vapor-ware/synse-amt-plugin
[synse-ipmi-plugin]: https://github.com/vapor-ware/synse-ipmi-plugin
[synse-plugin-tag]: https://github.com/topics/synse-plugin
[synse-charts]: https://charts.vapor.io
