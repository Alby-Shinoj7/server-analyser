# Server Log Analyzer and Monitoring Suite

This stack provides log aggregation, metrics monitoring and alerting using Docker Compose. Logs are forwarded via **syslog-ng** to **Loki** while metrics are scraped by **Prometheus** for display in **Grafana**.

## Components
- **syslog-ng** – receives system logs via TCP/UDP and forwards them to Loki
- **Loki** – stores aggregated logs
- **Prometheus** – scrapes metrics from exporters and evaluates alert rules
- **Node Exporter** – exposes host metrics (CPU, memory, disk, etc.)
- **cAdvisor** – collects container metrics
- **Alertmanager** – handles alerts from Prometheus
- **Grafana** – dashboards for logs and metrics

### Image Versions
The `docker-compose.yml` file pins each service to a specific version:

- `grafana/loki:2.9.0`
- `balabit/syslog-ng:4.2.0`
- `prom/prometheus:v2.46.0`
- `prom/node-exporter:v1.7.0`
- `gcr.io/cadvisor/cadvisor:v0.47.2`
- `grafana/grafana:10.0.3`
- `prom/alertmanager:v0.26.0`

## Usage
1. Ensure Docker and Docker Compose are installed on the host.
2. Clone this repository on the host machine with access to `/var/log`.
3. Run `docker-compose up -d` from the repository root.
4. Visit Grafana at [http://localhost:3000](http://localhost:3000) (default credentials `admin/admin`).
5. Prometheus is available at [http://localhost:9090](http://localhost:9090).
6. Alertmanager can be reached at [http://localhost:9093](http://localhost:9093).
7. Logs from `/var/log` and remote hosts are collected automatically. View them in Grafana under *Explore* using the `Loki` datasource to see real-time events.

## Checking Login Attempts
- Navigate to Grafana > Explore and select the `Loki` datasource.
- Query `{program="sshd"}` to see SSH login events including failures.

## Adding Alert Rules
- Edit `prometheus/alert.rules.yml` and add new rules under the `system-alerts` group.
- Reload Prometheus configuration by restarting the Prometheus container: `docker-compose restart prometheus`.

## Log Rotation & Retention
Loki stores data on the `loki_data` volume. Adjust retention or set up periodic cleanup as required.

If Grafana shows **"Log volume has not been configured"** or **"Traces not working"**, verify that the Loki data source is provisioned from `grafana/provisioning/datasources` and that the `loki_data` volume is mounted.

## Network
All services communicate on the `lognet` network defined in `docker-compose.yml`.

### Adding a New Log Source
- Send logs via TCP or UDP port **514** to the host running syslog-ng.
- Alternatively mount additional log files inside the syslog-ng container by editing `docker-compose.yml`.

### Customising Alert Rules
- Edit `prometheus/alert.rules.yml` and restart Prometheus with `docker-compose restart prometheus`.

### Configuring Alert Receivers
- Edit `alertmanager/alertmanager.yml` to add your email or Slack details under the `default` receiver.
- Restart Alertmanager with `docker-compose restart alertmanager` for changes to take effect.

### Viewing Real-Time Logs
- Open Grafana and navigate to **Explore**.
- Choose the **Loki** data source and run queries such as `{program="sshd"}` to drill down into login activity.
