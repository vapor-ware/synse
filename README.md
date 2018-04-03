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
  and [Grafana][grafana]. We have a [video walkthrough][monitoring-with-synse] if
  you'd like an overview.

# Components

## synse-server

[synse-server][synse-server] provides a simple JSON API to monitor and control physical
and virtual devices. It provides a uniform HTTP interface for back-end plugins
implementing various protocols, such as RS485, I2C, and IPMI. The Synse Server API
makes it easy to read from and write to devices, gather device meta-information, and
scan for devices across multiple back-ends through a curl-able interface.

## synse-graphql

[synse-graphql][synse-graphql] provides a GraphQL API that enables complex querying
of your data center and IT equipment. The API comes with an interactive query
application for exploration of the Synse API and provides a simple interface for
common integrations.

It also provides a [Prometheus][prometheus] exporter for the metrics being provided
by your data center and IT equipment. You can use this, in conjunction with Prometheus
to monitor these metrics and alert on them.

## synse-cli

[synse-cli][synse-cli] provides a command line interface to the underlying synse
components. It allows for real-time queries and interaction with the physical and
virtual devices managed by Synse Server and its plugins. It is meant to have feature
parity with the Synse Server API.


[synse-server]: https://github.com/vapor-ware/synse-server
[synse-graphql]: https://github.com/vapor-ware/synse-graphql
[synse-cli]: https://github.com/vapor-ware/synse-cli
[prometheus]: https://prometheus.io/
[grafana]: https://grafana.com/
[monitoring-with-synse]: https://drive.google.com/file/d/0B9jWZzNsJ7juUlN6WHVwS2pqcDQ/view
