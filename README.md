# Dockerized Prometheus Monitoring Stack

This project provides a Dockerized monitoring stack with **Prometheus**, **Grafana**, **Blackbox Exporter**, and **Node Exporter** for monitoring infrastructure and application performance.

---

## Prerequisites

1. **Install Docker and Docker Compose**:
   - Ensure Docker and Docker Compose are installed on your system:
     - [Docker installation guide](https://docs.docker.com/get-docker/)
     - [Docker Compose installation guide](https://docs.docker.com/compose/install/)

2. **Create Required Directories**:
   Create the following directories to persist data:
   ```bash
   mkdir -p grafana-data
   mkdir -p prom-data
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

### 1. Clone the Repository
```bash
git clone <repository-url>
cd Dockerized-Prometheus-Stack
```

### 2. Start the Stack
Use Docker Compose to start the stack:
```bash
docker-compose up -d
```

### 3. Verify Services
- **Prometheus**: [http://localhost:9090](http://localhost:9090)
- **Blackbox Exporter**: [http://localhost:9115](http://localhost:9115)
- **Grafana**: [http://localhost:3000](http://localhost:3000)

### 4. Default Grafana Credentials
- **Username**: `admin`
- **Password**: `123456`

---

## Securing File and Folder Permissions

To ensure secure access to configuration files and data directories, run the following commands:

### Configuration Files
```bash
chown root:root blackbox.yml prometheus.yml compose.yml
chmod 640 blackbox.yml prometheus.yml
```

### Data Directories
```bash
chown -R 65534:65534 prom-data
chmod -R 700 prom-data

chown -R 472:472 grafana-data
chmod -R 700 grafana-data
```

---

## Setting Up Node Exporter on Hosts

To monitor host metrics, set up the Prometheus Node Exporter on your hosts:

1. **Create a Node Exporter User**:
   ```bash
   sudo useradd --no-create-home --shell /bin/false node_exporter
   ```

2. **Download Node Exporter**:
   ```bash
   wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
   ```

3. **Extract and Move the Binary**:
   ```bash
   sudo tar xvf node_exporter-1.9.1.linux-amd64.tar.gz
   sudo mv node_exporter-1.9.1.linux-amd64/node_exporter /usr/local/bin/
   sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
   sudo rm -rf node_exporter-1.9.1.linux-amd64*
   ```

4. **Create a Systemd Service File**:
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

5. **Enable and Start the Service**:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable node_exporter.service
   sudo systemctl start node_exporter.service
   ```

6. **Verify the Service**:
   Check the status of the Node Exporter:
   ```bash
   sudo systemctl status node_exporter.service
   ```

   The Node Exporter will now be running and accessible on port `9100`.

---

## Best Practices

1. **Secure Grafana**:
   - Change the default Grafana admin password after the first login.
   - Use strong passwords or enable SSO.

2. **Restrict Network Access**:
   - Use a reverse proxy (e.g., NGINX) to secure access to services.
   - Restrict access to ports using firewall rules.

3. **Backup Data**:
   - Regularly back up the `grafana-data/` and `prom-data/` directories.

4. **Monitor Logs**:
   - Use `docker-compose logs -f` to monitor logs for troubleshooting.

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
