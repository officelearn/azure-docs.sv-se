---
title: Preview - Lägga till en punktnodpool i ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du lägger till en spotnodpool i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622047"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Preview - Lägga till en punktnodpool i ett AKS-kluster (Azure Kubernetes Service)

En punktnodpool är en nodpool som backas upp av en [skalan för en dekor virtuell dator.][vmss-spot] Genom att använda spot-datorer för noder med AKS-klustret kan du dra nytta av outnyttjad kapacitet i Azure till en betydande kostnadsbesparingar. Mängden tillgänglig outnyttjad kapacitet varierar beroende på många faktorer, inklusive nodstorlek, region och tid på dagen.

När du distribuerar en spotnodpool allokerar Azure spotnoderna om det finns tillgänglig kapacitet. Men det finns inget serviceavtal för spotnoderna. En punktskalauppsättning som stöder spotnodpoolen distribueras i en enda feldomän och inte erbjuder några höga tillgänglighetsgarantier. När som helst när Azure behöver tillbaka kapaciteten kommer Azure-infrastrukturen att ta bort spotnoder.

Spotnoder är bra för arbetsbelastningar som kan hantera avbrott, tidiga uppsägningar eller vräkningar. Arbetsbelastningar som batchbearbetningsjobb, utvecklings- och testmiljöer och stora beräkningsarbetsbelastningar kan till exempel vara bra kandidater som ska schemaläggas på en spotnodpool.

I den här artikeln lägger du till en sekundär punktnodpool i ett befintligt AKS-kluster (Azure Kubernetes Service).

Den här artikeln förutsätter en grundläggande förståelse av kubernetes och Azure Load Balancer begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Den här funktionen är för närvarande en förhandsversion.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

När du skapar ett kluster för att använda en dekornodpool måste det klustret också använda skalningsuppsättningar för virtuell dator för nodpooler och *standard* SKU-belastningsutjämnaren. Du måste också lägga till ytterligare en nodpool när du har skapat klustret för att använda en punktnodpool. Att lägga till ytterligare en nodpool täcks i ett senare steg, men du måste först aktivera en förhandsgranskningsfunktion.

