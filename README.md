# Project Structure

```
flux-kustomize/
├── base/                          # Base configuration (shared across environments)
│   ├── configmap.yaml            # Base ConfigMap (currently commented out)
│   ├── deployment.yaml           # Base Deployment
│   ├── kustomization.yaml        # Base kustomization file
│   ├── namespace.yaml            # Base Namespace
│   └── service.yaml              # Base Service
└── overlays/                      # Environment-specific customizations
    ├── dev/                       # Development environment
    │   ├── configs/
    │   │   └── cm-config.json    # Dev-specific configuration
    │   ├── env-patch.yaml        # Environment-specific patches
    │   ├── kustomization.yaml    # Dev kustomization
    │   └── replica-patch.yaml    # Replica count patches
    └── prod/                      # Production environment
        ├── configs/
        │   └── cm-config.json    # Prod-specific configuration
        ├── env-patch.yaml        # Environment-specific patches
        ├── kustomization.yaml    # Prod kustomization
        └── replica-patch.yaml    # Replica count patches
```

## Key Concepts

### 1. Base Configuration
The `base/` directory contains the common Kubernetes manifests that are shared across all environments:
- **Namespace**: Defines the `app` namespace
- **Deployment**: Base Node.js application deployment with 1 replica
- **Service**: Service configuration for the application
- **ConfigMap**: Application configuration (currently commented out)

### 2. Overlays
The `overlays/` directory contains environment-specific customizations:

#### Development Environment (`overlays/dev/`)
- Uses image tag `v1`
- Team label: `saas-001`
- Environment label: `development`
- Custom ConfigMap with dev-specific configuration

#### Production Environment (`overlays/prod/`)
- Uses image tag `v2`
- Team label: `saas-002`
- Environment label: `production`
- Custom ConfigMap with prod-specific configuration

## Usage

### Prerequisites
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installed
- [kustomize](https://kustomize.io/) installed
- Access to a Kubernetes cluster

### Building Manifests

#### Development Environment
```bash
# Build and apply to development cluster
kustomize build overlays/dev | kubectl apply -f -

# Or build to see the generated manifests
kustomize build overlays/dev
```

#### Production Environment
```bash
# Build and apply to production cluster
kustomize build overlays/prod | kubectl apply -f -

# Or build to see the generated manifests
kustomize build overlays/prod
```

### Viewing Differences
```bash
# See what would be applied
kustomize build overlays/dev | kubectl diff -f -

# Compare dev vs prod
diff <(kustomize build overlays/dev) <(kustomize build overlays/prod)
```

## Kustomize Features Demonstrated

### 1. Resource Management
- **Base Resources**: Common manifests in `base/`
- **Resource References**: Overlays reference base using `resources: [../../base]`

### 2. Patches
- **Strategic Merge Patches**: Environment-specific modifications
- **JSON Patches**: Precise modifications to specific fields

### 3. ConfigMap Generation
- **configMapGenerator**: Creates ConfigMaps from files
- **Environment-specific configs**: Different JSON configurations per environment

### 4. Image Tagging
- **Image tags**: Different versions for different environments
- **Version management**: `v1` for dev, `v2` for prod

### 5. Labels and Annotations
- **Common labels**: Applied to all resources
- **Environment-specific labels**: Different team and environment labels
- **Common annotations**: Metadata like owner and version ID

## Configuration Files

### Development Config (`overlays/dev/configs/cm-config.json`)
```json
{
  "environment": "development",
  "debug": true,
  "logLevel": "debug"
}
```

### Production Config (`overlays/prod/configs/cm-config.json`)
```json
{
  "environment": "production",
  "debug": false,
  "logLevel": "info"
}
```