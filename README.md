## Step 1: Set Up Storage

1. **Create a Persistent Volume (PV) for Open WebUI:**
   - Define a PV to store data persistently across pod restarts.
   - See `openwebui-pv.yaml` for the configuration.
   - Ensure the directory `/mnt/data` exists on each node in your cluster. This directory will be used by the Open WebUI PV to store data persistently. You can create it using:
     ```bash
     sudo mkdir -p /mnt/data
     sudo chmod 777 /mnt/data  # Adjust permissions as needed
     ```

2. **Create a Persistent Volume Claim (PVC) for Open WebUI:**
   - Request storage for your pods using a PVC.
   - See `openwebui-pvc.yaml` for the configuration.

3. **Create a Persistent Volume (PV) for Ollama:**
   - Define a PV to store data persistently for Ollama.
   - See `ollama-pv.yaml` for the configuration.
   - Ensure the directory `/mnt/ollama-data` exists on each node in your cluster. This directory will be used by the Ollama PV to store data persistently. You can create it using:
     ```bash
     sudo mkdir -p /mnt/ollama-data
     sudo chmod 777 /mnt/ollama-data  # Adjust permissions as needed
     ```

4. **Create a Persistent Volume Claim (PVC) for Ollama:**
   - Request storage for your pods using a PVC.
   - See `ollama-pvc.yaml` for the configuration.

These directories must exist on all nodes where the pods might be scheduled to ensure data persistence and accessibility.

## Step 2: Deploy Ollama and Open WebUI

1. **Create a Deployment for Ollama:**
   - Define a deployment for Ollama.
   - See `ollama-deployment.yaml` for the configuration.

2. **Create a Deployment for Open WebUI:**
   - Define a deployment for Open WebUI.
   - See `openwebui-deployment.yaml` for the configuration.

## Step 3: Set Up Services

1. **Create a Service for Ollama:**
   - Expose Ollama using a Kubernetes Service.
   - See `ollama-service.yaml` for the configuration.

2. **Create a Service for Open WebUI:**
   - Expose Open WebUI using a Kubernetes Service.
   - See `openwebui-service.yaml` for the configuration.

## Step 4: Set Up RBAC

1. **Create a Role for Ollama:**
   - Define a role for Ollama.
   - See `ollama-role.yaml` for the configuration.

2. **Create a RoleBinding for Ollama:**
   - Bind the role to Ollama.
   - See `ollama-rolebinding.yaml` for the configuration.

## Step 5: Deploy the Applications

- Apply the YAML files using `kubectl`:
  ```bash
  kubectl apply -f openwebui-pv.yaml
  kubectl apply -f openwebui-pvc.yaml
  kubectl apply -f ollama-pv.yaml
  kubectl apply -f ollama-pvc.yaml
  kubectl apply -f ollama-deployment.yaml
  kubectl apply -f openwebui-deployment.yaml
  kubectl apply -f ollama-service.yaml
  kubectl apply -f openwebui-service.yaml
  kubectl apply -f rbac.yaml
  ```

## Step 6: Install MetalLB

1. **Deploy MetalLB**:
   - Install MetalLB using the following command:
     ```bash
     kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml
     ```

2. **Configure MetalLB**:
   - After deploying MetalLB, apply the configuration to define the IP address range:
     ```bash
     kubectl apply -f metallb-config.yaml
     ```

### YAML Files Required:
- **`metallb-native.yaml`**: Used to deploy MetalLB in your cluster.
- **`metallb-config.yaml`**: Defines the IP address range for MetalLB to use.

## Step 7: Integrate Azure Models with Ollama

1. **Deploy LiteLLM Proxy**:
   - Deploy the LiteLLM Proxy in the `openwebui-system` namespace to bridge Azure-hosted models with Ollama's local API.
   - See `litellm-deployment.yaml` for the deployment configuration.

2. **Configure LiteLLM Proxy**:
   - Use a ConfigMap to store the configuration for the LiteLLM Proxy, specifying the Azure model details.
   - See `litellm-configmap.yaml` for the configuration.

3. **Expose LiteLLM Proxy**:
   - Expose the LiteLLM Proxy using a LoadBalancer service with MetalLB to allow external access.
   - See `litellm-service.yaml` for the service configuration.

4. **Update Ollama WebUI**:
   - Configure Ollama WebUI to use the LiteLLM Proxy as the API endpoint, using the external IP provided by MetalLB.

5. **Apply Configurations**:
   - Apply all configurations using `kubectl`:
     ```bash
     kubectl apply -f litellm-deployment.yaml
     kubectl apply -f litellm-configmap.yaml
     kubectl apply -f litellm-service.yaml
     ```

**LiteLLM Summary**:
LiteLLM is a proxy tool that allows integration of Azure-hosted AI models with local applications like Ollama WebUI. It translates Azure AI API requests into a format compatible with OpenAI-style requests, enabling seamless interaction between Azure models and local interfaces. This setup leverages both cloud-based AI services and local deployment capabilities, providing flexibility and scalability in AI model management.

**Communication Summary**:
- **Internal Communication**: Pods communicate using Kubernetes services, which provide DNS resolution and load balancing. The Open WebUI pod interacts with the Ollama pod for model management and with the LiteLLM Proxy for accessing Azure-hosted models.
- **External Communication**: The LiteLLM Proxy requires external access to communicate with Azure AI services, facilitated by a LoadBalancer service using MetalLB. This allows external clients to access the proxy.
- **LoadBalancer Service**: Ensures scalability and reliability by distributing requests across LiteLLM Proxy replicas and providing an external IP for access.

**Persistent Volume Setup**:
- **Ollama PV**: Ensure the directory `/mnt/ollama-data` exists on each node in your cluster. This directory will be used by the Ollama PV to store data persistently. You can create it using:
  ```bash
  sudo mkdir -p /mnt/ollama-data
  sudo chmod 777 /mnt/ollama-data  # Adjust permissions as needed
  ```
- **Open WebUI PV**: Ensure the directory `/mnt/data` exists on each node in your cluster. This directory will be used by the Open WebUI PV to store data persistently. You can create it using:
  ```bash
  sudo mkdir -p /mnt/data
  sudo chmod 777 /mnt/data  # Adjust permissions as needed
  ```

These directories must exist on all nodes where the pods might be scheduled to ensure data persistence and accessibility. 