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

--- 

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

---

### update elastcisearch.yml and pvc.yml data node hostname
- data node hostname elastcisearch.yml and pvc.yml 兩個data node 名稱要一樣

pvc.yml
```yaml
- matchExpressions:               
        - key: kubernetes.io/hostname
          operator: In
          values:
          - data node name # change here   ✅正確的 data node hostname
```

elasticsearch.yml
```yaml
podTemplate:
      spec:
        nodeSelector:
          kubernetes.io/hostname: data node name  # change here   ✅正確的 data node hostname
```

---

### update elasticsearch.yml 
- Pod 雖然正常排程、PVC 有綁定，但容器無法寫入 /usr/share/elasticsearch/data。
- 你可以在 podTemplate.spec 中加入這段 initContainer，用來修改 Elasticsearch 寫入資料的目錄權限：
- name: elasticsearch-data 要跟你的 volumeClaimTemplates.metadata.name 一致。

```yaml
        securityContext:
          runAsUser: 1000
          runAsGroup: 1000
          fsGroup: 1000
        initContainers:
        - name: fix-permissions
          image: busybox
          command: ["sh", "-c", "chown -R 1000:1000 /usr/share/elasticsearch/data"]
          volumeMounts:
          - name: elasticsearch-data
            mountPath: /usr/share/elasticsearch/data
```

### update pvc.yaml


```yaml
local:
    path: /home/ubuntu/ELK-stack-multiple-node/task-1/step2/pvc-data/
    type: DirectoryOrCreate
```

### update elasticsearch.yml 發現master
- 用於發現 master 節點

```yaml
cluster.name: "quickstart"  # 集群名稱
      discovery.seed_hosts: ["quickstart-es-master-node-0"] # 用於發現 master 節點
```