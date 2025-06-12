# ai-local-setup

## Prerequisites
- Rancher Desktop installed and running with Kubernetes enabled
- `kubectl` configured to point to the Rancher Desktop cluster
- (Optional) Depending on how localhost could work on your computer you might need to edit the hosts file (generally, this is not the case) Add the following entries to your `/etc/hosts` file to access services locally:
  ```shell
  127.0.0.1 litellm.localhost webui.localhost
  ```

## Deploying LiteLLM and OpenWebUI
Apply both manifests into the `litellm-ns` namespace:
```shell
kubectl apply -f litellm.yaml
kubectl apply -f openwebui.yaml
```
Verify that pods, services, and ingress are up:
```shell
kubectl get all,ingress -n litellm-ns
```

## Accessing the Services
- LiteLLM API:  http://litellm.localhost
- OpenWebUI UI: http://webui.localhost

## Configuring OpenWebUI to Use LiteLLM
Within the OpenWebUI interface:
1. Go to **Settings → Connections**.
2. Click **+**.
   - **URL**: `http://litellm-service:4000/v1`
3. Get API key from LiteLLM and enter that in the API key section.
4. Save

Alternatively, you can configure the OpenWebUI deployment to point to the internal service DNS by editing `openwebui.yaml`:
```yaml
# ...existing openwebui.yaml...
      containers:
      - name: open-webui
        # ...existing container spec...
        env:
        - name: OPENAI_API_BASE
          value: "http://litellm-service:4000/v1"
        # ...existing container spec...
# ...existing openwebui.yaml...
```