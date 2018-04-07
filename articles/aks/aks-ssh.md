---
title: SSH till noder i Azure Container Service (AKS)
description: Skapa en SSH-anslutning med ett Azure Container Service (AKS)-kluster med noder
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 085a2976443db8ece7a36dbfc133b173432ce4c8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>SSH till noder i Azure Container Service (AKS)

Ibland kan behöva du åtkomst till en Azure Container Service (AKS)-nod för underhåll, Logginsamling eller andra åtgärder för felsökning. Azure Container Service (AKS) noder exponeras inte till internet. Använd stegen som beskrivs i det här dokumentet för att skapa en SSH-anslutning med en AKS-nod.

## <a name="get-aks-node-address"></a>Hämta AKS nodadress

Hämta IP-adressen för en AKS nod med den `az vm list-ip-addresses` kommando. Ersätt resursgruppens namn med namnet på resursgruppen AKS.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>Skapa SSH-anslutning

Kör den `debian` behållare avbildningen och bifoga en terminalsession. Behållaren kan sedan användas för att skapa en SSH-session med en nod i klustret AKS.

```console
kubectl run -it --rm aks-ssh --image=debian
```

Installera en SSH-klient i behållaren.

```console
apt-get update && apt-get install openssh-client -y
```

Öppna en andra terminal och visa en lista med alla skida för att hämta det nyligen skapade baljor namnet.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

Kopiera SSH-nyckel till baljor ersätter baljor namnet med rätt värde.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

Uppdatera den `id_rsa` filen så att den användare som skrivskyddad.

```console
chmod 0600 id_rsa
```

Nu skapa en SSH-anslutning till noden AKS. Standardanvändarnamnet för ett AKS kluster är `azureuser`. Om det här kontot har ändrats vid tidpunkten för skapandet av klustret i stället rätt administratörsanvändarnamnet.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Ta bort SSH-åtkomst

När du är klar avsluta SSH-session och sedan behållare för interaktiva sessionen. Den här åtgärden tar bort baljor som används för SSH-åtkomst från AKS klustret.