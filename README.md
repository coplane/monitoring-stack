# Monitoring Stack

This directory contains a complete, containerized monitoring stack using Docker Compose. It is designed to collect, store, and visualize metrics and logs from your applications.

## Overview

The stack is built around the OpenTelemetry (OTel) standard for collecting telemetry data. Applications instrumented with OTel can send their metrics and logs to a central collector, which then forwards them to the appropriate backend for storage and analysis.

## Components

- **Grafana**: The visualization layer. It's pre-configured with datasources for Prometheus and Loki, allowing you to create dashboards for your metrics and logs.
- **Prometheus**: A time-series database that scrapes and stores metrics.
- **Loki**: A log aggregation system designed to be cost-effective and easy to operate.
- **OTel Collector**: Receives telemetry data (metrics, logs) from your applications via OTLP, processes it, and exports it to Prometheus and Loki.

## Architecture

1.  Your application, instrumented with an OpenTelemetry SDK, sends metrics and logs to the **OTel Collector** over OTLP (gRPC on port `4317`).
2.  The **OTel Collector** receives the data.
    -   Metrics are processed and exposed on an internal endpoint (`:8889`).
    -   Logs are processed and pushed to **Loki**.
3.  **Prometheus** is configured to scrape the metrics endpoint of the **OTel Collector**.
4.  **Grafana** is connected to **Prometheus** (for metrics) and **Loki** (for logs) as data sources.
5.  You can access the **Grafana** UI to query and visualize your application's telemetry.

## Getting Started

### Prerequisites

-   Docker
-   Docker Compose

### 1. Running the Stack

To start all the services, run the following command from the `monitoring-stack` directory:

```
docker compose up -d
```

This will start all the containers in the background.

### 2. Configuring Your Application

To send telemetry data to this stack, you need to configure your application's OpenTelemetry SDK to export to the OTel Collector. Set the OTLP gRPC endpoint to:

`http://127.0.0.1:4317`

Ensure your application is instrumented to produce metrics and logs.

With planar,  set `otel_collector_endpoint: http://127.0.0.1:4317` in your planar config.

### 3. Accessing Grafana

Once the stack is running, you can access the Grafana web interface:

-   **URL**: `http://localhost:3000`
-   **Username**: `admin`
-   **Password**: `admin`

The Prometheus and Loki datasources are pre-configured. You can start building dashboards or use the "Explore" view to query your data.

-   To see your logs, choose the `Logs` datasource in the Explore view.
-   To see your metrics, choose the `Metrics` datasource in the Explore view.

### 4. Stopping the Stack

To stop all the services and remove the containers, run:

```
docker compose down
```

To stop the services and also remove the data volumes (resetting Prometheus, Loki, and Grafana), run:

```
docker compose down -v
```
