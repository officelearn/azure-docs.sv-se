---
title: API-servern auktoriserade IP-intervall i Azure Kubernetes service (AKS)
description: Lär dig hur du skyddar klustret med hjälp av ett IP-adressintervall för åtkomst till API-servern i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 8418499cc3e094162ac7483aaa6c71e74db95ae1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472957"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Säker åtkomst till API-servern med behöriga IP-adressintervall i Azure Kubernetes service (AKS)

I Kubernetes tar API-servern emot begär Anden för att utföra åtgärder i klustret, till exempel för att skapa resurser eller skala antalet noder. API-servern är det centrala sättet att interagera med och hantera ett kluster. För att förbättra kluster säkerheten och minimera angrepp bör API-servern endast vara tillgänglig från en begränsad uppsättning IP-adressintervall.

Den här artikeln visar hur du använder tillåtna IP-adressintervall för API-Server för att begränsa vilka IP-adresser och CIDR-områden som kan komma åt kontroll planet.

> [!IMPORTANT]
> I nya kluster stöds endast tillåtna IP-adressintervall för API-server på SKU: n för *standard* -SKU: n. Befintliga kluster med den *grundläggande* SKU-belastningsutjämnaren och den API-server som auktoriserats har kon figurer ATS för att fortsätta fungera som det är. De befintliga clusers kan också uppgraderas och de kommer att fortsätta att fungera.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du arbetar med kluster som använder [Kubernetes][kubenet].  Med [Azure Container Networking Interface (cni)][cni-networking] -baserade kluster, kommer du inte att ha den nödvändiga routningstabellen som krävs för att skydda åtkomsten.  Du måste skapa routningstabellen manuellt.  Mer information finns i [Hantera](https://docs.microsoft.com/azure/virtual-network/manage-route-table) routningstabeller.

API-serverns auktoriserade IP-intervall fungerar bara för nya AKS-kluster som du skapar. Den här artikeln visar hur du skapar ett AKS-kluster med hjälp av Azure CLI.

Du behöver Azure CLI-versionen 2.0.76 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du konfigurerar tillåtna IP-intervall för API-servern:

* Du kan för närvarande inte använda Azure dev Spaces eftersom kommunikationen med API-servern också är blockerad.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Översikt över tillåtna IP-intervall för API-Server

Kubernetes-API-servern är hur de underliggande Kubernetes-API: erna exponeras. Den här komponenten ger interaktion för hanterings verktyg, till exempel `kubectl` eller Kubernetes-instrumentpanelen. AKS tillhandahåller en kluster hanterare för en enda klient, med en dedikerad API-Server. Som standard tilldelas API-servern en offentlig IP-adress och du bör kontrol lera åtkomst med hjälp av rollbaserad åtkomst kontroll (RBAC).

Du kan aktivera och använda auktoriserade IP-intervall för att skydda åtkomsten till den allmänt tillgängliga AKS kontroll planet/API-servern. Dessa auktoriserade IP-adressintervall tillåter endast att definierade IP-adressintervall kommunicerar med API-servern. En begäran till API-servern från en IP-adress som inte tillhör dessa auktoriserade IP-intervall är blockerad. Du bör fortsätta att använda RBAC för att sedan auktorisera användare och de åtgärder som de begär.

Mer information om API-servern och andra kluster komponenter finns i [Kubernetes Core Concepts for AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

API-serverns auktoriserade IP-intervall fungerar bara för nya AKS-kluster. Du kan inte aktivera auktoriserade IP-intervall som en del av klustret skapa åtgärd. Om du försöker aktivera auktoriserade IP-intervall som en del av klustrets skapande process, kan inte klusternoderna komma åt API-servern under distributionen eftersom den utgående IP-adressen inte har definierats vid den punkten.

Börja med att skapa ett kluster med kommandot [AZ AKS Create][az-aks-create] . I följande exempel skapas ett kluster med en nod med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-cluster-with-authorized-ip-ranges"></a>Uppdatera kluster med auktoriserade IP-intervall

Som standard använder klustret [standard SKU-belastningsutjämnaren][standard-sku-lb], som du kan använda för att konfigurera den utgående gatewayen. Använd [AZ Network Public-IP List][az-network-public-ip-list] och ange resurs gruppen för ditt AKS-kluster, som vanligt vis börjar med *MC_* . Detta visar den offentliga IP-adressen för klustret, som du kan tillåta. Använd kommandot [AZ AKS Update][az-aks-update] och ange parametern *--API-Server-Allowed-IP-Ranges* för att tillåta IP-adressen för klustret. Till exempel:

```azurecli-interactive
RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SLB_PublicIP=$(az network public-ip list --resource-group $RG --query [].ipAddress -o tsv)
az aks update --api-server-authorized-ip-ranges $SLB_PublicIP --resource-group myResourceGroup --name myAKSCluster
```

Om du vill aktivera API-serverns auktoriserade IP-intervall använder du kommandot [AZ AKS Update][az-aks-update] och anger parametern *--API-Server-auktoriserat-IP-Ranges* för att tillhandahålla en lista över tillåtna IP-adressintervall. Dessa IP-adressintervall är vanligt vis adress intervall som används av dina lokala nätverk eller offentliga IP-adresser. När du anger ett CIDR-intervall börjar du med den första IP-adressen i intervallet. Till exempel är *137.117.106.90/29* ett giltigt intervall, men se till att du anger den första IP-adressen i intervallet, till exempel *137.117.106.88/29*.

I följande exempel aktive ras API-servern auktoriserade IP-intervall i klustret med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*. De IP-adressintervall som ska auktoriseras är *172.0.0.0/16* (Pod/Node-adressintervall) och *168.10.0.0/18* (ServiceCidr):

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 172.0.0.0/16,168.10.0.0/18
```

> [!NOTE]
> Du bör lägga till dessa intervall i en lista över tillåtna:
> - Brand väggens offentliga IP-adress
> - Service-CIDR
> - Adress intervallet för under näten med noderna och poddar
> - Alla intervall som representerar nätverk som du ska administrera klustret från

## <a name="disable-authorized-ip-ranges"></a>Inaktivera auktoriserade IP-intervall

Om du vill inaktivera auktoriserade IP-intervall använder du [AZ AKS Update][az-aks-update] och anger ett tomt intervall för att inaktivera API-serverns auktoriserade IP-intervall. Till exempel:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du aktiverat tillåtna IP-intervall för API-servern. Den här metoden är en del av hur du kan köra ett säkert AKS-kluster.

Mer information finns i [säkerhets begrepp för program och kluster i AKS][concepts-security] och [metod tips för kluster säkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[standard-sku-lb]: load-balancer-standard.md
