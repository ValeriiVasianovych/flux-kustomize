# Helm Releases

Эта директория содержит Helm релизы, которые автоматически применяются через Flux GitOps.

## Структура

```
helm-releases/
├── nginx/
│   └── helmrelease.yaml      # HelmRelease для nginx-ingress
├── grafana/
│   └── helmrelease.yaml      # HelmRelease для Grafana
└── README.md                 # Этот файл
```

## Автоматическое применение

Каждый Helm релиз автоматически применяется через соответствующий Kustomization в `.flux/kustomizations/`:

- **nginx**: `.flux/kustomizations/nginx.yaml`
- **grafana**: `.flux/kustomizations/grafana.yaml`

## Настройки

### Nginx Ingress Controller
- Namespace: `nginx`
- Версия: `4.7.1`
- Тип сервиса: `LoadBalancer`
- IngressClass: `nginx` (по умолчанию)

### Grafana
- Namespace: `grafana`
- Версия: `9.2.10`
- Тип сервиса: `LoadBalancer`
- Persistence: `10Gi`
- Admin пароль: `admin`

## Изменения

Для внесения изменений в любой из релизов:
1. Отредактируйте соответствующий `helmrelease.yaml` файл
2. Закоммитьте изменения в Git
3. Flux автоматически применит изменения через 30 секунд 