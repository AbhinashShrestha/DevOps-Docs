To set up Grafana, Loki, and Promtail for logging, follow these steps:
### Installation and Setup on Server (192.168.56.8)

1. **Install Grafana:**

   Follow the [official Grafana installation guide](https://grafana.com/docs/grafana/latest/setup/grafana-installation/redhat-rhel-fedora/) for Red Hat based systems:

   ```bash
   sudo dnf install https://dl.grafana.com/oss/release/grafana-8.0.7-1.x86_64.rpm
   sudo systemctl start grafana-server
   sudo systemctl enable grafana-server
   ```

   Access Grafana at `http://localhost:3000` and log in with:

   - **User:** admin
   - **Password:** logging

2. **Install Loki and Promtail:**

   ```bash
   sudo dnf install loki promtail
   ```

3. **Configure Promtail to Access Logs:**

   Ensure Promtail has the necessary permissions:

   ```bash
   sudo usermod -a -G adm promtail
   sudo usermod -a -G systemd-journal promtail
   ```

   Open port 3100 for Promtail:

   ```bash
   sudo firewall-cmd --permanent --add-port=3100/tcp
   sudo firewall-cmd --reload
   ```

   Set ACLs for Promtail to access log files:

   ```bash
   sudo setfacl -R -m u:promtail:rX /var/log
   ```

### Client Setup (Remote Location)

1. **Install Promtail on Client:**

   Download and install Promtail on the client machine:

   ```bash
   sudo dnf localinstall promtail-2.9.8.x86_64.rpm
   ```

2. **Configure Promtail Client:**

   Edit the `config.yml` file located in `/etc/promtail` on the client:

   ```yaml
   clients:
     - url: http://192.168.56.8:3100/loki/api/v1/push
   ```

   Replace `192.168.56.8` with the actual IP address of your Grafana/Loki server.

### Verify Setup

1. **Start and Enable Services:**

   Ensure all services are running and enabled on both the server and client:

   ```bash
   # On the server
   sudo systemctl start grafana-server
   sudo systemctl enable grafana-server

   # On both server and client
   sudo systemctl start loki
   sudo systemctl enable loki
   sudo systemctl start promtail
   sudo systemctl enable promtail
   ```

2. **Access Grafana Dashboard:**

   Open a web browser and navigate to `http://192.168.56.8:3000` (replace with your server's IP) to access Grafana. Log in with the credentials provided earlier (`admin` / `mirumonit`).

3. **View Logs:**

   Use Grafana to configure dashboards and explore logs ingested by Loki through Promtail.

### Notes

- Ensure network connectivity and firewall rules allow communication between Promtail clients and the Loki server.
- Adjust configurations (`config.yml`, ACLs, etc.) based on your specific setup and security requirements.
- Monitor logs (`/var/log/grafana`, `/var/log/loki`, `/var/log/promtail`) for troubleshooting and debugging purposes.
