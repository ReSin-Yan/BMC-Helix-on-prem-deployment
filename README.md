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
| CPU |  8 CPU |
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
安裝Docker  
```
sudo apt-get update && sudo apt-get -y upgrade
sudo apt-get -y install vim build-essential curl ssh

curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh

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
