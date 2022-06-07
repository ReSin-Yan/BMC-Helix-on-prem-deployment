# BMC-Helix-on-prem-deployment

### 環境準備以及說明  
BMC-Helix-on-prem使用了Kubernetes的安裝方式  
並請在安裝過程中需要符合很多特定配置(包含配置版本)  
以下列出了全部需要的機器(虛擬機or實體機)  

使用的環境為  
 | 配置腳色 | 
|-------| 
| Kubernetes Master * 1 | 
| Kubernetes Worker * 5 | 
| Haproxy | 
| DeoloymentEngine  | 
| NFS  | 

## Kubernetes      

### 安裝步驟  

#### 環境硬體配置(最低需求)  

Kubernetes Master  
 | 配置 | 版本or建議規格 | 
|-------|-------|
| OS | ubutu desktop 20.04 |
| CPU |  56 CPU |
| Memory  | 644 GB+ |
| Disk  | 200 GB+ |  

Kubernetes Worker  
 | 配置 | 版本or建議規格 | 
|-------|-------|
| OS | ubutu desktop 20.04 |
| CPU |  16 CPU |
| Memory  | 644 GB+ |
| Disk  | 200 GB+ |  

以上總共六台機器，組成一組Kubernetes叢集  
需要注意六台機器建議使用ubuntu安裝  
並且hostname需要完全不相同  
使用centos需要額外關閉防火牆及其他設定步驟  

#### 環境套件需求(必要)  
 | 配置 | 版本or建議規格 | 
|-------|-------|
| Docker | Version 17.06.0-ce+ 或著更高版本 |

#### 環境準備  

環境更新及安裝基本套件  
```
sudo apt-get update && sudo apt-get -y upgrade
sudo apt-get -y install vim build-essential curl ssh nfs-kernel-server nfs-common  
```
安裝Docker engine  
```
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

確認安裝版本  
```
sudo docker --version
```

安裝kubernetes(在每一台機器上)  
```
#安裝kubernetes(在每一台機器上)
```
安裝helm和設定Kubernetes(在Master機器上)  
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

sudo kubeadm init  --pod-network-cidr=10.244.0.0/16  
```
安裝完成之後(在Master機器上)  
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
設定加入叢集(在5台Worker機器上)  
```
#參數請由Master產生的資訊貼上(最前面加sudo)  
sudo kubeadm join 192.168.0.181:6443 --token dlkmv8.iyfymnzohzqwwsp9 --discovery-token-ca-cert-hash sha256:e97eca0ba0bf19c1c3942b53314ad492c2fa57fbf109492f49fed12d4fcc5641
```
設定podNetwork，選擇使用calico  
```
curl https://docs.projectcalico.org/manifests/calico.yaml -O
kubectl apply -f calico.yaml

#輸入指令確認服務是否存在 
kubectl get pod -A  
```  
設定Loadbalance  
```  
git clone https://github.com/ReSin-Yan/kuberenetes-base.git
cd kuberenetes-base/k8stools/service/loadbalancetools/  
sh installMetallb.sh [IPRange] [IPRange]
```  
設定ingress  
設定StorageClass(根據準備好的NFS server)  
```  
helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner --set nfs.server=192.168.0.186 --set nfs.path=/mnt/nfsshare
```  
