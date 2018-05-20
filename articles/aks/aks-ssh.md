---
title: SSH till noder i Azure Kubernetes Service (AKS)
description: Skapa en SSH-anslutning med ett kluster i Azure Kubernetes Service (AKS) noder
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 95b385e9847a7809492bbb74bd1eba616df90d72
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="ssh-into-azure-kubernetes-service-aks-cluster-nodes"></a>SSH till noder i Azure Kubernetes Service (AKS)

Ibland kan behöva du åtkomst till en Azure Kubernetes Service (AKS)-nod för underhåll, Logginsamling eller andra åtgärder för felsökning. AKS noder exponeras inte till internet. Använd stegen som beskrivs i det här dokumentet för att skapa en SSH-anslutning med en AKS-nod.

## <a name="reset-ssh-keys"></a>Återställ SSH-nycklar

Om du har distribuerat en AKS utan SSH-nycklar eller inte har tillgång till rätt SSH-nycklar, kan dessa återställas med hjälp av Azure portal.

Bläddra till AKS klustret, väljer en AKS nod (virtuell dator) och väljer **Återställ lösenord** att återställa den offentliga SSH-nyckeln.

![AKS VM med knappen för återställning av lösenord](media/aks-ssh/reset-password.png)

Välj **återställa SSH offentlig nyckel**, ange AKS klustret användarnamn är **azueruser** som standard och kopiera i en offentlig SSH-nyckel. Välj **uppdatering** när du är klar.

![AKS portal VM med knappen för återställning av lösenord](media/aks-ssh/reset-password-2.png)

Du kan skapa en SSH-anslutning med hjälp av motsvarande privata nyckel när SSH-nyckeln har återställts.

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

Kopiera den privata SSH-nyckeln till baljor ersätter baljor namnet med rätt värde.

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
