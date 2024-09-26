[Official Doc](https://argo-cd.readthedocs.io/en/stable/operator-manual/installation/)

To proceed with configuring Argo CD and creating an application (`guestbook` in this case), follow these steps: 
 
### Step 1: Login to Argo CD

1. **Retrieve Initial Admin Password:**

   Use `kubectl` to fetch the initial admin password for Argo CD:

   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```

   This command decodes and displays the initial admin password.

   Example:
   ```
   $ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   YXJnb21lcm8K
   ```

   Use the decoded password (`argomero` in this case) to log in to the Argo CD dashboard.

2. Expose the service to be available 
		It needs to be either Load Balancer (need Metallb( or Node Port
```

kubectl get pods,svc,deployment -n argocd -o wide

argocd           pod/argocd-server-77f69c9d6-6vxbx                       1/1     Running     0             26m   10.42.1.8       node1.mero.com    <none>           <none>
```
		
1. **Login to Argo CD Dashboard:**

   Open your web browser and navigate to the Argo CD UI using its service URL. For example, if Argo CD is exposed as a NodePort service:

   ```
   https://ip_where the argocd server is running in k3s cluster (HA cluster)
   ```

   Use the following credentials to log in:

   - **Username:** admin
   - **Password:** The decoded password from the previous step (`argomero`)

### Step 2: Create an Application

1. **Create an Application (`guestbook`):**

   Use the `argocd app create` command to create an application named `guestbook`:

   ```bash
   argocd app create guestbook \
     --repo https://github.com/argoproj/argocd-example-apps.git \
     --path guestbook \
     --dest-server https://kubernetes.default.svc \
     --dest-namespace default
   ```

   This command creates an Argo CD application named `guestbook`, pulling its configuration from the specified Git repository (`https://github.com/argoproj/argocd-example-apps.git`) and deploying it to the Kubernetes cluster (`https://kubernetes.default.svc`) in the `default` namespace.

2. **Monitor Application Deployment:**

   Once the application is created, you can monitor its deployment status in the Argo CD dashboard. It will automatically synchronise with the Git repository and deploy the application components to your Kubernetes cluster.

### Step 3: Encode a Password for Configuration

1. **Encode Password for Configuration Files:**

   If you need to encode a password (`meroPasword`) for use in configuration files or scripts, encode it using `base64`:

   ```bash
	   echo "meroPasword" | base64
   ```

   Example:
   ```
   $ echo "meroPasword" | base64
   bWVyb1Bhc3dvcmQK
   ```

   Use the resulting base64-encoded string (`bWVyb1Bhc3dvcmQK`) where needed in your configuration files or scripts.

### when using private git repo 
https://argo-cd.readthedocs.io/en/stable/user-guide/private-repositories/#self-signed-untrusted-tls-certificates 



### Notes:

- **Argo CD Access:** Ensure that your Kubernetes cluster has network access to the Argo CD service and that the necessary ingress or NodePort configurations are set up correctly.
- **Git Repository:** The `--repo` and `--path` flags in the `argocd app create` command specify the Git repository URL and the path within the repository where the application manifests are located.