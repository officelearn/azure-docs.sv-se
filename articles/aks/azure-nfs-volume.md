---
title: Skapa NFS Ubuntu Linux Server-volym
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du skapar en NFS Ubuntu Linux Server-volym manuellt för användning med poddar i Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 7db3f806df88e5b23012e97ba5c2f14ca65b2508
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803474"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Skapa och använda en Linux Server-volym (NFS) (Network File System) manuellt med Azure Kubernetes Service (AKS)
Att dela data mellan behållare är ofta en nödvändig komponent i behållarbaserade tjänster och program. Du har vanligtvis olika poddar som behöver åtkomst till samma information på en extern beständig volym.    
Medan Azure-filer är ett alternativ är det en annan form av beständig delad lagring att skapa en NFS-server på en virtuell Azure-dator. 

Den här artikeln visar hur du skapar en NFS-server på en virtuell Ubuntu-dator. Och även ge din AKS behållare tillgång till detta delade filsystem.

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster läser du SNABBSTARTen för AKS [med Azure CLI][aks-quickstart-cli] eller använder [Azure-portalen][aks-quickstart-portal].

AKS-klustret måste bo i samma eller peer-virtuella nätverk som NFS-servern. Klustret måste skapas i ett befintligt virtuellt nätverk, vilket kan vara samma virtuella nätverk som den virtuella datorn.

Stegen för att konfigurera med ett befintligt virtuellt nätverk beskrivs i dokumentationen: [skapa AKS-kluster i befintligt virtuellt][aks-virtual-network] nätverk och [ansluta virtuella nätverk med VNET-peering][peer-virtual-networks]

Det förutsätter också att du har skapat en Ubuntu Linux Virtual Machine (till exempel 18,04 LTS). Inställningar och storlek kan passa dig och kan distribueras via Azure. Snabbstart för Linux finns i [Linux VM-hantering][linux-create].

Om du distribuerar aks-klustret först fyller Azure automatiskt i det virtuella nätverksfältet när du distribuerar din Ubuntu-dator, vilket gör att de visas inom samma virtuella nätverk. Men om du vill arbeta med peered-nätverk istället, läs dokumentationen ovan.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Distribuera NFS-servern på en virtuell dator
Här är skriptet för att ställa in en NFS-server i din Ubuntu virtuella dator:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
Servern startas om (på grund av skriptet) och du kan montera NFS-servern till AKS.

>[!IMPORTANT]  
>Se till att ersätta **AKS_SUBNET** med rätt från klustret annars "*" kommer att öppna din NFS-server till alla portar och anslutningar.

När du har skapat den virtuella datorn kopierar du skriptet ovan till en fil. Sedan kan du flytta den från din lokala dator, eller var skriptet än är, till den virtuella datorn med: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
När skriptet är i din virtuella dator kan du ssh i den virtuella datorn och köra den via kommandot:
```console
sudo ./nfs-server-setup.sh
```
Om körningen misslyckas på grund av ett nekat tillståndsfel anger du körningsbehörighet via kommandot:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Ansluta AKS-kluster till NFS-server
Vi kan ansluta NFS-servern till vårt kluster genom att etablera en beständig volym och beständig volym anspråk som anger hur du kommer åt volymen.

Det är nödvändigt att ansluta de två tjänsterna i samma eller peer-virtuella nätverk. Instruktioner för att konfigurera klustret i samma virtuella nätverk finns här: [Skapa AKS-kluster i befintligt VNET][aks-virtual-network]

När de finns i samma virtuella nätverk (eller peer-) måste du etablera en beständig volym och ett beständigt volymanspråk i AKS-klustret. Behållarna kan sedan montera NFS-enheten till sin lokala katalog.

Här är ett exempel Kubernetes definition för den beständiga volymen (Den här definitionen förutsätter att ditt kluster och den virtuella datorn är i samma VNET):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Ersätt **NFS_INTERNAL_IP,** **NFS_NAME** och **NFS_EXPORT_FILE_PATH** med NFS-serverinformation.

Du behöver också en beständig volymanspråksfil. Här är ett exempel på vad som ska ingå:

>[!IMPORTANT]  
>**"storageClassName"** måste förbli en tom sträng eller så fungerar inte anspråket.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Felsökning
Om du inte kan ansluta till servern från ett kluster kan ett problem vara den exporterade katalogen, eller så har den överordnade katalogen inte tillräcklig behörighet för att komma åt servern.

Kontrollera att både exportkatalogen och den överordnade katalogen har 777 behörigheter.

Du kan kontrollera behörigheter genom att köra kommandot nedan och kataloger bör ha *"drwxrwxrwx"* behörigheter:
```console
ls -l
```

## <a name="more-information"></a>Mer information
För att få en fullständig genomgång eller för att hjälpa dig att felsöka din NFS Server setup, här är en djupgående handledning:
  - [NFS-självstudiekurs][nfs-tutorial]

## <a name="next-steps"></a>Nästa steg

Information om associerade metodtips finns [i Metodtips för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md
