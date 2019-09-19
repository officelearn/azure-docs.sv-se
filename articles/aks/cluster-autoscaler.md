---
title: Använda den automatiska skalnings tjänsten för kluster i Azure Kubernetes service (AKS)
description: Lär dig hur du använder kluster autoskalning för att automatiskt skala klustret så att det uppfyller program kraven i ett Azure Kubernetes service-kluster (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: e96d501196a629c7e37de7e5ad66b68863bf556f
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097903"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>För hands version – Skala automatiskt ett kluster så att det uppfyller program kraven på Azure Kubernetes service (AKS)

För att hålla dig uppdaterad med program krav i Azure Kubernetes service (AKS) kan du behöva justera antalet noder som kör arbets belastningarna. Komponenten för kluster autoskalning kan bevaka poddar i klustret som inte kan schemaläggas på grund av resurs begränsningar. När problem upptäcks ökas antalet noder i en Node-pool för att uppfylla programmets krav. Noderna kontrol leras också regelbundet för att det inte ska finnas någon poddar, med antalet noder som sedan kan minskas efter behov. Med den här möjligheten att automatiskt skala upp eller ned antalet noder i AKS-klustret kan du köra ett effektivt, kostnads effektivt kluster.

Den här artikeln visar hur du aktiverar och hanterar klustrets autoskalning i ett AKS-kluster. Kluster autoskalning ska endast testas i för hands versionen i AKS-kluster.

> [!IMPORTANT]
> AKS för hands versions funktioner är självbetjänings deltagande. För hands versioner tillhandahålls "i befintligt skick" och "som tillgängliga" och undantas från service nivå avtalen och den begränsade garantin. AKS för hands versionerna omfattas delvis av kund supporten på bästa möjliga sätt. Dessa funktioner är därför inte avsedda att användas för produktion. Om du vill ha ytterligare information kan du läsa följande artiklar om support:
>
> * [Support principer för AKS][aks-support-policies]
> * [Vanliga frågor och svar om support för Azure][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI-version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Installera AKS-Preview CLI-tillägg

Om du vill använda den automatiska skalnings versionen av klustret behöver du *AKS-Preview CLI-* tillägget 0.4.12 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som använder kluster autoskalning:

* Det går inte att använda Dirigerings tillägget för HTTP-program.

## <a name="about-the-cluster-autoscaler"></a>Om klustrets autoskalning

För att justera för att ändra program krav, till exempel mellan arbets dagar och kväll eller på en helg, behöver kluster ofta ett sätt att skala automatiskt. AKS-kluster kan skalas på ett av två sätt:

* Den **automatiska skalnings** funktionen i klustret söker efter poddar som inte kan schemaläggas på noder på grund av resurs begränsningar. Klustret ökar sedan automatiskt antalet noder.
* Den **horisontella Pod-autoskalning** använder mått servern i ett Kubernetes-kluster för att övervaka resurs behovet för poddar. Om ett program behöver fler resurser ökas antalet poddar automatiskt för att uppfylla behovet.

![Autoskalning av klustret och horisontell Pod-autoskalning fungerar ofta tillsammans för att stödja nödvändiga program krav](media/autoscaler/cluster-autoscaler.png)

Både den automatiska skalnings tjänsten för Pod och klustret kan också minska antalet poddar och noder efter behov. Klustrets autoskalning minskar antalet noder när det finns outnyttjad kapacitet under en tids period. Poddar på en nod som ska tas bort av klustrets autoskalning är säkert schemalagt i klustret. Klustrets autoskalning kan inte skalas ned om poddar inte kan flyttas, till exempel i följande situationer:

* En pod har skapats direkt och backas inte upp av ett kontrollant-objekt, t. ex. en distribution eller replik uppsättning.
* En POD störnings budget (PDB) är för begränsad och tillåter inte att antalet poddar kan falla under ett visst tröskelvärde.
* En POD använder Node-väljare eller anti-tillhörighet som inte kan användas om de är schemalagda på en annan nod.

För mer information om hur klustrets autoskalning inte kan skalas ned, se [vilka typer av poddar kan förhindra att klustret autoskalning tar bort en nod?][autoscaler-scaledown]

Klustrets autoskalning använder start parametrar för saker som tidsintervall mellan skalnings händelser och resurs trösklar. Dessa parametrar definieras av Azure-plattformen och är för närvarande inte tillgängliga för dig att justera. Mer information om vilka parametrar som används av klustret finns i [Vad är klustrets parametrar för autoskalning?][autoscaler-parameters].

De automatiska skalningarna i kluster och horisontella Pod kan fungera tillsammans och båda är ofta distribuerade i ett kluster. När den här funktionen kombineras, fokuserar den horisontella Pod autoskalning på att köra antalet poddar som krävs för att uppfylla programmets krav. Klustrets autoskalning fokuserar på att köra antalet noder som krävs för att stödja den schemalagda poddar.

> [!NOTE]
> Manuell skalning är inaktive rad när du använder klustret autoskalning. Låt klustret autoscaler fastställa antalet noder som krävs. Om du vill skala klustret manuellt [inaktiverar du klustrets autoskalning](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Skapa ett AKS-kluster och aktivera kluster autoskalning

Om du behöver skapa ett AKS-kluster använder du kommandot [AZ AKS Create][az-aks-create] . Om du vill aktivera och konfigurera klustrets autoskalning på nodens nod för klustret använder du parametern *--Enable-Cluster-autoscaleor* och anger en nod *--min-Count* och *--Max-Count*.

> [!IMPORTANT]
> Klustrets autoskalning är en Kubernetes-komponent. Även om AKS-klustret använder en skalnings uppsättning för virtuella datorer för noderna, ska du inte manuellt aktivera eller redigera inställningarna för skalnings uppsättning autoskalning i Azure Portal eller med hjälp av Azure CLI. Låt Kubernetes-klustret hantera de nödvändiga skalnings inställningarna. Mer information finns i [kan jag ändra AKS-resurserna i noden resurs grupp?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

I följande exempel skapas ett AKS-kluster med en pool med flera noder som backas upp av en skalnings uppsättning för virtuella datorer. Det aktiverar också klustrets autoskalning på nodens nod för klustret och anger minst *1* och högst *3* noder:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> Om du anger en *--Kubernetes-version* när den `az aks create`körs måste den versionen uppfylla eller överskrida det lägsta versions nummer som anges i föregående avsnitt [innan du börjar](#before-you-begin) .

Det tar några minuter att skapa klustret och konfigurera inställningarna för den automatiska skalnings inställningen i klustret.

## <a name="change-the-cluster-autoscaler-settings"></a>Ändra inställningarna för kluster autoskalning

> [!IMPORTANT]
> Om funktionen *flera agenter* är aktive rad för din prenumeration går du vidare till [avsnittet autoskalning med flera agenter](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Kluster med flera agenter aktiverade kräver `az aks nodepool` att kommando uppsättningen ändrar egenskaper för Node-pooler i stället för. `az aks` I anvisningarna nedan förutsätter vi att du inte har aktiverat flera noder i pooler. Du kan kontrol lera om du har aktiverat, `az feature  list -o table` köra och leta `Microsoft.ContainerService/multiagentpoolpreview`efter.

I föregående steg för att skapa ett AKS-kluster eller uppdatera en befintlig Node-pool, angavs det lägsta antalet noder för klustrets Autoskala till *1*, och det maximala antalet noder hade angetts till *3*. När dina program kräver ändringar kan du behöva justera antalet noder i den automatiska skalnings tjänsten för klustret.

Om du vill ändra antalet noder använder du kommandot [AZ AKS Update][az-aks-update] .

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Exemplet ovan uppdaterar kluster autoskalning på den enskild Node-poolen i *myAKSCluster* till minst *1* och högst *5* noder.

> [!NOTE]
> Under för hands versionen kan du inte ange ett högre lägsta antal noder än vad som för närvarande har angetts för Node-poolen. Om du till exempel har angett till *1*för antal, kan du inte uppdatera det minsta antalet till *3*.

Övervaka prestanda för dina program och tjänster och justera antalet noder i den automatiska skalnings tjänsten för klustret så att de matchar de nödvändiga prestanda.

## <a name="disable-the-cluster-autoscaler"></a>Inaktivera klustrets autoskalning

Om du inte längre vill använda klustrets autoskalning kan du inaktivera det med kommandot [AZ AKS Update][az-aks-update] och ange parametern *--disable-Cluster-autoscaler* . Noderna tas inte bort när klustrets autoskalning är inaktive rad.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Du kan skala klustret manuellt när du har inaktiverat kluster autoskalning med kommandot [AZ AKS Scale][az-aks-scale] . Om du använder den vågräta Pod autoskalning, fortsätter funktionen att köras med den automatiska skalnings funktionen för klustret inaktive rad, men poddar kan inte schemaläggas om alla resurs resurser används.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Återaktivera en inaktive rad kluster autoskalning

Om du vill återaktivera klustrets autoskalning i ett befintligt kluster kan du återaktivera det med kommandot [AZ AKS Update][az-aks-update] och ange parametern *--Enable-Cluster-autoscaler* .

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Använd kluster autoskalning med flera noder aktiverade

Klustrets autoskalning kan användas tillsammans med funktionen för för [hands versions funktionen för flera noder](use-multiple-node-pools.md) aktiverade. Följ det dokumentet för att lära dig hur du aktiverar flera resurspooler och lägger till ytterligare resurspooler i ett befintligt kluster. När du använder båda funktionerna tillsammans aktiverar du klustrets autoskalning på varje enskild nod i klustret och kan skicka unika regler för automatisk skalning till var och en.

Kommandot nedan förutsätter att du följde de [inledande anvisningarna](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) tidigare i det här dokumentet och du vill uppdatera en befintlig nods Pools Max antal från *3* till *5*. Använd kommandot [AZ AKS nodepool Update][az-aks-nodepool-update] för att uppdatera en befintlig Node-uppsättnings inställningar.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Klustret autoskalning kan inaktive ras med [AZ AKS nodepool Update][az-aks-nodepool-update] och skicka `--disable-cluster-autoscaler` parametern.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln visade vi hur du automatiskt skalar antalet AKS-noder. Du kan också använda den vågräta Pod autoskalning för att automatiskt justera antalet poddar som kör programmet. Anvisningar om hur du använder den vågräta Pod-autoskalning finns i [skala program i AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
