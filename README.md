# high-availability

## 应该采取哪些措施?

- 打散 Pod 调度
使用反亲和策略打散 Pod 调度到不同一个节点，避免单节点故障

```
spec:
  affinity:
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        podAffinityTerm:
          labelSelector: 
            matchExpressions:
            - key: app
              operator: In
              values:
              -  my-app
          topologyKey: "kubernetes.io/hostname"
```
- 节点组调度
不同类型的应用对资源的需求往往不同，使用节点亲和策略将这些应用分配到不同的节点组，可以根据其特定需求配置节点资源，从而提高资源利用率和应用性能
```
   affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: node-group
            operator: In
            values:                 
            - business
```
优化滚动更新《滚动更新也翻车：为什么 Kubernetes 看似无缝的更新也会影响服务》中有一些具体的方案

- 合理设置 Request 与 Limit：可以避免某些Pod消耗过多资源，导致节点过载或其他Pod资源不足，确保集群中的每个Pod都能获得公平的资源分配

- Horizontal Pod Autoscaler：Pod 水平自动扩缩，根据应用的实际负载动态调整Pod的数量，以确保应用在高负载时有足够的资源支持，甚至你还可以在《KEDA：解锁 Kubernetes 自动扩缩的无限可能》这节中根据你的需求进行更细粒度的扩展

- 优化依赖服务：为关键依赖服务配置冗余，避免单点故障，对依赖服务进行监控，及时发现并处理问题，确保依赖服务的稳定性

- Cluster Autoscaler：动态扩展集群，当集群中有Pod因为资源不足而无法调度时，它会自动增加新节点来弥补节点资源不足的问题


