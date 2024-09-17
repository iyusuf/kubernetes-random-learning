# How to configure Kubectl to talk to remote Kubernetes node

- Install Kubectl cli.


```bash
# Step 1: Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Step 2: Save kubeconfig file
# Assume you have copied the kubeconfig file to ~/.kube/config

# Step 3: Set KUBECONFIG environment variable
echo 'export KUBECONFIG=~/.kube/config' >> ~/.bashrc
source ~/.bashrc  # or source ~/.zshrc if you use zsh

# Step 4: Verify configuration
kubectl get nodes
```

- Alis k to kubectl

```bash
echo "alias k=kubectl" >> ~/.bashrc
echo "complete -o default -F __start_kubectl k" >> ~/.bashrc
source ~/.bashrc
```
