### 1. install nfs server
    1. yum -y install rpcbind nfs-utils
    2. mkdir /data/share  # create a shared directory
    3. chmod 755 -R /data/share/ # modify the permissions for this directory
    4. vim /etc/exposts
        Add a line as follows
          /data/share/ 192.168.11.34(rw,no_root_squash,no_all_squash,sync) 
### 2. create pv on cluster
    kubectl apply -f es-pc.yml
### 3. create pvc on namespace 
    kubectl apply -f es-pvc.yml
### 4. create es-sts on namespace
    kubectl apply -f es-sts.yml
### 5. create es-svc on namespace
    kubectl apply -f es-svc.yml
### 6. create kibana on namespace
    kubectl apply -f kibana-deploy.yml
### 7. create kibana svc on namespace
    kubectl apply -f kibana-svc.yml

#### pv access strategy
```
Retain：保留，该策略允许手动回收资源，当删除PVC时，PV仍然存在，PV被视为已释放，管理员可以手动回收卷。
Recycle：回收，如果Volume插件支持，Recycle策略会对卷执行rm -rf清理该PV，并使其可用于下一个新的PVC，但是本策略将来会被弃用，目前只有NFS和HostPath支持该策略。
Delete：删除，如果Volume插件支持，删除PVC时会同时删除PV，动态卷默认为Delete，目前支持Delete的存储后端包括AWS EBS, GCE PD, Azure Disk, or OpenStack Cinder等。
可以通过persistentVolumeReclaimPolicy: Recycle字段配置
```
#### pv ecycling strategy
```
ReadWriteOnce：可以被单节点以读写模式挂载，命令行中可以被缩写为RWO。
ReadOnlyMany：可以被多个节点以只读模式挂载，命令行中可以被缩写为ROX。
ReadWriteMany：可以被多个节点以读写模式挂载，命令行中可以被缩写为RWX。
ReadWriteOncePod ：只允许被单个Pod访问，需要K8s 1.22+以上版本，并且是CSI创建的PV才可使用
```