### PVC Path 權限設定
- 使用ansble 腳本自動設定

```yml
hosts: all 
...
when: inventory_hostname in groups['k3s_server']
...
when: inventory_hostname in groups['k3s_agent']
```
- 判斷 when