# Dockerized Prometheus Stack

This repository contains a Dockerized Prometheus monitoring stack, including Prometheus, Blackbox Exporter, and Grafana. Follow this guide to set up and run the stack effectively.

---

## Prerequisites

1. **Install Docker and Docker Compose**:
   - Ensure Docker and Docker Compose are installed on your system. Refer to the [Docker installation guide](https://docs.docker.com/get-docker/) and [Docker Compose installation guide](https://docs.docker.com/compose/install/).

2. **Create Required Directories**:
   Before running the stack, create the following directories to persist data:
   ```bash
   mkdir -p grafana-data
   mkdir -p prom-data
   ```

---

## Stack Overview

### Services

1. **Prometheus**:
   - Monitors and collects metrics.
   - Configuration file: `prometheus.yml`.
   - Data directory: `prom-data/`.

2. **Blackbox Exporter**:
   - Probes endpoints over HTTP, TCP, ICMP, and DNS.
   - Configuration file: `blackbox.yml`.

3. **Grafana**:
   - Visualizes metrics and dashboards.
   - Data directory: `grafana-data/`.

---

## Configuration

### Prometheus Configuration
- The Prometheus configuration is defined in `prometheus.yml`. Update this file to add or modify scrape targets.

### Blackbox Exporter Configuration
- The Blackbox Exporter configuration is defined in `blackbox.yml`. Update this file to define custom probes.

### Grafana Configuration
- Grafana uses the `grafana-data/` directory to store its database, Dashboards and plugins.

---

## Running the Stack

1. **Clone the Repository**:
   ```bash
   git clone <repository-url>
   cd Dockerized-Prometheus-Stack
   ```

2. **Ensure Required Directories Exist**:
   Verify that the directories listed in the prerequisites are created.

3. **Start the Stack**:
   Use Docker Compose to start the stack:
   ```bash
   docker-compose up -d
   ```

4. **Verify Services**:
   - Prometheus: [http://localhost:9090](http://localhost:9090)
   - Blackbox Exporter: [http://localhost:9115](http://localhost:9115)
   - Grafana: [http://localhost:3000](http://localhost:3000)

5. **Default Grafana Credentials**:
   - Username: `admin`
   - Password: `123456`

---

## Best Practices

1. **Secure Grafana**:
   - Change the default Grafana admin password after the first login.

2. **Backup Data**:
   - Regularly back up the `grafana-data/` and `prom-data/` directories to avoid data loss.

3. **Monitor Logs**:
   - Use `docker-compose logs -f` to monitor logs for troubleshooting.

4. **Update Images**:
   - Periodically update Docker images to ensure you are using the latest versions:
     ```bash
     docker-compose pull
     docker-compose up -d
     ```

---

## Stopping the Stack

To stop the stack, run:
```bash
docker-compose down
```

---

## Troubleshooting

1. **Check Service Health**:
   - Prometheus: `http://localhost:9090/-/healthy`
   - Blackbox Exporter: `http://localhost:9115/-/healthy`
   - Grafana: `http://localhost:3000/login`

2. **Inspect Logs**:
   - Use `docker-compose logs <service-name>` to inspect logs for a specific service.

---


## License

This project is licensed under the MIT License. See the LICENSE file for details.
