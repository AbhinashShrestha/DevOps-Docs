#### Setting Up UptimeKuma with Docker

Run UptimeKuma Docker container:

```bash
docker run -d --restart=always \
    -p 3001:3001 \
    -v uptime-kuma:/app/data \
    --name ore-no-kuma \
    louislam/uptime-kuma:1
```

#### Access Credentials for UptimeKuma

- Username: `kumakuma`
- Password: `OreNoKuma8`

### Notes

- Replace placeholders (`3001`, `uptime-kuma`, `ore-no-kuma`, etc.) with your preferred ports and container names.
- Ensure Docker is properly installed and configured on your host machine.
- Customize volumes and configurations as per your specific setup and requirements.