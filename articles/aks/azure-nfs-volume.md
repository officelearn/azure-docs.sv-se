---
title: Skapa en Ubuntu-Server för NFS (Network File System) för användning av poddar i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar en Ubuntu Linux-Server för NFS-volym för användning med poddar i Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.service: container-service
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 55eb5b0b98a4097d2f300bacabbfef3b0a32b27b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468506"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Manuellt skapa och använda en Linux-Server för NFS (Network File System)-volym med Azure Kubernetes Service (AKS)
Dela data mellan behållare är ofta en nödvändig komponent för behållarbaserade tjänster och program. Du har vanligtvis olika poddar som behöver åtkomst till samma information på en extern permanent volym.    
Azure files är ett alternativ, är skapa en NFS-Server i en Azure-dator en annan typ av beständig delad lagring. 

Den här artikeln visar hur du skapar en NFS-Server på en Ubuntu-dator. Och även ge din AKS behållare åtkomst till den här delade filsystem.

## <a name="before-you-begin"></a>Innan du börjar
Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster finns i snabbstarten om AKS [med Azure CLI] [ aks-quickstart-cli] eller [med Azure portal][aks-quickstart-portal].

AKS-klustret måste finnas i samma eller en peer-kopplade virtuella nätverk som NFS-servern. Klustret måste skapas i ett befintligt VNET som kan vara i samma virtuella nätverk som den virtuella datorn.

Stegen för att konfigurera med ett befintligt VNET beskrivs i dokumentationen: [skapar AKS-kluster i befintliga VNET] [ aks-virtual-network] och [ansluter virtuella nätverk med VNET-peering][peer-virtual-networks]

Det förutsätts även att du har skapat en Ubuntu Linux-dator (till exempel 18.04 LTS). Inställningar och storlek kan vara enligt dina önskemål och kan distribueras via Azure. Linux Snabbstart Se [linux VM management][linux-create].

Om du distribuerar AKS-klustret först Azure automatiskt fylls fältet virtuellt nätverk när du distribuerar din Ubuntu-dator, vilket gör dem live inom samma virtuella nätverk. Men om du vill arbeta med peer-kopplade nätverk i stället läser du dokumentationen som ovan.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>Distribuera NFS-servern på en virtuell dator
Här är skript för att ställa in en NFS-Server inom virtuell Ubuntu-dator:
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
Servern startas om (på grund av skript) och du kan montera NFS-servern till AKS

>[!IMPORTANT]  
>Ersätt den **AKS_SUBNET** med rätta från ditt kluster eller ”*” öppnas NFS-Server för alla portar och anslutningar.

När du har skapat den virtuella datorn kan du kopiera skriptet ovan till en fil. Sedan kan du flytta den från din lokala dator eller när skriptet är i virtuell dator med: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
När skriptet är i den virtuella datorn, att ssh till den virtuella datorn och kör det via kommandot:
```console
sudo ./nfs-server-setup.sh
```
Om körningen misslyckas på grund av ett behörighet nekad-fel kan du ange körning behörighet via kommandot:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>Anslutande AKS-kluster till NFS-Server
Vi kan ansluta NFS-servern till klustret genom att etablera en permanent volym och permanent volym anspråk som anger hur du kommer åt volymen.  
Det är nödvändigt att ansluta de två tjänsterna i samma eller en peer-kopplade virtuella nätverk. Anvisningar för att skapa kluster i samma virtuella nätverk finns här: [skapar AKS-kluster i ett befintligt virtuellt nätverk][aks-virtual-network]

När de är i samma virtuella nätverk (eller peer-kopplade) måste du etablera en permanent volym och en permanent volym anspråk i AKS-klustret. Behållarna kan sedan montera NFS-enheten till sina lokala katalog.

Här är ett exempel kubernetes-definition för permanent volym (den här definitionen förutsätter ditt kluster och virtuella datorer finns i samma virtuella nätverk):

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
Ersätt **NFS_INTERNAL_IP**, **NFS_NAME** och **NFS_EXPORT_FILE_PATH** med information om NFS-servern.

Du måste också en permanent volym anspråk-fil. Här är ett exempel på vad som ska ingå:

>[!IMPORTANT]  
>**”storageClassName”** måste vara en tom sträng eller anspråket fungerar inte.

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
Om du inte kan ansluta till servern från ett kluster, ett problem kan vara den exporterade katalogen eller dess överordnade objekt, har inte tillräcklig behörighet för att få åtkomst till servern.

Kontrollera att både export katalogen och dess överordnade katalogen har 777 behörigheter.

Du kan kontrollera behörigheter genom att köra kommandot nedan och katalogerna ska ha *'drwxrwxrwx'* behörigheter:
```console
ls -l
```

## <a name="more-information"></a>Mer information
Att hämta en fullständig genomgång eller för att felsöka din konfiguration för NFS-Server, är här detaljerade Självstudier:
  - [NFS-självstudien][nfs-tutorial]

## <a name="next-steps"></a>Nästa steg

Associerade metodtips finns [bästa praxis för lagring och säkerhetskopiering i AKS][operator-best-practices-storage].

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
