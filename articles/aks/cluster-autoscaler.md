---
title: Använda klusterautomatskalningsverktyget i Azure Kubernetes Service (AKS)
description: Lär dig hur du använder kluster automatisk skalning för att automatiskt skala klustret för att uppfylla programkraven i ett AKS-kluster (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: 0b94865d81afc56c24d470012c668662f003a1b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596257"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Skala automatiskt ett kluster för att uppfylla programkraven på Azure Kubernetes Service (AKS)

För att hålla jämna steg med programkraven i Azure Kubernetes Service (AKS) kan du behöva justera antalet noder som kör dina arbetsbelastningar. Komponenten kluster automatisk skalbara kan titta efter poddar i klustret som inte kan schemaläggas på grund av resursbegränsningar. När problem upptäcks ökas antalet noder i en nodpool för att uppfylla programmets efterfrågan. Noder kontrolleras också regelbundet för brist på löpande poddar, med antalet noder minskade sedan efter behov. Med den här möjligheten att automatiskt skala upp eller ned antalet noder i AKS-klustret kan du köra ett effektivt och kostnadseffektivt kluster.

Den här artikeln visar hur du aktiverar och hanterar kluster automatisk skalning i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som använder kluster automatisk skalning:

* Http-programroutningstillägget kan inte användas.

## <a name="about-the-cluster-autoscaler"></a>Om kluster automatisk skalning

För att anpassa sig till ändrade programkrav, till exempel mellan arbetsdag och kväll eller på en helg, behöver kluster ofta ett sätt att automatiskt skala. AKS-kluster kan skalas på två sätt:

* **Klusterautomaten** söker efter poddar som inte kan schemaläggas på noder på grund av resursbegränsningar. Klustret ökar sedan automatiskt antalet noder.
* Den **horisontella pod-bildrensaren** använder Metrics Server i ett Kubernetes-kluster för att övervaka resursbehovet för poddar. Om ett program behöver mer resurser ökas antalet poddar automatiskt för att möta efterfrågan.

![Kluster automatisk skalning och horisontell pod automatisk skalning arbetar ofta tillsammans för att stödja de nödvändiga programkraven](media/autoscaler/cluster-autoscaler.png)

Både den horisontella pod autoscaler och kluster automatisk skalning kan också minska antalet poddar och noder efter behov. Kluster automatiskskalning minskar antalet noder när det har funnits outnyttjad kapacitet under en tidsperiod. Poddar på en nod som ska tas bort av kluster automatisk skalningsapparat är säkert schemalagda någon annanstans i klustret. Kluster automatisk skalning kanske inte kan skalas ned om poddar inte kan flytta, till exempel i följande situationer:

* En pod skapas direkt och backas inte upp av ett styrenhetsobjekt, till exempel en distribution eller replikuppsättning.
* En pod avbrott budget (PDB) är alltför restriktiv och tillåter inte antalet poddar att falla under ett visst tröskelvärde.
* En pod använder nodväljare eller antitillhörighet som inte kan uppfyllas om den schemaläggs på en annan nod.

Mer information om hur kluster automatisk skalning kanske inte kan skalas ned finns i Vilka typer av poddar kan förhindra att [klusteravkalkaren tar bort en nod?][autoscaler-scaledown]

Kluster automatiskskalning använder startparametrar för saker som tidsintervall mellan skalningshändelser och resurströskelvärden. Dessa parametrar definieras av Azure-plattformen och är för närvarande inte exponerade för att du ska justera. Mer information om vilka parametrar som klusterautomatsystemet använder finns i [Vilka parametrar för automatisk skalning av kluster?][autoscaler-parameters]

Klustret och den horisontella pod-gruppen kan arbeta tillsammans och distribueras ofta i ett kluster. När den kombineras fokuserar den horisontella pod-förankningsenheten på att köra antalet poddar som krävs för att möta programmets efterfrågan. Kluster automatiskskalning fokuserar på att köra antalet noder som krävs för att stödja schemalagda poddar.

> [!NOTE]
> Manuell skalning är inaktiverat när du använder kluster automatisk skalning. Låt kluster automatisk skalning bestämma det antal noder som krävs. Om du vill skala klustret manuellt [inaktiverar du klusterautomaten](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Skapa ett AKS-kluster och aktivera klusterautomatisk skalning

Om du behöver skapa ett AKS-kluster använder du kommandot [az aks create.][az-aks-create] Om du vill aktivera och konfigurera klusterautomatskalningsaren på nodpoolen för klustret använder du parametern *--enable-cluster-autoscaler* och anger en nod *--min-count* och *--max-count*.

> [!IMPORTANT]
> Kluster automatisk skalning är en Kubernetes komponent. Även om AKS-klustret använder en skalningsuppsättning för den virtuella datorn för noderna ska du inte aktivera eller redigera inställningar manuellt för automatisk skalning av skalning i Azure-portalen eller med hjälp av Azure CLI. Låt Kubernetes-kluster automatiskt skala de skalningsinställningar som krävs. Mer information finns i [Kan jag ändra AKS-resurserna i nodresursgruppen?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

I följande exempel skapas ett AKS-kluster med en enda nodpool som backas upp av en skalningsuppsättning för den virtuella datorn. Det gör det också möjligt för kluster automatisk skalbar på nodpoolen för klustret och anger minst *1* och högst *3* noder:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Det tar några minuter att skapa klustret och konfigurera inställningarna för automatisk skalning av kluster.

## <a name="change-the-cluster-autoscaler-settings"></a>Ändra inställningarna för automatisk skalning av kluster

> [!IMPORTANT]
> Om du har flera nodpooler i AKS-klustret går du till [avsnittet Automatisk skalning med flera agentpooler](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Kluster med flera agentpooler kräver `az aks nodepool` användning av kommandouppsättningen för `az aks`att ändra nodpoolspecifika egenskaper i stället för .

I föregående steg för att skapa ett AKS-kluster eller uppdatera en befintlig nodpool angavs minsta nodantalet för kluster automatisk skalning *till 1*och det maximala antalet noder angavs till *3*. När ditt program kräver förändring kan du behöva justera antalet nodnoder för klusternoder.

Om du vill ändra antalet noder använder du kommandot [az aks update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

I exemplet ovan uppdateras kluster automatisk skalbar på den enda nodpoolen i *myAKSCluster* till minst *1* och högst *5* noder.

> [!NOTE]
> Du kan inte ange ett högre minsta antal nod än vad som för närvarande anges för nodpoolen. Om du till exempel har minantal inställt på *1*kan du inte uppdatera antalet minuter till *3*.

Övervaka prestanda för dina program och tjänster och justera antalet klusternoder för automatisk skalning så att de matchar den prestanda som krävs.

## <a name="using-the-autoscaler-profile"></a>Använda profilen för automatisk skalning

Du kan också konfigurera mer detaljerad information om kluster automatisk skalning genom att ändra standardvärdena i den klusteromfattande autoskalningsprofilen. En nedskalningshändelse inträffar till exempel efter att noder har utnyttjats underutnyttjade efter 10 minuter. Om du hade arbetsbelastningar som kördes var 15:e minut kanske du vill ändra profilen för automatisk skalning för att skala ned under använda noder efter 15 eller 20 minuter. När du aktiverar klusterprofilen används en standardprofil om du inte anger olika inställningar. Profilen för automatisk skalning av kluster har följande inställningar som du kan uppdatera:

| Inställning                          | Beskrivning                                                                              | Standardvärde |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| skanningsintervall                    | Hur ofta kluster omvärderas för skala upp eller ned                                    | 10 sekunder    |
| skala ned-fördröjning-efter-tillägg       | Hur lång tid efter skala upp att skala ner utvärderingen återupptas                               | 10 minuter    |
| skala ned-fördröjning-efter-borttagning    | Hur lång tid efter nodborttagning som skala ner utvärderingen återupptas                          | skanningsintervall |
| skala ned-fördröjning-efter-fel   | Hur länge efter nedskalningsfel återupptas utvärderingen                     | 3 minuter     |
| nedskalad-onödiga tid         | Hur länge en nod ska vara onödiga innan den kan skalas ned                  | 10 minuter    |
| nedskala-ner-oläslig tid          | Hur länge en oläslig nod ska vara onödiga innan den kan skalas ned         | 20 minuter    |
| nedvärderingströskel | Nodutnyttjandenivå, definierad som summan av begärda resurser dividerat med kapacitet, under vilken en nod kan beaktas för nedskalning | 0,5 |
| max-graciös-uppsägning-sek     | Maximalt antal sekunder som klusterautomatenheten väntar på pod-avslutning när du försöker skala ned en nod. | 600 sekunder   |

> [!IMPORTANT]
> Profilen för automatisk skalning av kluster påverkar alla nodpooler som använder kluster automatisk skalbar. Du kan inte ange en profil för automatisk skalning per nodpool.

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för förhandsversion

Om du vill ange profilen för inställningar för klusterinställningar för kluster behöver du CLI-tilläggsversionen 0.4.30 eller senare *aks-förhandsversion.* Installera Azure *CLI-tillägget aks-preview* med kommandot [az extension add][az-extension-add] och sök sedan efter tillgängliga uppdateringar med kommandot az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Ange profilen för automatisk skalning av kluster på ett befintligt AKS-kluster

Använd kommandot [az aks update][az-aks-update] med parametern *kluster-autoscaler-profile* för att ställa in klustrets autoskalningsprofil i klustret. I följande exempel konfigureras skanningsintervallinställningen som 30:00 i profilen.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

När du aktiverar kluster automatisk skalbar på nodpooler i klustret, kommer dessa kluster också att använda klustrets autoskalningsprofil. Ett exempel:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> När du ställer in profilen för automatisk skalning av kluster börjar alla befintliga nodpooler med klusterautomaten aktiverad att använda profilen omedelbart.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Ange profilen för klusterprofilen för automatisk skalning när du skapar ett AKS-kluster

Du kan också använda parametern *kluster-autoscaler-profile* när du skapar klustret. Ett exempel:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Kommandot ovan skapar ett AKS-kluster och definierar skanningsintervallet som 30 sekunder för den klusteromfattande autoskalningsprofilen. Kommandot aktiverar också kluster automatiskskalning på den första nodpoolen, anger minsta nodantal till 1 och det maximala antalet noder till 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Återställa profil för automatisk skalning av kluster till standardvärden

Använd kommandot [az aks update][az-aks-update] för att återställa klustrets autoskalningsprofil i klustret.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Inaktivera kluster automatisk skalning

Om du inte längre vill använda kluster automatisk skalning, kan du inaktivera den med kommandot [az aks update,][az-aks-update] ange parametern *--disable-cluster-autoscaler.* Noder tas inte bort när kluster automatisk skalning inaktiveras.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Du kan skala klustret manuellt när du har inaktiverat klusteravkalkaren med kommandot [az aks scale.][az-aks-scale] Om du använder den automatiska vågen pod-skalningen fortsätter den funktionen att köras med klusterautomatenheten inaktiverad, men poddar kan inte schemaläggas om alla nodresurser används.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Återaktivera en inaktiverad kluster automatisk skalning

Om du vill återaktivera klusterautomatiskskalning på ett befintligt kluster kan du återaktivera det med kommandot [az aks update][az-aks-update] och ange parametrarna *--enable-cluster-autoscaler*, *--min-count*och *--max-count.*

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Hämta loggar och status för klusterloggar för automatisk skalning

För att diagnostisera och felsöka händelser för automatisk skalning kan loggar och status hämtas från tillägget autoskalning.

AKS hanterar kluster automatisk skalning för din räkning och kör den i det hanterade kontrollplanet. Huvudnodsloggar måste vara konfigurerade för att kunna visas som ett resultat.

Så här konfigurerar du loggar som ska skickas från klusterautomaten till Log Analytics.

1. Ställ in en regel för diagnostikloggar för att skicka kluster-autoscaler loggar till Log Analytics. [Instruktionerna är detaljerade här,](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)se `cluster-autoscaler` till att du markerar rutan för när du väljer alternativ för "Loggar".
1. Klicka på avsnittet "Loggar" i klustret via Azure-portalen.
1. Ange följande exempelfråga i Logganalys:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Du bör se loggar som liknar följande exempel så länge det finns loggar att hämta.

![Logga Analytics-loggar](media/autoscaler/autoscaler-logs.png)

Kluster automatisk skalbar kommer också att skriva ut `cluster-autoscaler-status`hälsostatus till en configmap med namnet . Om du vill hämta dessa `kubectl` loggar kör du följande kommando. En hälsostatus rapporteras för varje nodpool som konfigurerats med kluster automatisk skalbar.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Om du vill veta mer om vad som loggas från den automatiska skalningsapparaten läser du vanliga frågor och svar om [Kubernetes/autoscaler GitHub-projektet](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why).

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Använda kluster automatisk skalbar med flera nodpooler aktiverade

Kluster automatisk skalbar kan användas tillsammans med [flera nodpooler](use-multiple-node-pools.md) aktiverade. Följ det dokumentet om du vill lära dig hur du aktiverar flera nodpooler och lägger till ytterligare nodpooler i ett befintligt kluster. När du använder båda funktionerna tillsammans aktiverar du klusterautomatskalningsaren på varje enskild nodpool i klustret och kan skicka unika regler för automatisk skalning till var och en.

Kommandot nedan förutsätter att du följde de [första instruktionerna](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) tidigare i det här dokumentet och du vill uppdatera en befintlig nodpools maxantal från *3* till *5*. Använd kommandot [az aks nodepool update][az-aks-nodepool-update] för att uppdatera inställningarna för en befintlig nodpool.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Kluster automatiskskalning kan inaktiveras med [az aks nodepool uppdatering][az-aks-nodepool-update] och passerar parametern. `--disable-cluster-autoscaler`

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Om du vill återaktivera kluster automatisk skalning på ett befintligt kluster kan du återaktivera det med kommandot [az aks nodepool update][az-aks-nodepool-update] och ange parametrarna *--enable-cluster-autoscaler*, *--min-count*och *--max-count.*

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du automatiskt skalar antalet AKS-noder. Du kan också använda den vågräta podden för automatisk skalning för att automatiskt justera antalet poddar som kör ditt program. Steg om hur du använder den automatiska vågensapenna finns [i Skala program i AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
