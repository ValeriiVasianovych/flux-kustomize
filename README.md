# Flux Kustomize Project

A well-structured Kubernetes application deployment using Kustomize and Flux CD.

## Project Structure

```
flux-kustomize/
├── base/                    # Base Kubernetes manifests
│   ├── namespace.yaml       # Base namespace
│   ├── deployment.yaml      # Application deployment
│   ├── service.yaml         # Service definition
│   ├── configmap.yaml       # Base configuration
│   ├── kustomization.yaml   # Base kustomize config
│   └── README.md           # Base documentation
├── overlays/                # Environment-specific configurations
│   ├── dev/                # Development environment
│   │   ├── kustomization.yaml
│   │   ├── namespace-patch.yaml
│   │   ├── replica-patch.yaml
│   │   ├── env-patch.yaml
│   │   ├── configs/
│   │   └── README.md
│   ├── prod/               # Production environment
│   │   ├── kustomization.yaml
│   │   ├── namespace-patch.yaml
│   │   ├── replica-patch.yaml
│   │   ├── env-patch.yaml
│   │   ├── configs/
│   │   └── README.md
│   └── README.md           # Overlays documentation
├── .flux/                  # Flux CD configurations
│   ├── kustomization-dev.yaml
│   └── kustomization-prod.yaml
├── helm/                   # Helm charts (RabbitMQ)
└── README.md              # This file
```

## Quick Start

### Prerequisites
- Kubernetes cluster
- kubectl configured
- Flux CD installed (optional)

### Manual Deployment

```bash
# Development environment
kubectl apply -k overlays/dev

# Production environment
kubectl apply -k overlays/prod
```

### Flux CD Deployment

The project is configured for automatic deployment with Flux CD:

```bash
# Apply Flux configurations
kubectl apply -f .flux/kustomization-dev.yaml
kubectl apply -f .flux/kustomization-prod.yaml
```

## Environment Differences

| Feature | Development | Production |
|---------|-------------|------------|
| Namespace | `nodejs-app-dev` | `nodejs-app-prod` |
| Replicas | 1 | 3 |
| Image Tag | v1 | v2 |
| Team Label | `saas-dev` | `saas-prod` |
| Name Suffix | `-dev` | `-prod` |

## Configuration

### Base Configuration
- Single replica deployment
- ClusterIP service on port 80
- Base configuration map
- Standard labels and annotations

### Environment-Specific Overrides
- Namespace isolation
- Replica count adjustments
- Environment variables
- Configuration maps
- Image tags

## Best Practices

1. **Namespace Isolation**: Each environment has its own namespace
2. **Label Consistency**: Standardized labeling across environments
3. **Configuration Management**: Environment-specific configs via ConfigMaps
4. **Version Control**: Different image tags for different environments
5. **Documentation**: Comprehensive README files for each component

## Maintenance

### Adding New Environments
1. Create new directory in `overlays/`
2. Copy existing environment structure
3. Update namespace and configuration
4. Add Flux configuration if needed

### Updating Base Configuration
1. Modify files in `base/`
2. Test with `kubectl diff -k overlays/dev`
3. Apply changes to all environments

## Troubleshooting

### Common Issues
- **Namespace conflicts**: Ensure unique namespaces per environment
- **Image pull errors**: Verify image tags exist in registry
- **ConfigMap issues**: Check JSON syntax in config files

### Debug Commands
```bash
# Check generated manifests
kubectl kustomize overlays/dev

# Diff changes
kubectl diff -k overlays/dev

# Check Flux status
flux get kustomizations
```