---
title: API-servern auktoriserade IP-intervall i Azure Kubernetes service (AKS)
description: Lär dig hur du skyddar klustret med hjälp av ett IP-adressintervall för åtkomst till API-servern i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 593f9e0b335e6f4d62c76ce92f833ff4e9143372
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126617"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Säker åtkomst till API-servern med behöriga IP-adressintervall i Azure Kubernetes service (AKS)

I Kubernetes tar API-servern emot begär Anden för att utföra åtgärder i klustret, till exempel för att skapa resurser eller skala antalet noder. API-servern är det centrala sättet att interagera med och hantera ett kluster. För att förbättra kluster säkerheten och minimera angrepp bör API-servern endast vara tillgänglig från en begränsad uppsättning IP-adressintervall.

Den här artikeln visar hur du använder tillåtna IP-adressintervall för API-Server för att begränsa vilka IP-adresser och CIDR-områden som kan komma åt kontroll planet.

> [!IMPORTANT]
> I nya kluster stöds endast tillåtna IP-adressintervall för API-server på SKU: n för *standard* -SKU: n. Befintliga kluster med den *grundläggande* SKU-belastningsutjämnaren och den API-server som har kon figurer ATS, fortsätter att fungera som det är men kan inte migreras till en *standard* -SKU. De befintliga klustren kommer också att fortsätta fungera om deras Kubernetes-version eller kontroll plan uppgraderas.

## <a name="before-you-begin"></a>Innan du börjar

API-serverns auktoriserade IP-intervall fungerar bara för nya AKS-kluster som du skapar. Den här artikeln visar hur du skapar ett AKS-kluster med hjälp av Azure CLI.

Du behöver Azure CLI-versionen 2.0.76 eller senare installerad och konfigurerad. Kör  `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Översikt över tillåtna IP-intervall för API-Server

Kubernetes-API-servern är hur de underliggande Kubernetes-API: erna exponeras. Den här komponenten ger interaktion för hanterings verktyg, till exempel `kubectl` eller Kubernetes-instrumentpanelen. AKS tillhandahåller en kluster hanterare för en enda klient, med en dedikerad API-Server. Som standard tilldelas API-servern en offentlig IP-adress och du bör kontrol lera åtkomst med hjälp av rollbaserad åtkomst kontroll (RBAC).

Du kan aktivera och använda auktoriserade IP-intervall för att skydda åtkomsten till den allmänt tillgängliga AKS kontroll planet/API-servern. Dessa auktoriserade IP-adressintervall tillåter endast att definierade IP-adressintervall kommunicerar med API-servern. En begäran till API-servern från en IP-adress som inte tillhör dessa auktoriserade IP-intervall är blockerad. Fortsätt att använda RBAC för att auktorisera användare och de åtgärder som de begär.

Mer information om API-servern och andra kluster komponenter finns i [Kubernetes Core Concepts for AKS][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Skapa ett AKS-kluster med autentiserade IP-intervall för API-servern

API-serverns auktoriserade IP-intervall fungerar bara för nya AKS-kluster. Skapa ett kluster med hjälp av parametern [AZ AKS Create][az-aks-create] och ange parametern *--API-Server-auktoriserat-IP-Ranges* för att tillhandahålla en lista över tillåtna IP-adressintervall. Dessa IP-adressintervall är vanligt vis adress intervall som används av dina lokala nätverk eller offentliga IP-adresser. När du anger ett CIDR-intervall börjar du med den första IP-adressen i intervallet. Till exempel är *137.117.106.90/29* ett giltigt intervall, men se till att du anger den första IP-adressen i intervallet, till exempel *137.117.106.88/29*.

> [!IMPORTANT]
> Som standard använder klustret [standard SKU-belastningsutjämnaren][standard-sku-lb] som du kan använda för att konfigurera den utgående gatewayen. När du aktiverar tillåtna IP-adressintervall för API-servern när klustret skapas, tillåts även den offentliga IP-adressen för klustret som standard utöver de intervall som du anger. Om du anger *""* eller inget värde för *--API-Server-auktoriserade-IP-intervall*, inaktive ras API-servern auktoriserade IP-intervall.

I följande exempel skapas ett kluster med en nod med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup* med autentiserade IP-adressintervall för API-servern. De IP-adressintervall som tillåts är *73.140.245.0/24*:

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
> Du bör lägga till dessa intervall i en lista över tillåtna:
> - Brand väggens offentliga IP-adress
> - Alla intervall som representerar nätverk som du ska administrera klustret från
> - Om du använder Azure dev Spaces i ditt AKS-kluster måste du tillåta [ytterligare intervall baserat på din region][dev-spaces-ranges].

> Den övre gränsen för antalet IP-intervall som du kan ange är 3500. 

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Ange utgående IP-adresser för belastningsutjämnaren för standard-SKU

När du skapar ett AKS-kluster och anger utgående IP-adresser eller prefix för klustret, tillåts även dessa adresser eller prefix. Exempel:

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

I ovanstående exempel är alla IP-adresser som finns i parametern *--Load-Balancer-utgående-IP-prefix* tillåtna tillsammans med IP-adresserna i parametern *--API-Server-auktoriserat-IP-Ranges* .

Alternativt kan du ange parametern *--Load-Balancer-utgående-IP-prefix* för att tillåta utgående IP-prefix för belastningsutjämnare.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Tillåt endast den utgående offentliga IP-adressen för standard-SKU-belastningsutjämnaren

När du aktiverar API-serverns auktoriserade IP-intervall när klustret skapas, tillåts även den utgående offentliga IP-adressen för standard-SKU: n för klustret också som standard utöver de intervall som du anger. Om du bara vill tillåta den utgående offentliga IP-adressen för belastningsutjämnaren för standard-SKU använder du *0.0.0.0/32* när du anger parametern *--API-Server-auktoriserat-IP-intervall* .

I följande exempel tillåts endast den utgående offentliga IP-adressen för standard-SKU-belastningsutjämnaren tillåten och du kan bara komma åt API-servern från noderna i klustret.

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

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Uppdatera ett klusters API-Server auktoriserade IP-intervall

Om du vill uppdatera API-serverns auktoriserade IP-intervall i ett befintligt kluster, använder du kommandot [AZ AKS Update][az-aks-update] och använder parametrarna *--API-Server-auktoriserad-IP-Ranges*, *--Load-Balancer-utgående-IP-prefix*, *--Load-Balancer-utgående-* IP-eller *--Load-Balancer-utgående-IP-prefix* .

I följande exempel uppdateras API-servern auktoriserade IP-intervall i klustret med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*. Det IP-adressintervall som ska auktoriseras är *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Du kan också använda *0.0.0.0/32* när du anger parametern *--API-Server-Allowed-IP-Ranges* så att endast den offentliga IP-adressen för standard-SKU-belastningsutjämnaren tillåts.

## <a name="disable-authorized-ip-ranges"></a>Inaktivera auktoriserade IP-intervall

Om du vill inaktivera auktoriserade IP-intervall använder du [AZ AKS Update][az-aks-update] och anger ett tomt intervall för att inaktivera API-serverns auktoriserade IP-intervall. Exempel:

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
