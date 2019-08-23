# Upgrade Kubernetes that was deployed with Kubespray

1. First check current version to ensure that kubernetes is runnign 1.14.x
```(bash)
kubectl version
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"14", GitVersion:"v1.14.3", GitCommit:"96fac5cd13a5dc064f7d9f4f23030a6aeface6cc", GitTreeState:"clean", BuildDate:"2019-08-19T11:05:16Z", GoVersion:"go1.12.9", Compiler:"gc", Platform:"linux/amd64"}
```

## Update cephcluster to not allow multiple per node
```(bash)
kubectl edit cephclusters.ceph.rook.io -n rook-ceph rook-ceph
```
```(yaml)
spec: 
  mon: 
    allowMultiplePerNode: false
```

## Update kubespray to enable version 1.14.6
```(bash)
cd ~/kubespray
```

## Change the kube_version: property to 1.14.6 in these files
```(bash)
vi inventory/swimlane-k8s/group_vars/k8s-cluster/k8s-cluster.yml
vi roles/kubespray-defaults/defaults/main.yaml
vi roles/download/defaults/main.yml
```
## Add the proper checksums to the downloads config as well
```(bash)
vi roles/download/defaults/main.yml
```
```(yaml)
hyperkube_checksums: 
  amd64:
    v1.14.6: 4f9a8984985786797fa3353961ba2b58f50235581c9b5978130fbb4199005538 
kubeadm_checksums: 
  amd64:
    v1.14.6: 4ef6030ab059ed434702c003975273dc855c370c4fcdae1109a3bb137c16ecb9
```
## Run the cluster upgrade (plan on about 30- 45 minutes)
```(bash)
ansible-playbook -b -i inventory/swimlane-k8s/hosts.yml upgrade-cluster.yml
```