# ArgoCD ApplicationSet for Simple Web Application

This directory contains ArgoCD ApplicationSet configurations to deploy a simple web application with namespace management.

## Files

- `application-set.yaml` - ArgoCD ApplicationSet manifest for the web application
- `namespace-application-set.yaml` - ArgoCD ApplicationSet manifest for namespace creation
- `basic-web-app/` - Kubernetes manifests for the web application
  - `deployment.yaml` - Deployment for the web app (using nginx)
  - `service.yaml` - Service to expose the web app (LoadBalancer type)
  - `kustomization.yaml` - Kustomize configuration
- `namespace/` - Namespace manifest
  - `namespace.yaml` - Namespace definition for basic-web-app

## Setup Instructions

1. **Update the ApplicationSets** with your repository details:
   - Replace `https://github.com/your-org/your-repo.git` with your actual Git repository URL
   - Update `targetRevision` if using a different branch
   - Adjust `path` if your manifests are in a different directory

2. **Apply the ApplicationSets** (apply namespace first):
   ```bash
   # Apply namespace ApplicationSet first
   kubectl apply -f namespace-application-set.yaml
   
   # Apply the web application ApplicationSet
   kubectl apply -f application-set.yaml
   ```

3. **Verify the Applications**:
   ```bash
   kubectl get applicationset -n argocd
   kubectl get application -n argocd
   kubectl get svc -n basic-web-app
   ```

## Customization

### Using a Different Image

To use a custom web application image, update the `image` field in `basic-web-app/deployment.yaml`:
```yaml
image: your-registry/your-app:tag
```

### Service Type

The service is configured as `LoadBalancer` type in `basic-web-app/service.yaml`. This will provision an external load balancer (cloud provider dependent). To change to ClusterIP or NodePort, update the `type` field.

### Adding Ingress

To expose the application externally via Ingress, add an Ingress manifest in the `basic-web-app/` directory and reference it in `kustomization.yaml`.

### Multiple Environments

To deploy to multiple environments, modify the ApplicationSet generator:
```yaml
generators:
  - list:
      elements:
        - name: simple-webapp-dev
          namespace: dev
          ...
        - name: simple-webapp-prod
          namespace: prod
          ...
```

## Notes

- The ApplicationSets use automated sync with self-healing enabled
- The namespace ApplicationSet creates the `basic-web-app` namespace
- The web application is deployed to the `basic-web-app` namespace
- The service uses LoadBalancer type for external access
- Applications will retry up to 5 times on sync failures

