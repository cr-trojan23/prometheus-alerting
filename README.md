## Docker Compose File Documentation

This documentation provides an overview of a Docker Compose file that sets up multiple Prometheus instances, Alertmanager, and Node Exporter. The file defines a collection of services, each running in its own container.

### Services

#### Prometheus Instances

The Docker Compose file configures three Prometheus instances, each with a unique name, image, volume mount, and port mapping.

- `prometheus1`: This service sets up the first Prometheus instance. It uses the `prom/prometheus` image and container name `prometheus_container1`. The configuration file `prometheus.yml` is mounted from the local `./prometheus1` directory to `/etc/prometheus` inside the container. The service is accessible through port `9090`.

- `prometheus2`: The second Prometheus instance is similar to `prometheus1`, but with different container name, mount directory, and port. It uses the `prom/prometheus` image, container name `prometheus_container2`, mounts `./prometheus2` to `/etc/prometheus`, and exposes port `9091`.

- `prometheus3`: The third Prometheus instance follows the same pattern as the previous ones. It utilizes the `prom/prometheus` image, container name `prometheus_container3`, mounts `./prometheus3` to `/etc/prometheus`, and exposes port `9092`.

#### Alertmanager

The Docker Compose file also includes the configuration for the Alertmanager service, responsible for handling alerts generated by Prometheus.

- `alertmanager`: This service uses the `prom/alertmanager` image and container name `alertmanager`. The configuration file `alertmanager.yml` is mounted from the local `./alertmanager` directory to `/etc/alertmanager` inside the container. The service is accessible through port `9093`.

#### Node Exporter

Additionally, the Docker Compose file sets up the Node Exporter service, which collects metrics from the host system.

- `node-exporter`: This service uses the `prom/node-exporter` image and container name `node_exporter`. It mounts various directories from the host system to provide access to system metrics. Specifically, it mounts `/proc` to `/host/proc`, `/sys` to `/host/sys`, and `/` (root filesystem) to `/rootfs`. The service is accessible through port `9100`.

### Usage

To use this Docker Compose file, perform the following steps:

1. Create a new directory on your local machine.
2. Save the Docker Compose code provided above into a file named `docker-compose.yml` within the newly created directory.
3. Customize the configuration files for Prometheus (`prometheus.yml`), Alertmanager (`alertmanager.yml`), and any other relevant components to suit your needs.
4. Open a terminal or command prompt and navigate to the directory containing the Docker Compose file.
5. Run the command `docker-compose up -d` to start the containers in detached mode.
6. You can now access the services based on their respective ports mentioned in the Docker Compose file.

## Alertmanager Configuration

The Alertmanager configuration in the Docker Compose file defines how alerts generated by Prometheus are routed and which receivers handle them. It also specifies the configuration for each receiver, including email and Slack notification settings.

### Route Configuration

The route configuration determines how alerts are routed based on their matchers and grouping rules.

- `repeat_interval: 5m`: This setting specifies the interval at which the entire route configuration is repeated.

- `receiver: 'mail'`: The default receiver for all alerts is set to `mail`. Alerts will be sent to the receiver specified with this name.

- `routes`: This section contains a list of route rules for specific matchers.

  - `matchers`: Matchers are conditions used to select alerts for a specific route rule.

  - `receiver: 'mail'`: The receiver specified by this name will handle alerts that match the defined matchers.

  - `group_by: ['alertname']`: This setting groups alerts based on the specified labels. In this example, alerts with the same `alertname` will be grouped together.

  - `repeat_interval: 2m`: Overrides the default repeat interval for this specific route rule.

  - `continue: true`: Specifies that the next route rule should also be evaluated for matching alerts.

### Receiver Configuration

The receiver configuration defines the settings for handling and sending alerts to different notification channels.

- `name: 'mail'`: The receiver name used in the route configuration to specify which receiver to use for specific alerts.

- `email_configs`: Configuration for sending alerts via email.

  - `smarthost: 'smtp.gmail.com:587'`: Specifies the SMTP server address and port for sending emails.

  - `from: 'your_email@gmail.com'`: The email address from which the alert notifications will be sent.

  - `to: 'your_email@gmail.com'`: The email address to which the alert notifications will be sent.

  - `auth_identity: 'your_email@gmail.com'`: The email address used for authentication.

  - `auth_username: 'your_email@gmail.com'`: The username for authentication, which should be the same as the `auth_identity` value.

  - `auth_password: 'insert_google_app_password'`: The application-specific password generated for your Google account to authenticate the email sending.

  - `require_tls: true`: Specifies that a TLS connection is required for sending emails.

- `name: 'slack'`: The receiver name used in the route configuration to specify which receiver to use for specific alerts.

- `webhook_configs`: Configuration for sending alerts to a Slack channel via webhook.

  - `url: "https://example.com"`: The URL of the Slack webhook endpoint.

  - `send_resolved: true`: Determines whether resolved alerts should also be sent to Slack.

## Prometheus Configuration

The Prometheus configuration in the Docker Compose file defines the global settings, scrape configurations, alerting configuration, and rule files.

### Global Configuration

- `scrape_interval: 15s`: This setting specifies the interval at which Prometheus scrapes metrics from the configured targets.

### Scrape Configurations

Scrape configurations define the targets from which Prometheus collects metrics.

- `job_name: 'prometheus'`: This scrape configuration is for the Prometheus server itself.

  - `static_configs`: This section contains a list of static targets to be scraped.

    - `targets: ['prometheus1:9090', 'prometheus2:9090', 'prometheus3:9090']`: Specifies the addresses of the Prometheus instances to be scraped. In this example, three instances with the given addresses will be scraped for metrics.

- `job_name: 'node_exporter'`: This scrape configuration is for the Node Exporter service.

  - `metrics_path: '/metrics'`: Specifies the endpoint path where Node Exporter exposes its metrics.

  - `static_configs`: This section contains a list of static targets to be scraped.

    - `targets: ['node_exporter:9100']`: Specifies the address of the Node Exporter instance to be scraped.

### Alerting Configuration

The alerting configuration defines the alertmanager's configuration and the targets it should forward alerts to.

- `alertmanagers`: This section contains the configuration for the Alertmanager service.

  - `static_configs`: This section contains a list of static targets to which alerts will be forwarded.

    - `targets: ['alertmanager:9093']`: Specifies the address of the Alertmanager instance to which alerts will be forwarded.

### Rule Files

- `rule_files: - '/etc/prometheus/rules/*.rules'`: Specifies the location of rule files containing alerting and recording rules.





