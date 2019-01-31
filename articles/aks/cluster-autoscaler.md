---
title: Använd autoskalningen kluster i Azure Kubernetes Service (AKS)
description: Lär dig hur du använder kluster autoskalningen för att skala automatiskt ditt kluster för att uppfylla krav på program i ett kluster i Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/29/2019
ms.author: iainfou
ms.openlocfilehash: 6b2302e69c9412170b55df4bfd8c1df5a9f75ef3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55479294"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Skala automatiskt ett kluster för att uppfylla krav på program på Azure Kubernetes Service (AKS)

Om du vill hålla jämna steg med behov i Azure Kubernetes Service (AKS), kan du behöva justera antalet noder som kör dina arbetsbelastningar. Komponenten kluster autoskalningen kan bevaka poddar i ditt kluster inte kan schemaläggas på grund av resursbegränsningar. När problem identifieras måste ökas antalet noder för att uppfylla programmets efterfrågan. Noder kontrolleras även regelbundet om bristande kör poddar, med antalet noder som sedan minskar efter behov. Den här möjligheten att automatiskt skala upp eller ned antalet noder i AKS-kluster kan du köra en effektiv och kostnadseffektiv kluster.

Den här artikeln visar hur du aktiverar och hanterar klustret autoskalningen i ett AKS-kluster.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.55 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

AKS-kluster som har stöd för autoskalningen kluster måste använda VM-skalningsuppsättningar och kör Kubernetes-version *1.12.4* eller senare. Den här stöd finns i förhandsversion. Om du vill anmäla dig och skapa kluster som använder skalningsuppsättningar, installera den *aks-förhandsversion* Azure CLI tillägget med hjälp av den [az-tillägget lägger du till] [ az-extension-add] kommandot, som visas i följande exempel:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> När du installerar den *aks-förhandsversion* tillägg, varje AKS-kluster som du skapar använder scale set förhandsversion-distributionsmodellen. Avanmäla dig och skapa regular, stöds fullt ut kluster genom att ta bort tillägget med hjälp av `az extension remove --name aks-preview`.

## <a name="about-the-cluster-autoscaler"></a>Om klustret autoskalningen

Att anpassas till olika behov, t.ex. mellan workday och kväll eller på en helg kluster ofta behöver ett sätt att skala automatiskt. AKS-kluster kan skalas i ett av två sätt:

* Den **kluster autoskalningen** ur för poddar som inte kan schemaläggas till noder på grund av resursbegränsningar. Klustret automatiskt sedan ökar antalet noder.
* Den **vågrät pod autoskalningen** använder mått-Server i ett Kubernetes-kluster för att övervaka resursbehov av poddar. Om en tjänst behöver mer resurser, ökar antalet poddar automatiskt för att möta efterfrågan.

![Klustret autoskalningen och vågräta pod autoskalningen arbetar ofta tillsammans för att stödja kraven för programmet som krävs](media/autoscaler/cluster-autoscaler.png)

Vågrät pod autoskalningen såväl kluster autoskalningen kan då också minska antalet poddar och noder efter behov. Klustret autoskalningen minskar antalet noder när det har förekommit outnyttjad kapacitet för en viss tidsperiod. Poddar på en nod tas bort av klustret autoskalningen schemaläggs på ett säkert sätt någon annanstans i klustret. Autoskalningen klustret kan vara det går inte att skala ned om poddar inte kan flytta, till exempel i följande situationer:

* En pod skapas direkt och omfattas inte av en domänkontrollant objekt, sådan distribution eller repliken uppsättning.
* En pod avbrott budget (PDB) är för restriktiva och tillåter inte att antalet poddar vara faller under ett visst tröskelvärde.
* En pod använder noden väljare eller anti-tillhörighet som inte kan användas om schemalagd på en annan nod.

Läs mer om hur klustret autoskalningen kanske inte att skala ned [vilka typer av poddar kan förhindra att klustret autoskalningen tar bort en nod?][autoscaler-scaledown]

Autoskalningen klustret använder startparametrar för saker som tidsintervall mellan skalningshändelser och resurströskelvärden. Dessa parametrar definieras av Azure-plattformen och visas inte för närvarande att justera. Mer information om vilka parametrar som klustret autoskalningen använder, finns i [vad är autoskalningen Klusterparametrar?] [autoscaler-parameters].