> [!IMPORTANT]
> AKS-förhandsgranskningsfunktioner är självbetjäning, opt-in. De tillhandahålls för att samla in feedback och buggar från vårt samhälle. I förhandsversion är dessa funktioner inte avsedda för produktionsanvändning. Funktioner i offentlig förhandsversion faller under "bästa möjliga" stöd. Hjälp från AKS tekniska supportteam är endast tillgänglig under kontorstid stillahavstid (PST). Mer information finns i följande supportartiklar:
>
> * [Aks-supportpolicyer][aks-support-policies]
> * [Vanliga frågor och svar om Azure-support][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Registrera förhandsgranskningsfunktionen för spotpoolpreview

Om du vill skapa ett AKS-kluster som använder en punktnodpool måste du aktivera flaggan *för spotpoolpreview-funktioner* i din prenumeration. Den här funktionen innehåller den senaste uppsättningen serviceförbättringar när du konfigurerar ett kluster.

> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte registrera den funktionen. När du har aktiverat vissa förhandsversionsfunktioner kan standardvärden användas för alla AKS-kluster som sedan skapas i prenumerationen. Aktivera inte förhandsgranskningsfunktioner på produktionsprenumerationer. Använd en separat prenumeration för att testa förhandsgranskningsfunktioner och samla in feedback.

Registrera flaggan *spotpoolpreview-funktion* med kommandot [az-funktionsregister][az-feature-register] som visas i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Det tar några minuter innan statusen *visas.* Du kan kontrollera registreringsstatus med kommandot [az-funktionslista:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av *Microsoft.ContainerService-resursprovidern* med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för förhandsversion

Om du vill skapa ett AKS-kluster som använder en dekornodpool behöver du *CLI-tilläggsversionen* 0.4.32 eller senare. Installera Azure *CLI-tillägget aks-preview* med kommandot [az extension add][az-extension-add] och sök sedan efter tillgängliga uppdateringar med kommandot az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster med en punktnodpool:

* En spotnodpool kan inte vara klustrets standardnodpool. En punktnodpool kan bara användas för en sekundär pool.
* Du kan inte uppgradera en spotnodpool eftersom dekornodpooler inte kan garantera avspärrning och avlopp. Du måste ersätta din befintliga spotnodpool med en ny för att kunna utföra åtgärder, till exempel uppgradera Kubernetes-versionen. Om du vill ersätta en spotnodpool skapar du en ny spotnodpool med en annan version av Kubernetes, väntar tills dess status är *Klar*och tar sedan bort den gamla nodpoolen.
* Kontrollplanet och nodpoolerna kan inte uppgraderas samtidigt. Du måste uppgradera dem separat eller ta bort spotnodpoolen för att uppgradera kontrollplanet och återstående nodpooler samtidigt.
* En dekornodspool måste använda skaluppsättningar för virtuella datorer.
* Du kan inte ändra ScaleSetPriority eller SpotMaxPrice när du har skapat.
* När spotmaxprice ställs in måste värdet vara -1 eller ett positivt värde med upp till fem decimaler.
* En spotnodpool har etiketten *kubernetes.azure.com/scalesetpriority:spot,* den *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*och systemkapslar har anti-affinitet.
* Du måste lägga till en [motsvarande tolerans för att][spot-toleration] schemalägga arbetsbelastningar på en spotnodpool.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Lägga till en punktnodpool i ett AKS-kluster

Du måste lägga till en punktnodpool i ett befintligt kluster som har flera nodpooler aktiverade. Mer information om hur du skapar ett AKS-kluster med flera nodpooler finns [här][use-multiple-node-pools].

Skapa en nodpool med [az aks-nodpoolen][az-aks-nodepool-add]add .
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Som standard skapar du en nodpool med *prioriteten* *Normal* i AKS-klustret när du skapar ett kluster med flera nodpooler. Kommandot ovan lägger till en hjälpnodspool i ett befintligt AKS-kluster med *prioriteten* *Spot*. *Prioriteten* *för Spot* gör nodpoolen till en spotnodpool. Parametern *vräkning-princip* är inställd på *Ta bort* i exemplet ovan, vilket är standardvärdet. När du anger att [vräkningsprincipen][eviction-policy] ska *tas bort*tas noder i den underliggande skalningsuppsättningen för nodpoolen bort när de vräkas. Du kan också ställa in vräkningsprincipen till *Deallocate*. När du ställer in vräkningsprincipen till *Deallocate*anges noder i den underliggande skalningsuppsättningen till tillståndet stoppad deallocated vid vräkning. Noder i tillståndet stoppad deallocated mot beräkningskvoten och kan orsaka problem med klusterskalning eller uppgradering. *Prioritets-* och *vräkningsprincipvärdena* kan bara anges när nodpoolen skapas. Dessa värden kan inte uppdateras senare.

Kommandot aktiverar också [kluster automatisk skalbar][cluster-autoscaler], som rekommenderas att använda med dekornodpooler. Baserat på arbetsbelastningarna som körs i klustret skalas klusterautomaten upp och skalar ned antalet noder i nodpoolen. För spotnodpooler skalar klusterautomatiskskalning upp antalet noder efter en vräkning om ytterligare noder fortfarande behövs. Om du ändrar det maximala antalet noder som en nodpool kan ha måste du också justera `maxCount` värdet som är associerat med klusterautomatskalningen. Om du inte använder en automatisk skalning av kluster minskar platspoolen så småningom till noll och kräver en manuell åtgärd för att ta emot ytterligare spotnoder.

> [!Important]
> Schemalägg endast arbetsbelastningar på spotnodpooler som kan hantera avbrott, till exempel batchbearbetningsjobb och testmiljöer. Vi rekommenderar att du ställer in [taints och tolerations][taints-tolerations] på spotnodpoolen för att säkerställa att endast arbetsbelastningar som kan hantera nodvräkningar schemaläggs på en spotnodpool. Till exempel lägger kommandot ovan ny standard en fläck av *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* så att endast poddar med motsvarande tolerans schemaläggs på den här noden.

## <a name="verify-the-spot-node-pool"></a>Verifiera den punktnodspoolen

Så här kontrollerar du att nodpoolen har lagts till som en punktnodpool:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Bekräfta *scaleSetPriority* är *Spot*.

Om du vill schemalägga en pod som ska köras på en spotnod lägger du till en toleration som motsvarar den befläckning som tillämpas på spotnoden. I följande exempel visas en del av en yaml-fil som definierar en tolerans som motsvarar en *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* behäftning som används i föregående steg.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

När en pod med den här toleransen distribueras kan Kubernetes schemalägga podden på noderna med den behäftade.

## <a name="max-price-for-a-spot-pool"></a>Max pris för en plats pool
[Prissättningen för spotinstanser är variabel][pricing-spot], baserat på region och SKU. Mer information finns i priser för [Linux][pricing-linux] och [Windows][pricing-windows].

Med rörlig prissättning har du möjlighet att ange ett maxpris, i US-dollar (USD), med upp till 5 decimaler. Värdet *0,98765* skulle till exempel vara ett maxpris på 0,98765 USD per timme. Om du ställer in maxpriset till *-1*kommer instansen inte att vräkas baserat på pris. Priset för instansen blir det aktuella priset för Spot eller priset för en standardinstans, beroende på vilket som är lägst, så länge det finns kapacitet och kvot tillgänglig.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du lägger till en spotnodpool i ett AKS-kluster. Mer information om hur du styr poddar över nodpooler finns [i Metodtips för avancerade schemaläggarfunktioner i AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md