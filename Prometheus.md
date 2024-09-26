### Installation and Configuration[Doc](https://medium.com/@kemalozz/how-to-install-and-configure-prometheus-grafana-on-rhel9-a23085992e6e)

1. **Download and Extract Prometheus:**

   Assuming you've downloaded Prometheus (`prometheus-2.52.0.linux-amd64.tar.gz`), extract it:

   ```bash
   tar xvfz prometheus-2.52.0.linux-amd64.tar.gz
   cd prometheus-2.52.0.linux-amd64/
   ```

2. **Create `prometheus.yml` Configuration File:**

   Create a `prometheus.yml` configuration file in the Prometheus directory:

   ```yaml
   global:
     scrape_interval: 15s
     external_labels:
       monitor: 'codelab-monitor'

   scrape_configs:
     - job_name: 'prometheus'
       scrape_interval: 5s
       static_configs:
         - targets: ['localhost:9090']

     - job_name: 'node'
       static_configs:
         - targets: ['localhost:9100']
   ```

   - `global`: Defines global settings for Prometheus.
   - `scrape_configs`: Specifies what endpoints Prometheus should scrape. In this example:
     - `prometheus` job scrapes Prometheus itself on `localhost:9090`.
     - `node` job scrapes Node Exporter (for system metrics) on `localhost:9100`.

3. **Run Prometheus:**

   Start Prometheus using the configuration file:

   ```bash
   ./prometheus --config.file=prometheus.yml
   ```

   Prometheus will start and begin scraping targets specified in `prometheus.yml`.

### Systemd Service Configuration (Optional)

If you want to run Prometheus as a service managed by systemd:

1. **Create a systemd Service Unit File:**

   Create a file named `/etc/systemd/system/prometheus.service`:

   ```
   [Unit]
   Description=Prometheus Server
   Documentation=https://prometheus.io/docs/introduction/overview/
   After=network-online.target

   [Service]
   User=prometheus
   ExecStart=/path/to/prometheus --config.file=/path/to/prometheus.yml

   [Install]
   WantedBy=multi-user.target

   Replace `/path/to/prometheus` and `/path/to/prometheus.yml` with the actual paths to your Prometheus binary and configuration file.
   ```

2. **Reload systemd and Start Prometheus Service:**

   sudo systemctl daemon-reload
   sudo systemctl start prometheus
   sudo systemctl enable prometheus
   Now, Prometheus will start automatically on system boot and can be managed using systemd commands (`systemctl start/stop/restart prometheus`).

### Access Prometheus UI

Once Prometheus is running, you can access its web interface:

- Open a web browser and go to `http://localhost:9090` (replace `localhost` with your server IP if accessing remotely).

### Notes

- Ensure firewall rules allow access to Prometheus ports (`9090` by default).
- Customize `prometheus.yml` to include additional scrape configurations as per your monitoring targets.
- Monitor logs (`/var/log/prometheus/`) for any issues during startup or operation.
- Adjust configurations and paths based on your specific setup and requirements