# Local Docker Registry
## Installation

Setting up a local Docker registry is a straightforward process. Docker provides an official image for the Docker Registry, which you can run as a container. Here are the steps to set up a local Docker registry:

### Steps to Set Up a Local Docker Registry

1. **Pull the Docker Registry Image**:
   - Pull the official Docker Registry image from Docker Hub.
     ```sh
     docker pull registry:2
     ```

2. **Run the Docker Registry Container**:
   - Run the Docker Registry container. This will start the registry on port 5000 by default.
     ```sh
     docker run -d -p 5000:5000 --name local-registry registry:2
     ```

3. **Tag and Push an Image to the Local Registry**:
   - Tag an existing Docker image to push it to your local registry.
     ```sh
     docker tag <your-image> localhost:5000/<your-image>
     ```
   - Push the tagged image to the local registry.
     ```sh
     docker push localhost:5000/<your-image>
     ```

4. **Pull the Image from the Local Registry**:
   - Pull the image from your local registry to verify that it works.
     ```sh
     docker pull localhost:5000/<your-image>
     ```

### Example Commands

1. **Pull the Docker Registry Image**:
   ```sh
   docker pull registry:2
   ```

2. **Run the Docker Registry Container**:
   ```sh
   docker run -d -p 5000:5000 --name local-registry registry:2
   ```

3. **Tag and Push an Image to the Local Registry**:
   - Tag an existing image (e.g., `nginx`):
     ```sh
     docker tag nginx localhost:5000/nginx
     ```
   - Push the tagged image to the local registry:
     ```sh
     docker push localhost:5000/nginx
     ```

4. **Pull the Image from the Local Registry**:
   ```sh
   docker pull localhost:5000/nginx
   ```

### Verify the Setup

1. **List Images in the Local Registry**:
   - You can use the Docker Registry HTTP API to list the images in your local registry.
     ```sh
     curl http://localhost:5000/v2/_catalog
     ```

2. **List Tags for an Image**:
   - List the tags for a specific image in your local registry.
     ```sh
     curl http://localhost:5000/v2/nginx/tags/list
     ```

### Example Output

- **List Images in the Local Registry**:
  ```sh
  curl http://localhost:5000/v2/_catalog
  ```
  ```json
  {
    "repositories": [
      "nginx"
    ]
  }
  ```

- **List Tags for an Image**:
  ```sh
  curl http://localhost:5000/v2/nginx/tags/list
  ```
  ```json
  {
    "name": "nginx",
    "tags": [
      "latest"
    ]
  }
  ```

### Cleanup

To stop and remove the local Docker registry container:
```sh
docker stop local-registry
docker rm local-registry
```

By following these steps, you can set up a local Docker registry quickly and easily, allowing you to push and pull Docker images locally.


## Use Local Registry in Local Kubernetes Development Node

To use a local Docker registry in your Kubernetes Pod definition, you need to ensure that your Kubernetes nodes can access the local registry. This typically involves configuring the Docker daemon on each node to trust the local registry and then referencing the images from the local registry in your Pod manifest.

### Steps to Use Local Docker Registry in Kubernetes

1. **Configure Docker Daemon to Trust the Local Registry**:
   - On each Kubernetes node, configure the Docker daemon to trust the local registry. Create or edit the `/etc/docker/daemon.json` file to include the local registry as an insecure registry.
     ```json
     {
       "insecure-registries": ["localhost:5000"]
     }
     ```
   - Restart the Docker daemon to apply the changes.
     ```sh
     sudo systemctl restart docker
     ```

2. **Tag and Push an Image to the Local Registry**:
   - Tag an existing Docker image to push it to your local registry.
     ```sh
     docker tag nginx localhost:5000/nginx
     ```
   - Push the tagged image to the local registry.
     ```sh
     docker push localhost:5000/nginx
     ```

3. **Reference the Image in Your Kubernetes Pod Manifest**:
   - Update your Kubernetes Pod manifest to reference the image from the local registry.

### Example Kubernetes Pod Manifest

Here is an example of a Kubernetes Pod manifest that uses an image from the local Docker registry:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-local
spec:
  containers:
  - name: nginx
    image: localhost:5000/nginx
    ports:
    - containerPort: 80
```

### Steps to Deploy the Pod

1. **Apply the Pod Manifest**:
   - Save the above manifest to a file, e.g., `nginx-local.yaml`.
   - Apply the manifest to your Kubernetes cluster.
     ```sh
     kubectl apply -f nginx-local.yaml
     ```

2. **Verify the Pod Status**:
   - Check if the Pod is running.
     ```sh
     kubectl get pods
     ```

### Example Commands

1. **Configure Docker Daemon**:
   - Edit the Docker daemon configuration.
     ```sh
     sudo nano /etc/docker/daemon.json
     ```
   - Add the following content:
     ```json
     {
       "insecure-registries": ["localhost:5000"]
     }
     ```
   - Restart Docker.
     ```sh
     sudo systemctl restart docker
     ```

2. **Tag and Push an Image**:
   ```sh
   docker tag nginx localhost:5000/nginx
   docker push localhost:5000/nginx
   ```

3. **Apply the Pod Manifest**:
   ```sh
   kubectl apply -f nginx-local.yaml
   ```

4. **Verify the Pod Status**:
   ```sh
   kubectl get pods
   ```

### Cleanup

To delete the Pod and clean up resources:
```sh
kubectl delete -f nginx-local.yaml
```

By following these steps, you can use your local Docker registry in your Kubernetes Pod definition, allowing you to deploy images from the local registry to your Kubernetes cluster.


## Persist Docker Registry

Yes, Docker Registry 2 can be configured to persist data by setting up **persistent storage** for the image layers and metadata. Here's how you can achieve that:

### 1. **Using File System for Persistence**
   - You can configure Docker Registry 2 to store images and data on a local file system.
   - In the registry's configuration file (`config.yml`), specify the path where you want the registry to store data.

   Example configuration (`config.yml`):
   ```yaml
   version: 0.1
   storage:
     filesystem:
       rootdirectory: /var/lib/registry
   ```

   When you run the Docker Registry, all images and metadata will be stored in `/var/lib/registry` or whichever directory you specify.

### 2. **Using External Storage (S3, GCS, Azure Blob, etc.)**
   Docker Registry 2 can also store images on **cloud storage** such as Amazon S3, Google Cloud Storage (GCS), or Azure Blob Storage.

   Example S3 configuration (`config.yml`):
   ```yaml
   version: 0.1
   storage:
     s3:
       accesskey: your-access-key
       secretkey: your-secret-key
       region: your-region
       bucket: your-bucket-name
   ```

   This ensures that all your Docker images are persisted in your S3 bucket, making the registry more durable and scalable.

### 3. **Using Docker Volumes**
   - When running the Docker Registry container, you can use Docker volumes to persist the data:
     ```bash
     docker run -d -p 5000:5000 --name registry \
     -v /path/to/persistent/data:/var/lib/registry \
     registry:2
     ```
   - This will mount your local directory to the container, ensuring that data is persisted even if the container is removed.

### 4. **Using Kubernetes (Persistent Volume Claims)**
   - If running Docker Registry in Kubernetes, you can use Persistent Volume Claims (PVC) to store the registry data persistently.

By setting up persistent storage, Docker Registry 2 will store images and metadata reliably, allowing for safe restarts and scalability.