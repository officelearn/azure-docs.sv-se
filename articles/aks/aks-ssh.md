---
title: SSH till noder i Azure Container Service (AKS)
description: Skapa en SSH-anslutning med ett Azure Container Service (AKS)-kluster med noder
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>SSH till noder i Azure Container Service (AKS)

Ibland kan behöva du åtkomst till en Azure Container Service (AKS)-nod för underhåll, Logginsamling eller andra åtgärder för felsökning. Azure Container Service (AKS) noder exponeras inte till internet. Använd stegen som beskrivs i det här dokumentet för att skapa en SSH-anslutning med en AKS-nod.

## <a name="configure-ssh-access"></a>Konfigurera SSH-åtkomst

 Till SSH till en viss nod, skapas en baljor med `hostNetwork` åtkomst. En tjänst skapas också för baljor åtkomst. Den här konfigurationen är privilegierad och bör tas bort efter användning.

Skapa en fil med namnet `aks-ssh.yaml` och kopiera i manifestet. Uppdatera nodnamnet med namnet på AKS målnoden.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

Kör manifestet för att skapa baljor och tjänsten.

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

Hämta externa IP-adressen för tjänsten exponerade. Det kan ta ett tag för IP-adresskonfiguration att slutföra. 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

Skapa den ssh anslutning. 

Standardanvändarnamnet för ett AKS kluster är `azureuser`. Om det här kontot har ändrats vid tidpunkten för skapandet av klustret i stället rätt administratörsanvändarnamnet. 

Om nyckeln inte är på `~/ssh/id_rsa`, ange rätt plats med hjälp av den `ssh -i` argumentet.

```azurecli-interactive
$ ssh azureuser@13.92.154.191

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

Ta bort tjänsten SSH åtkomst baljor och när du är klar.

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```