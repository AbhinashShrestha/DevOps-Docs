Here is your content formatted for Markdown, strictly following the information you provided:
#### Creating and Editing `docker-compose.yml`

1. Create the `docker-compose.yml` file:
    ```sh
    touch docker-compose.yml
    ```

2. Open `docker-compose.yml` in a text editor (e.g., vim):
    ```sh
    vim docker-compose.yml
    ```

## Running Docker Compose Services

3. Start the reverse proxy (Traefik):
    ```sh
    docker-compose up -d reverse-proxy-traefik
    ```

4. Start the `whoami` service:
    ```sh
    docker-compose up -d whoami
    ```

5. Scale the `whoami` service to 2 instances:
    ```sh
    docker-compose up -d --scale whoami=2
    ```

## Reference

- [Traefik Quick Start Guide](https://doc.traefik.io/traefik/getting-started/quick-start/)

## Hostname Configuration

- Update `/etc/hosts` to use `webapp.docker.localhost` instead of `webapp.localhost`:
    ```
    127.0.0.1 webapp.docker.localhost
    ```
    