Två autoscalers kan fungera tillsammans och ofta båda distribueras i ett kluster. I kombination, fokuserar vågrät pod autoskalningen på Kör antalet poddar som krävs för att uppfylla programmets efterfrågan. Klustret autoskalningen fokuserar på Kör antalet noder som krävs för att stödja schemalagda poddarna.

> [!NOTE]
> Manuell skalning är inaktiverat när du använder kluster autoskalningen. Låt kluster autoskalningen bestämma antalet noder som krävs. Om du vill manuellt skala ditt kluster [inaktivera kluster autoskalningen](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Skapa ett AKS-kluster och aktivera autoskalningen kluster

Om du vill skapa ett AKS-kluster kan du använda den [az aks skapa] [ az-aks-create] kommando. Ange en *--kubernetes-version* som uppfyller eller överskrider det lägsta versionsnumret som krävs enligt beskrivningen i det föregående [innan du börjar](#before-you-begin) avsnittet. Om du vill aktivera och konfigurera autoskalningen kluster måste använda den *--enable-kluster-autoskalningen* parametern och ange en nod *– minsta antal* och *--maximalt antal*.

I följande exempel skapar ett AKS-kluster med virtual machine scale Sets och klustret autoskalningen aktiverad och kräver minst *1* och högst *3* noder:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location canadaeast

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --kubernetes-version 1.12.4 \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Det tar några minuter att skapa klustret och konfigurera klusterinställningarna autoskalningen.

### <a name="enable-the-cluster-autoscaler-on-an-existing-aks-cluster"></a>Aktivera autoskalningen kluster i ett befintligt AKS-kluster

Du kan aktivera autoskalningen klustret på ett AKS-kluster som uppfyller kraven som beskrivs i det föregående [innan du börjar](#before-you-begin) avsnittet. Använd den [az aks uppdatera] [ az-aks-update] kommandot och väljer att *--enable-kluster-autoskalningen*, ange sedan en nod *– minsta antal* och *--maximalt antal*. I följande exempel aktiveras klustret autoskalningen i ett befintligt kluster som använder minst *1* och högst *3* noder:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Om det lägsta nodantalet är större än det befintliga antalet noder i klustret, tar det några minuter att skapa ytterligare noder.

## <a name="change-the-cluster-autoscaler-settings"></a>Ändra autoskalningen klusterinställningar

I föregående steg för att skapa eller uppdatera ett befintligt AKS-kluster, autoskalningen minsta antalet klusternoder var inställd *1*, och det högsta antalet noder har angetts *3*. Som programmet ändrar kraven kan du behöva justera antalet klusternoder autoskalningen.

Du kan ändra antalet noder med den [az aks uppdatera] [ az-aks-update] kommandot och ange ett lägsta och högsta värde. I följande exempel anges den *– minsta antal* till *1* och *--maximalt antal* till *5*:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

> [!NOTE]
> Du kan inte ange ett högre lägsta nodantalet än vad som har angetts för klustret i förhandsversionen. Exempel: Om du har för närvarande minsta antal inställd *1*, du kan inte uppdatera min antal till *3*.

Övervaka prestanda för dina program och tjänster och justera klustret autoskalningen nod antal så att den matchar den nödvändiga prestandan.

## <a name="disable-the-cluster-autoscaler"></a>Inaktivera autoskalningen kluster

Om du inte längre vill använda autoskalningen kluster kan du inaktivera den med hjälp av den [az aks uppdatera] [ az-aks-update] kommando. Noder tas inte bort när klustret autoskalningen är inaktiverad.

Om du vill ta bort klustret autoskalningen, ange den *--disable-kluster-autoskalningen* parametern, som visas i följande exempel:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Du kan manuellt skala ditt kluster med hjälp av den [az aks skala] [ az-aks-scale] kommando. Om du använder horisontell pod autoskalningen funktionen fortsätter att köras med klustret autoskalningen inaktiverad, men poddar kan det sluta kunde inte schemaläggas om noden resurser finns i användning.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du automatiskt skala antalet AKS-noder. Du kan också använda vågrät pod autoskalningen för att automatiskt justera antalet poddar som kör programmet. Anvisningar om hur du använder horisontell pod autoskalningen finns i [skala program i AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
