# Server Log Analyzer and Monitoring Suite

This stack provides log collection, system monitoring, dashboards and alerting using Docker Compose.

## Components
- **syslog-ng** – receives system logs via TCP/UDP and forwards them to Loki
- **Loki** – stores aggregated logs
- **Prometheus** – scrapes metrics from exporters and evaluates alert rules
- **Node Exporter** – exposes host metrics (CPU, memory, disk, etc.)
- **cAdvisor** – collects container metrics
- **Alertmanager** – handles alerts from Prometheus
- **Grafana** – dashboards for logs and metrics

## Usage
1. Ensure Docker and Docker Compose are installed on the host.
2. Clone this repository on the host machine with access to `/var/log`.
3. Run `docker-compose up -d` from the repository root.
4. Visit Grafana at [http://localhost:3000](http://localhost:3000) (default credentials `admin/admin`).
5. Prometheus is available at [http://localhost:9090](http://localhost:9090).
6. Logs are collected from `/var/log` on the host; view them in Grafana under *Explore* using the `Loki` datasource.

## Checking Login Attempts
- Navigate to Grafana > Explore and select the `Loki` datasource.
- Query `{program="sshd"}` to see SSH login events including failures.
- Failed login counts trigger the `ExcessiveLoginFailures` alert rule in Prometheus.

## Adding Alert Rules
- Edit `prometheus/alert.rules.yml` and add new rules under the `system-alerts` group.
- Reload Prometheus configuration by restarting the Prometheus container: `docker-compose restart prometheus`.

## Log Rotation
Loki stores data on the `loki-data` volume. Adjust retention or set up periodic cleanup as required.

## Network
All services communicate on the `lognet` network defined in `docker-compose.yml`.
