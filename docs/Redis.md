#### Setting Up Redis with Docker

Before executing Redis commands, create a custom Docker network:

```bash
docker network create some-network
```

#### Docker Volumes for Persistent Storage

Create a Docker volume named `redis-data` for storing Redis data persistently:

```bash
docker volume create redis-data
```

#### Pull Redis Image

Pull the Redis image from Docker Hub:

```bash
docker pull redis
```

#### Starting Redis Server

Start the Redis server container with the volume attached:

```bash
docker run -d --name practice_redis --network some-network -v redis-data:/data redis
```

For Redis Stack deployment (including GUI access on ports 6379 and 8001):

```bash
docker run -d --name practice_redis -p 6379:6379 -p 8001:8001 --network some-network -v redis-data:/data redis/redis-stack
```

#### Connecting to Redis Server with Redis CLI

Connect to the Redis server using the Redis CLI in another container:

```bash
docker run -it --network some-network --rm redis redis-cli -h practice_redis
```

#### Redis Commands Reference

- **Store Data**: `HSET key field value`
- **Retrieve Data**: `HGETALL key`, `HGET key field`
- **Delete Data**: `HDEL key field`
- **Set Auto Expiry (TTL)**:
  - Set expiry time: `EXPIRE key 10` (sets key to expire in 10 seconds)
  - Check time to live (TTL): `TTL mykey`

#### Data Persistence

Redis data stored in the `redis-data` volume persists across container restarts.

### Notes

Ensure the Docker network (`some-network` in this example) is appropriately substituted with your desired network name. Adjust ports (`6379`, `8001`) and volume names (`redis-data`) based on your deployment requirements and configurations.