# ArgoCD ApplicationSet for Simple Web Application

This directory contains an ArgoCD ApplicationSet configuration to deploy a simple web application.

## Files

- `application-set.yaml` - ArgoCD ApplicationSet manifest
- `manifests/` - Kubernetes manifests for the web application
  - `deployment.yaml` - Deployment for the web app (using nginx)
  - `service.yaml` - Service to expose the web app
  - `kustomization.yaml` - Kustomize configuration

## Setup Instructions

1. **Update the ApplicationSet** with your repository details:
   - Replace `https://github.com/your-org/your-repo.git` with your actual Git repository URL
   - Update `targetRevision` if using a different branch
   - Adjust `path` if your manifests are in a different directory

2. **Apply the ApplicationSet**:
   ```bash
   kubectl apply -f application-set.yaml
   ```

3. **Verify the Application**:
   ```bash
   kubectl get applicationset -n argocd
   kubectl get application -n argocd
   ```

## Customization

### Using a Different Image

To use a custom web application image, update the `image` field in `manifests/deployment.yaml`:
```yaml
image: your-registry/your-app:tag
```

### Adding Ingress

To expose the application externally, add an Ingress manifest in the `manifests/` directory and reference it in `kustomization.yaml`.

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

- The ApplicationSet uses automated sync with self-healing enabled
- Namespaces will be created automatically if they don't exist
- The application will retry up to 5 times on sync failures

