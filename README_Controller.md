### Key Concepts

```
                        Cluster-scoped Resources
┌─────────────────────────────────────────────────────────────────┐
│                                                                   │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │             NamespaceClass (development-class)           │   │
│   │  Items: [appconfig-item, networkpolicy-item, ...]       │   │
│   └────────────────┬────────────────────────────────────────┘   │
│                    │ References                                  │
│       ┌────────────┼────────────┬──────────────┐                │
│       ▼            ▼            ▼              ▼                │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐            │
│  │  Item:  │  │  Item:  │  │  Item:  │  │  Item:  │            │
│  │appconfig│  │network  │  │resource │  │  ...    │            │
│  │  -item  │  │policy-  │  │quota-   │  │         │            │
│  │         │  │  item   │  │  item   │  │         │            │
│  │Template:│  │Template:│  │Template:│  │Template:│            │
│  │AppConfig│  │Network  │  │Resource │  │  ...    │            │
│  │ YAML    │  │Policy   │  │Quota    │  │         │            │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘            │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
                               │
                               │ Label: namespaceclass.akuity.io/name=development-class
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│              Namespace: my-app (Core Kubernetes)                 │
│                     labels:                                      │
│              namespaceclass.akuity.io/name: development-class    │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           │ Namespace Controller creates
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│    NamespaceState (my-app) - in namespace: my-app               │
│                                                                   │
│    spec:                                                         │
│      namespaceClass: development-class                           │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                           │ NamespaceState Controller creates instances
                           │ for each item in the class
                           │
        ┌──────────────────┼──────────────────┬──────────────┐
        ▼                  ▼                  ▼              ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────┐
│NamespaceItem │  │NamespaceItem │  │NamespaceItem │  │NamespaceItem
│  Instance    │  │  Instance    │  │  Instance    │  │  Instance│
│              │  │              │  │              │  │          │
│appconfig-item│  │networkpolicy │  │resourcequota │  │   ...    │
│in my-app ns  │  │ -item        │  │ -item        │  │          │
└──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬───┘
       │                 │                 │                 │
       │ NamespaceItemInstance Controller creates actual resources
       │
       ▼                 ▼                 ▼                 ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────┐
│  AppConfig   │  │ NetworkPolicy│  │ ResourceQuota│  │   ...    │
│ my-app-config│  │   dev-policy │  │  dev-quota   │  │          │
│              │  │              │  │              │  │          │
│ (Actual K8s  │  │ (Actual K8s  │  │ (Actual K8s  │  │(Actual   │
│  Resource)   │  │  Resource)   │  │  Resource)   │  │Resource) │
└──────────────┘  └──────────────┘  └──────────────┘  └──────────┘

All resources in namespace: my-app are owned by NamespaceItemInstances
```