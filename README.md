<p align="center">
  <img src="img/logo.png" width="360"><br />
  <img alt="Data Driven" src="img/data-driven.png" width="250" />
</p>

## Overview

Synse is a platform which provides a simple HTTP API to monitor and control data center
equipment, or more generally - physical and virtual devices. It enables automated and
programmatic operation and monitoring of data centers, supporting protocol backends for
IT and OT equipment, such as IPMI, I²C, and RS-485. The pluggable backend enables you
to gather environmentals, like temperature and humidity, equipment telemetry, like
power usage and data throughput, and control building systems like heating/cooling and
power.

Synse collects all the data from its backend plugins and presents it to the user
in a uniform API, allowing it to become the single source of telemetry and control
for devices and equipment using various protocols.

## The Synse Ecosystem

There are a number of components that make up the *Synse Ecosystem*.

### Core Components

- [**vapor-ware/synse-server**][synse-server]: An HTTP/WebSocket server providing a uniform API to
  interact with physical and virtual devices via plugin backends. This can be thought of as the
  'front end' for Synse Plugins.

- [**vapor-ware/synse-sdk**][synse-sdk]: The official SDK (written in Go) for Synse Plugin
  development.

- [**vapor-ware/synse-server-grpc**][synse-grpc]: The gRPC API that is used for bidirectional
  communication between Synse Server and the Synse Plugins.

- [**vapor-ware/synse-cli**][synse-cli]: A CLI that allows you to easily interact with
  Synse Server and Plugins directly from the command line.

### Synse Plugins

In addition to the core components listed above, there are a growing collection of open source
plugins, extending the capabilities of the Synse platform.

- [**Emulator**][synse-emulator-plugin]: A simple plugin with no hardware dependencies which
  provides simulated device data. It can be used as a plugin backend for development, testing,
  getting familiar with Synse, or as a reference for implementing your own plugins.

- [**SNMP**][synse-snmp-plugin]: A plugin that enables SNMP capabilities for the Synse platform.

- [**IPMI**][synse-ipmi-plugin]: A plugin that enables basic IPMI functionality, including
  reading/writing power status and boot target selection.

- [**Intel AMT**][synse-amt-plugin]: A plugin for communicating with Intel AMT enabled machines.

- [**Modbus TCP/IP**][synse-modbus-ip-plugin]: A basic, general purpose plugin for modbus over
  TCP/IP.

Plugins for the Synse Platform can be found via the [`synse-plugin`][synse-plugin-tag] GitHub tag.

## Helm Charts

If you are looking to deploy Synse on [Kubernetes][kubernetes] we provide some [Helm][helm]
charts for Synse Server and some of the Synse plugins. [Our charts][synse-charts] are actively
maintained. As new plugins are developed, they will be added to the chart repo.

You can add our helm chart repo to your local helm server with:

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
...
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
[synse-cli]: https://github.com/vapor-ware/synse-cli
[synse-snmp-plugin]: https://github.com/vapor-ware/synse-snmp-plugin
[synse-emulator-plugin]: https://github.com/vapor-ware/synse-emulator-plugin
[synse-modbus-ip-plugin]: https://github.com/vapor-ware/synse-modbus-ip-plugin
[synse-amt-plugin]: https://github.com/vapor-ware/synse-amt-plugin
[synse-ipmi-plugin]: https://github.com/vapor-ware/synse-ipmi-plugin
[synse-plugin-tag]: https://github.com/topics/synse-plugin
[synse-charts]: https://charts.vapor.io
