## Step 1: Set Up Storage

1. **Create a Persistent Volume (PV):**
   - Define a PV to store data persistently across pod restarts.
   - See `openwebui-pv.yaml` for the configuration.

2. **Create a Persistent Volume Claim (PVC):**
   - Request storage for your pods using a PVC.
   - See `openwebui-pvc.yaml` for the configuration.

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