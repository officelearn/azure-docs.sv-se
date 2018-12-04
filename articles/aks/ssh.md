---
title: SSH till noder i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar en SSH-anslutning med Azure Kubernetes Service (AKS) klusternoder för felsökning och underhållsåtgärder.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/21/2018
ms.author: iainfou
ms.openlocfilehash: d687467e6bd64363c78f60064c6a17adbc5e0d1f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52846139"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Ansluta med SSH till Azure Kubernetes Service (AKS) klusternoderna för underhåll och felsökning

Du kan behöva åtkomst till ett AKS-noden under livscykeln för ditt kluster i Azure Kubernetes Service (AKS). Den här åtkomsten kan vara för underhåll, Logginsamling eller andra felsökning åtgärder. AKS-noder är virtuella Linux-datorer så att du kan komma åt dem med hjälp av SSH. Av säkerhetsskäl exponeras inte AKS-noder till internet.

Den här artikeln visar hur du skapar en SSH-anslutning med ett AKS-noden med sina privata IP-adresser.

## <a name="add-your-public-ssh-key"></a>Lägg till din offentliga SSH-nyckel

SSH-nycklar skapas som standard när du skapar ett AKS-kluster. Om du inte angav din egen SSH-nycklar när du skapade ditt AKS-kluster, lägger du till din offentliga SSH-nycklar till AKS-noder. 

Om du vill lägga till din SSH-nyckel till ett AKS-nod, utför du följande steg:

1. Hämta resursgruppens namn för dina resurser för AKS-kluster som använder [az aks show][az-aks-show]. Ange din egen core resursgruppens namn och AKS-klusternamnet:

    ```azurecli
    az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
    ```

1. Lista de virtuella datorerna i AKS kluster resource gruppen med den [az vm list] [ az-vm-list] kommando. Dessa virtuella datorer är AKS-noder:

    ```azurecli
    az vm list --resource-group MC_myResourceGroup_myAKSCluster_eastus -o table
    ```

    Följande Exempelutdata visar AKS-noder:

    ```
    Name                      ResourceGroup                                  Location
    ------------------------  ---------------------------------------------  ----------
    aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
    ```

1. Lägg till SSH-nycklar till noden genom att använda den [az vm user update] [ az-vm-user-update] kommando. Ange resursgruppens namn och sedan en av noderna i AKS som hämtades i föregående steg. Användarnamn för AKS-noder är som standard *azureuser*. Ange platsen för din egen SSH offentlig nyckel plats, till exempel *~/.ssh/id_rsa.pub*, eller klistra in innehållet i din offentliga SSH-nyckel:

    ```azurecli
    az vm user update \
      --resource-group MC_myResourceGroup_myAKSCluster_eastus \
      --name aks-nodepool1-79590246-0 \
      --username azureuser \
      --ssh-key-value ~/.ssh/id_rsa.pub
    ```

## <a name="get-the-aks-node-address"></a>Hämta AKS nod-adressen

AKS-nodernas exponeras inte offentligt på Internet. SSH för AKS-noder kan du använda den privata IP-adressen.

Visa privat IP-adressen för ett AKS-kluster noden med den [az vm list-ip-adresser] [ az-vm-list-ip-addresses] kommando. Ange din egen AKS-kluster resursgruppens namn fick i ett tidigare [az-aks-visa] [ az-aks-show] steg:

```azurecli
az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table
```

Följande Exempelutdata visar privata IP-adresserna för AKS-noder:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Skapa SSH-anslutning

För att skapa en SSH-anslutning till ett AKS-nod, kör du en helper-pod AKS-klustret. Den här helper-pod innehåller SSH-åtkomst till klustret och sedan ytterligare SSH-noden åtkomst. Om du vill skapa och använda den här helper-pod, gör du följande:

1. Kör en `debian` behållare avbildningen och bifoga en terminalsession till den. Den här behållaren kan användas för att skapa en SSH-session med en nod i AKS-klustret:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

1. Den grundläggande Debian-avbildningen innehåller inte SSH-komponenter. När terminalsessionen är ansluten till behållaren, installerar du en SSH-klient med hjälp av `apt-get` på följande sätt:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. I ett nytt terminalfönster inte är ansluten till din behållare, en lista med poddarna på AKS-kluster med den [kubectl hämta poddar] [ kubectl-get] kommando. Pod som skapades i föregående steg som startar med namnet *aks-ssh*, enligt följande exempel:

    ```
    $ kubectl get pods
    
    NAME                       READY     STATUS    RESTARTS   AGE
    aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
    ```

1. Du lagt till din offentliga SSH-nyckel AKS-noden i det första steget i den här artikeln. Nu kan kopiera den privata SSH-nyckeln till din pod. Den privata nyckeln används för att skapa en SSH i AKS-noder.

    Ange din egen *aks-ssh* podnamn hämtades i föregående steg. Om det behövs ändrar *~/.ssh/id_rsa* till platsen för din privata SSH-nyckel:

    ```console
    kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
    ```

1. Tillbaka i terminalsession till behållaren, uppdatera behörigheterna på den kopierade `id_rsa` privata SSH-nyckeln så att den är användaren skrivskyddad:

    ```console
    chmod 0600 id_rsa
    ```

1. Nu ska du skapa en SSH-anslutning till AKS-nod. Igen, Standardanvändarnamnet för AKS-noder är *azureuser*. Acceptera uppmaningen att fortsätta med anslutningen som SSH-nyckeln är den första betrodda. Du sedan får bash-Kommandotolken för AKS-nod:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4
    
    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.
    
    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud
    
    [...]
    
    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Ta bort SSH-åtkomst

När du är klar `exit` SSH-sessionen och sedan `exit` interaktiva container-sessionen. När den här behållaren sessionen stängs tas pod som används för SSH-åtkomst från AKS-klustret bort.

## <a name="next-steps"></a>Nästa steg

Om du behöver ytterligare felsökning data, kan du [visa kubelet-loggar] [ view-kubelet-logs] eller [visa Kubernetes-huvudnod loggar][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: view-master-logs.md
