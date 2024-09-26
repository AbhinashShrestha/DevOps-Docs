Setting up a multi-master HA K3S cluster using an external MySQL database involves several steps to ensure each master node connects to the shared database and operates correctly. Here's a step-by-step guide based on your requirements:
### Setting Up MySQL Database

1. **Install MySQL**:
   - If MySQL is not already installed, you can run it using Docker for testing purposes:
     ```bash
docker run --name mysql-db -e MYSQL_ROOT_PASSWORD=my-secret-pw -e MYSQL_DATABASE=k3s -p 3306:3306 -d mysql:latest
     ```
   - Connect to MySQL to create a database and a user for K3S:
```bash
docker exec -it mysql-db mysql -uroot -p
```

2. **Create Database and User**:
   ```sql
   CREATE DATABASE k3s;
   USE k3s;
   CREATE USER 'k3s'@'%' IDENTIFIED BY 'thisfork3s';
   GRANT ALL PRIVILEGES ON k3s.* TO 'k3s'@'%';
   FLUSH PRIVILEGES;
   ```

### Installing K3S on the First Master Node

3. **Install K3S on the First Master**:
   - Replace `192.168.56.1` with the actual IP address of your MySQL server and `192.168.56.10` with the IP address of your first master node.
   
```bash
curl -sfL https://get.k3s.io | sh -s - server --datastore-endpoint='mysql://k3s:thisfork3s@tcp(192.168.56.6)/k3s' --tls-san=192.168.56.6
```
   - Retrieve the server node token:
     ```bash
     sudo cat /var/lib/rancher/k3s/server/token
    K103b9e18335faa299d07e1e56fe73a315988db268c289d8131254adfeb0d6b6b4f::server:f82a159b6af9cbbc5920938c45c813cc
     ```

### Installing K3S on the Second Master Node

4. **Install K3S on the Second Master**:
   - Use the token obtained from the first master to join the second master to the cluster:
   ```bash
   curl -sfL https://get.k3s.io | K3S_TOKEN=<token_from_first_master> sh -s - server --datastore-endpoint='mysql://k3s:thisfork3s@tcp(192.168.56.1:3306)/k3s' --tls-san=192.168.56.10 --node-ip=192.168.56.10
```

## Join agents

```
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.56.10:6443 K3S_TOKEN=K10f821333335d7c35d0be9a2181def2b85c2585aaa90c6621e1866c6b4f65084b0::server:d4cdace2643065ad53eb762d0be48491 sh -

```
### Verifying the Cluster

5. **Verify Cluster Status**:
   - Once both master nodes are joined, check the status of nodes in the cluster:
   ```bash
   sudo kubectl get nodes -o wide
   ```

### Summary

- Ensure MySQL is accessible from both master nodes using the correct IP and credentials.
- Use the server node token from the first master to join subsequent master nodes to the cluster.
- Verify connectivity and node status using `kubectl get nodes`.

By following these steps, you should have a multi-master K3S cluster configured with an external MySQL database for shared state persistence. This setup enhances resilience and scalability by distributing control plane responsibilities across multiple nodes.