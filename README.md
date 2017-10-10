# Overview

Synse is a simple, scalable API for sensing and controlling data center equipment. It is an open standard for highly automated and programmatic operation and monitoring of data centers. It integrates with existing DCIM standards, including IPMI and Redfish, for monitoring IT equipment, and also integrates with critical data center systems, such as power and cooling, to provide a single source of telemetry for data centers of any size. Synse is capable of monitoring and managing thousands of endpoints in both centralized and distributed environments, and is ideally suited for building tools for remote and automated operations.

# Getting Started

- [Monitoring with Synse](tutorial/monitoring.md) - A great way to get an idea of the value that Synse can provide. Ths tutorial walks you through standing up an emulated environment and shows example integrations with [Prometheus][prometheus] and [Grafana][grafana]. We have a [video walkthrough][monitoring-with-synse] if you'd like an overview.

# Components

## synse-server

[synse-server][synse-server] provides a REST API for monitoring and control of data center and IT equipment, including reading sensors and server power control - via. numerous backend protocols such as IPMI, Redfish, SNMP, I2C, RS485, and PLC. The API is easy to integrate into third-party monitoring, management and orchestration providers. It provides a simple, curl-able interface for common and custom devops tasks.

 ## synse-graphql

[synse-graphql][synse-graphql] provides a GraphQL API that enables complex querying of your data center and IT equipment. The API comes with an interactive query application for exploration of the Synse APIs and provides a simple interface for common integrations.

## synse-prometheus

[synse-prometheus][synse-prometheus] provides a [Prometheus][prometheus] exporter for the metrics being provided by your data center and IT equipment. You can use this, in conjunction with Prometheus to monitor these metrics and alert on them.

## synse-cli

[synse-cli][synse-cli] provides a command line interface to the underlying synse components. It allows for real-time queries and interaction with hardware endpoints monitored by synse, and is meant to have feature parity with the synse API.

[synse-server]: https://github.com/vapor-ware/synse-server
[synse-graphql]: https://github.com/vapor-ware/synse-graphql
[synse-prometheus]: https://github.com/vapor-ware/synse-prometheus
[synse-cli]: https://github.com/vapor-ware/synse-cli
[prometheus]: https://prometheus.io/
[grafana]: https://grafana.com/
[monitoring-with-synse]: https://drive.google.com/file/d/0B9jWZzNsJ7juUlN6WHVwS2pqcDQ/view
