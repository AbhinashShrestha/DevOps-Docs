To set up Horizontal Pod Autoscaling (HPA) in Kubernetes, you'll need to follow these steps. Below, I'll outline how to create a deployment, expose it as a service, set up autoscaling, and simulate load.

### Step 1: Create a Deployment and Service

1. **Create a Deployment (`php-apache`):**

   Save the following YAML configuration to a file named `php-apache-deployment.yaml`.

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: php-apache
     labels:
       app: php-apache
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: php-apache
     template:
       metadata:
         labels:
           app: php-apache
       spec:
         containers:
         - name: php-apache
           image: php:7.4-apache
           ports:
           - containerPort: 80
   ```

   Apply the deployment to your cluster:

   ```bash
   kubectl apply -f php-apache-deployment.yaml -n custom-namespace
   ```

2. **Expose Deployment as a Service:**

   Save the following YAML configuration to a file named `php-apache-service.yaml`.

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: php-apache
   spec:
     ports:
     - port: 80
       targetPort: 80
     selector:
       app: php-apache
   ```

   Apply the service to your cluster:

   ```bash
   kubectl apply -f php-apache-service.yaml -n custom-namespace
   ```

### Step 2: Configure Horizontal Pod Autoscaler (HPA)

1. **Create Horizontal Pod Autoscaler:**

   Run the following command to create an HPA for the `php-apache` deployment:

   ```bash
   kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=3 -n custom-namespace
   ```

   This command sets up autoscaling for the deployment based on CPU utilization, scaling between 1 and 3 replicas.

### Step 3: Simulate Load

1. **Simulate Load with a Load Generator:**

   Run the following command to simulate load on the `php-apache` service:

   ```bash
   kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache.custom-namespace.svc.cluster.local; done"
   ```

   Replace `custom-namespace` with your actual namespace if it's different.

### Explanation:

- **Deployment and Service:** The `php-apache` deployment runs a PHP Apache server with one replica initially.
- **Horizontal Pod Autoscaler (HPA):** Autoscaling is configured to scale between 1 and 3 replicas based on CPU utilization (`--cpu-percent=50`).
- **Load Simulation:** The load generator continuously makes requests to the `php-apache` service, simulating traffic that triggers autoscaling based on CPU load.

By following these steps, you can effectively set up Horizontal Pod Autoscaling in Kubernetes, allowing your deployments to dynamically scale based on CPU metrics to handle varying workloads. Adjust the configuration and parameters according to your specific application requirements and cluster setup.