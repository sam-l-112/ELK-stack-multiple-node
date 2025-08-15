# ELK-stack-multiple-node


## update ansibel
- 支援一鍵執行 Ansible Playbook，透過 `ansible-playbook` 指令自動部署 k3s、ELK 相關服務。
- Playbook 可自動安裝 master/agent 節點，並完成 kubeconfig 權限、ELK 角色等配置。

# update ELK-roles
ELK-roles 目錄包含 elasticsearch、kibana、logstash 等角色的 Ansible 任務與設定檔。
- 可快速套用 ELK 各元件的標準化配置。
- 方便多節點部署與自動化管理。


