# Copilot Instructions for ELK-stack-multiple-node

## Project Overview
This repository automates multi-node ELK stack (Elasticsearch, Logstash, Kibana, Filebeat) deployment on Kubernetes (k3s) using Helm and Ansible. It supports dynamic configuration via Ansible inventories and Jinja2 templates, enabling cluster scaling and node role assignment.

## Architecture & Key Patterns
- **Helm Charts**: Used for deploying ELK components. Values files (e.g., `elk/elasticsearch/values-worker.yaml`, `elk/filebeat/values.yml`) define node roles, resources, and service endpoints.
- **Ansible Automation**: Playbooks and roles (see `AQUA-CARE-2025-June/ansible/`) provision k3s clusters, generate Helm values from inventory IPs, and orchestrate deployments.
- **Jinja2 Templates**: Located in `elk/elasticsearch/templates/`, these allow dynamic Helm values generation based on Ansible inventory (hosts.ini).
- **Persistent Storage**: PV/PVC YAMLs (e.g., `task-1/step2/pvc.yml`) use nodeAffinity and hostPath/local volumes to bind storage to specific k3s nodes.

## Developer Workflows
- **Dynamic Values Generation**: Use Ansible's `template` module to render Helm values from Jinja2 templates, injecting inventory IPs and node roles.
- **Deployment**: Run Ansible playbooks to provision k3s, generate values files, and deploy ELK via Helm.
- **Storage Binding**: Ensure PV nodeAffinity matches Pod nodeSelector for correct PVC binding.
- **Debugging**: Check pod scheduling, PVC status, and service endpoints. Use `kubectl` and Helm commands for troubleshooting.

## Conventions & Integration
- **Node Role Assignment**: Values files use `nodeSelector` and `roles` to bind pods to specific nodes and assign Elasticsearch roles.
- **Secrets Management**: Sensitive credentials (e.g., Elasticsearch username/password) are injected via Kubernetes Secrets and referenced in values files.
- **Service Exposure**: Service types (ClusterIP, LoadBalancer) are set in values files to control network access.
- **Resource Requests**: Explicit CPU/memory requests/limits are set for each ELK component.

## Examples
- **Jinja2 Template Usage**:
  ```yaml
  output.elasticsearch:
    hosts: ["http://{{ groups['elasticsearch'][0] }}:9200"]
  ```
- **Ansible Task for Template Rendering**:
  ```yaml
  - name: Render Helm values
    template:
      src: elk/elasticsearch/templates/values-elasticsearch.jinja2
      dest: /tmp/values-elasticsearch.yaml
  ```
- **Pod/Storage Binding**:
  - In values-worker.yaml:
    ```yaml
    nodeSelector:
      kubernetes.io/hostname: node-worker
    volumeClaimTemplate:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 30Gi
    ```
  - In pvc.yml:
    ```yaml
    nodeAffinity:
      required:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/hostname
            operator: In
            values:
            - node-worker
    ```

## Key Files & Directories
- `elk/elasticsearch/values-worker.yaml`, `elk/filebeat/values.yml`: Helm values for ELK components
- `elk/elasticsearch/templates/values-elasticsearch.jinja2`: Jinja2 template for dynamic values
- `AQUA-CARE-2025-June/ansible/`: Ansible playbooks and roles
- `task-1/step2/pvc.yml`: PersistentVolume definitions

## Tips
- Always sync nodeSelector/nodeAffinity between values files and PVs for reliable storage binding.
- Use Ansible inventory groups to drive dynamic configuration.
- Reference secrets in values files for secure credential management.

---
_Last updated: 2025-07-21_
