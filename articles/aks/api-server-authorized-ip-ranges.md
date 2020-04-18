---
title: API-server auktoriserade IP-intervall i Azure Kubernetes Service (AKS)
description: Lär dig hur du skyddar klustret med hjälp av ett IP-adressintervall för åtkomst till API-servern i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 570d842409fc019d24446e091f83402f4c288d7c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640054"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Säker åtkomst till API-servern med auktoriserade IP-adressintervall i Azure Kubernetes Service (AKS)

I Kubernetes tar API-servern emot begäranden om att utföra åtgärder i klustret, till exempel för att skapa resurser eller skala antalet noder. API-servern är det centrala sättet att interagera med och hantera ett kluster. För att förbättra klustersäkerheten och minimera attacker bör API-servern endast vara tillgänglig från en begränsad uppsättning IP-adressintervall.

Den här artikeln visar hur du använder API-server auktoriserade IP-adressintervall för att begränsa vilka IP-adresser och CIDRs kan komma åt kontrollplan.

> [!IMPORTANT]
> På nya kluster stöds API-serverauktoriserade IP-adressintervall endast på standard-SKU-belastningsutjämnaren. *Standard* Befintliga kluster med *grundläggande* SKU belastningsutjämnare och API-server auktoriserade IP-adressintervall konfigureras fortsätter att fungera som det är men kan inte migreras till en *Standard* SKU belastningsutjämningsutjämning. Dessa befintliga kluster fortsätter också att fungera om deras Kubernetes version eller kontrollplan uppgraderas.

## <a name="before-you-begin"></a>Innan du börjar

API-server auktoriserade IP-intervall fungerar bara för nya AKS-kluster som du skapar. Den här artikeln visar hur du skapar ett AKS-kluster med Hjälp av Azure CLI.

Du behöver Azure CLI version 2.0.76 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa  [Installera Azure CLI 2.0][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Översikt över API-serverauktoriserat IP-intervall

Kubernetes API-server är hur de underliggande Kubernetes API:erna exponeras. Den här komponenten tillhandahåller interaktionen `kubectl` för hanteringsverktyg, till exempel eller kubernetes instrumentpanelen. AKS tillhandahåller en klusterhanterare för en klient med en dedikerad API-server. Som standard tilldelas API-servern en offentlig IP-adress och du bör styra åtkomsten med hjälp av rollbaserade åtkomstkontroller (RBAC).

För att säkra åtkomsten till den annars allmänt tillgängliga AKS-kontrollplan/API-servern kan du aktivera och använda auktoriserade IP-intervall. Dessa auktoriserade IP-intervall tillåter endast definierade IP-adressintervall att kommunicera med API-servern. En begäran till API-servern från en IP-adress som inte ingår i dessa auktoriserade IP-intervall blockeras. Fortsätt att använda RBAC för att auktorisera användare och de åtgärder de begär.

Mer information om API-servern och andra klusterkomponenter finns i [Kubernetes grundläggande begrepp för AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Skapa ett AKS-kluster med API-serverauktoriserat IP-intervall aktiverat

API-server auktoriserade IP-intervall fungerar bara för nya AKS-kluster. Skapa ett kluster med parametern [az aks create][az-aks-create] och specify the *--api-server-authorized-ip-ranges* parameter to provide a list of authorized IP address ranges. Dessa IP-adressintervall är vanligtvis adressintervall som används av lokala nätverk eller offentliga IP-adresser. När du anger ett CIDR-intervall börjar du med den första IP-adressen i intervallet. Till exempel är *137.117.106.90/29* ett giltigt intervall, men se till att du anger den första IP-adressen i intervallet, till exempel *137.117.106.88/29*.

> [!IMPORTANT]
> Som standard använder klustret [standard-SKU-belastningsutjämnaren][standard-sku-lb] som du kan använda för att konfigurera den utgående gatewayen. När du aktiverar API-serverauktoriserat IP-intervall när klustret skapas tillåts även den offentliga IP-adressen för klustret som standard utöver de intervall som du anger. Om du anger *""* eller inget värde för *--api-server-authorized-ip-ranges*inaktiveras API-serverauktoriserat IP-intervall. Observera att om du använder PowerShell använder du *--api-server-authorized-ip-ranges=""* (med likhetstecken) för att undvika tolkningsproblem.

I följande exempel skapas ett kluster med en nod med namnet *myAKSCluster* i resursgruppen *myResourceGroup* med API-serverauktoriserade IP-intervall aktiverade. De tillåtna IP-adressintervallen är *73.140.245.0/24:*

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Du bör lägga till dessa intervall i en tillåt-lista:
> - Brandväggens offentliga IP-adress
> - Alla områden som representerar nätverk som du administrerar klustret från
> - Om du använder Azure Dev Spaces i AKS-klustret måste du tillåta [ytterligare intervall baserat på din region][dev-spaces-ranges].

> Den övre gränsen för antalet IP-intervall som du kan ange är 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Ange utgående IP-adresser för standard SKU-belastningsutjämnaren

När du skapar ett AKS-kluster tillåts även dessa adresser eller prefix om du anger utgående IP-adresser eller prefix för klustret. Ett exempel:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

I exemplet ovan tillåts alla IP-adresser som anges i parametern *--load-balancer-outbound-ip-prefix* tillsammans med IP-adresserna i parametern *--api-server-authorized-ip-ranges.*

Du kan också ange parametern *--load-balancer-outbound-ip-prefix* för att tillåta ip-prefix för utgående belastningsutjämnare.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Tillåt endast den utgående offentliga IP-adressen för standard SKU-belastningsutjämnaren

När du aktiverar API-serverauktoriserade IP-intervall under klusterskapande tillåts även den utgående offentliga IP-adressen för standard SKU-belastningsutjämnaren för klustret som standard utöver de intervall som du anger. Om du bara vill tillåta den utgående offentliga IP-adressen för standard SKU-belastningsutjämnaren använder du *parametern 0.0.0.0/32* när parametern *--api-server-authorized-ip-ranges* anges.

I följande exempel tillåts endast den utgående offentliga IP-adressen för Standard SKU-belastningsutjämnaren och du kan bara komma åt API-servern från noderna i klustret.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Uppdatera ett klusters API-server auktoriserade IP-intervall

Om du vill uppdatera API-serverns auktoriserade IP-intervall i ett befintligt kluster använder du az [aks update-kommando][az-aks-update] och använder *parametrarna --api-server-authorized-ip-ranges*, *--load-balancer-outbound-ip-prefix ,* *--load-balancer-outbound-ips*eller *--load-balancer-outbound-ip-prefix parameters.*

I följande exempel uppdateras API-serverauktoriserade IP-områden i klustret *myAKSCluster* i resursgruppen *myResourceGroup*. IP-adressintervallet för att auktorisera är *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Du kan också använda *parametern 0.0.0.0/32* när du anger parametern *--api-server-authorized-ip-ranges* för att endast tillåta den offentliga IP-adressen för standard SKU-belastningsutjämnaren.

## <a name="disable-authorized-ip-ranges"></a>Inaktivera auktoriserade IP-intervall

Om du vill inaktivera auktoriserade IP-intervall använder du [az aks-uppdatering][az-aks-update] och anger ett tomt intervall för att inaktivera API-serverauktoriserat IP-intervall. Ett exempel:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du aktiverat API-serverauktoriserat IP-intervall. Den här metoden är en del av hur du kan köra ett säkert AKS-kluster.

Mer information finns i [Säkerhetsbegrepp för program och kluster i AKS][concepts-security] och [metodtips för klustersäkerhet och uppgraderingar i AKS][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
