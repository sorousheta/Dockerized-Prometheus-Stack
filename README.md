# Dockerized Prometheus Monitoring Stack

This project provides a Dockerized monitoring stack with **Prometheus**, **Grafana**, **Blackbox Exporter**, and **Node Exporter** for monitoring infrastructure and application performance.

---

## Prerequisites

1. Install **Docker** and **Docker Compose**:
   - [Docker installation guide](https://docs.docker.com/get-docker/)
   - [Docker Compose installation guide](https://docs.docker.com/compose/install/)

2. Create required directories:
   ```bash
   mkdir -p grafana-data prom-data
   ```

---

## Stack Components

| Component            | Description                          | Default Port | Configuration File       |
|-----------------------|--------------------------------------|--------------|--------------------------|
| **Prometheus**        | Metrics collection and alerting     | `9090`       | `prometheus.yml`         |
| **Grafana**           | Metrics visualization               | `3000`       | N/A                      |
| **Blackbox Exporter** | Endpoint probing                    | `9115`       | `blackbox.yml`           |
| **Node Exporter**     | Host-level metrics collection       | `9100`       | Systemd service file     |

---

## Setup Instructions

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd Dockerized-Prometheus-Stack
   ```

2. Start the stack:
   ```bash
   docker-compose up -d
   ```

3. Verify services:
   - Prometheus: [http://localhost:9090](http://localhost:9090)
   - Blackbox Exporter: [http://localhost:9115](http://localhost:9115)
   - Grafana: [http://localhost:3000](http://localhost:3000)

4. Default Grafana credentials:
   - Username: `admin`
   - Password: `123456`

---

## Configuration

- **Prometheus**: Define scrape targets in `prometheus.yml`. Example:
  ```yaml
  scrape_configs:
    - job_name: 'node_exporter'
      static_configs:
        - targets: ['localhost:9100']
  ```

- **Blackbox Exporter**: Define probes in `blackbox.yml`. Example:
  ```yaml
  modules:
    http_2xx:
      prober: http
      timeout: 5s
      http:
        method: GET
        valid_http_versions: ["HTTP/1.1", "HTTP/2.0"]
        valid_status_codes: [200]
  ```

- **Grafana**: Add Prometheus as a data source via **Configuration > Data Sources** with:
  - **Type**: Prometheus
  - **URL**: `http://prometheus:9090`

---

## Securing File and Folder Permissions

Run the following commands to secure configuration files and data directories:

```bash
# Configuration files
chown root:root blackbox.yml prometheus.yml compose.yml
chmod 640 blackbox.yml prometheus.yml

# Data directories
chown -R 65534:65534 prom-data
chmod -R 700 prom-data

chown -R 472:472 grafana-data
chmod -R 700 grafana-data
```

---

## Setting Up Node Exporter on Hosts

1. Create a Node Exporter user:
   ```bash
   sudo useradd --no-create-home --shell /bin/false node_exporter
   ```

2. Download and install Node Exporter:
   ```bash
   wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
   sudo tar xvf node_exporter-1.9.1.linux-amd64.tar.gz
   sudo mv node_exporter-1.9.1.linux-amd64/node_exporter /usr/local/bin/
   sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
   sudo rm -rf node_exporter-1.9.1.linux-amd64*
   ```

3. Create a systemd service file:
   ```bash
   sudo nano /etc/systemd/system/node_exporter.service
   ```

   Add the following content:
   ```ini
   [Unit]
   Description=Prometheus Node Exporter
   Wants=network-online.target
   After=network-online.target

   [Service]
   User=node_exporter
   Group=node_exporter
   Type=simple
   ExecStart=/usr/local/bin/node_exporter --collector.systemd
   Restart=always
   RestartSec=5

   [Install]
   WantedBy=multi-user.target
   ```

4. Enable and start the service:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable node_exporter.service
   sudo systemctl start node_exporter.service
   ```

5. Verify the service:
   ```bash
   sudo systemctl status node_exporter.service
   ```

---

## Best Practices

1. **Secure Grafana**:
   - Change the default admin password.
   - Use strong passwords or enable SSO.

2. **Restrict Network Access**:
   - Use a reverse proxy (e.g., NGINX) to secure services.
   - Restrict access to ports using firewall rules.

3. **Backup Data**:
   - Regularly back up `grafana-data/` and `prom-data/`.

4. **Monitor Logs**:
   - Use `docker-compose logs -f` for troubleshooting.

5. **Update Images**:
   - Periodically update Docker images:
     ```bash
     docker-compose pull
     docker-compose up -d
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
