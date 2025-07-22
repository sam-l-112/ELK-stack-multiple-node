### update pvc.yml
- 綁定master node and data node 名稱為

```yaml
claimRef:
    name: elasticsearch-data-quickstart-es-master-node-0
    namespace: default
```

```yaml
name:
    name: elasticsearch-data-quickstart-es-data-node-0
    namespace: default
```
### update elasticsearch.yml 
- 指定此 nodeSet 的 Pod 只會被排程到指定的節點（node-data-5361855-iaas.novalocal）
- 這樣可以確保 data-node 只會在特定主機上運行，通常用於本地存儲或特殊硬體需求
- nodeSelector 需與 PV 的 nodeAffinity 設定一致，才能正確綁定 PVC

```yaml
podTemplate:
      spec:
        nodeSelector:
          kubernetes.io/hostname: node-data-5361855-iaas.novalocal
```