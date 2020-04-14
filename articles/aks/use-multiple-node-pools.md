---
title: Använda flera nodpooler i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och hanterar flera nodpooler för ett kluster i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: f948c115b86abc532a121c68fa7a148ff15caae9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259093"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Skapa och hantera flera nodpooler för ett kluster i Azure Kubernetes Service (AKS)

I Azure Kubernetes Service (AKS) grupperas noder med samma konfiguration i *nodpooler*. Dessa nodpooler innehåller underliggande virtuella datorer som kör dina program. Det ursprungliga antalet noder och deras storlek (SKU) definieras när du skapar ett AKS-kluster, som skapar en [systemnodpool][use-system-pool]. Om du vill stödja program som har olika beräknings- eller lagringskrav kan du skapa ytterligare *användarnodpooler*. Systemnodpooler tjänar det primära syftet att vara värd för kritiska systemkapslar som CoreDNS och tunnelfront. Användarnodpooler tjänar det primära syftet med att vara värd för dina programpoddar. Programpoddar kan dock schemaläggas på systemnodpooler om du bara vill ha en pool i AKS-klustret. Användarnodpooler är där du placerar dina programspecifika poddar. Använd till exempel dessa ytterligare användarnodpooler för att tillhandahålla GPU:er för beräkningsintensiva program eller åtkomst till högpresterande SSD-lagring.

> [!NOTE]
> Den här funktionen ger högre kontroll över hur du skapar och hanterar flera nodpooler. Därför krävs separata kommandon för att skapa/uppdatera/ta bort. Tidigare klusteråtgärder `az aks create` `az aks update` genom eller använde managedCluster API och var det enda alternativet för att ändra ditt kontrollplan och en enda nodpool. Den här funktionen exponerar en separat åtgärdsuppsättning för agentpooler `az aks nodepool` via agentPool API och kräver användning av kommandouppsättningen för att köra åtgärder på en enskild nodpool.

Den här artikeln visar hur du skapar och hanterar flera nodpooler i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.2.0 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera nodpooler:

* Se [Kvoter, storleksbegränsningar för virtuella datorer och regiontillgänglighet i Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Du kan ta bort systemnodpooler, förutsatt att du har en annan systemnodpool som ska äga rum i AKS-klustret.
* Systempooler måste innehålla minst en nod och användarnodpooler kan innehålla noll eller fler noder.
* AKS-klustret måste använda standard SKU-belastningsutjämnaren för att använda flera nodpooler, funktionen stöds inte med grundläggande SKU-belastningsutjämnare.
* AKS-klustret måste använda skaluppsättningar för virtuella datorer för noderna.
* Namnet på en nodpool får bara innehålla gemener alfanumeriska tecken och måste börja med en gemen bokstav. För Linux-nodpooler måste längden vara mellan 1 och 12 tecken, för Windows-nodpooler måste längden vara mellan 1 och 6 tecken.
* Alla nodpooler måste finnas i samma virtuella nätverk.
* När du skapar flera nodpooler vid klusterskapande tid måste alla Kubernetes-versioner som används av nodpooler matcha versionsuppsättningen för kontrollplanet. Detta kan uppdateras när klustret har etablerats med hjälp av per nodpoolåtgärder.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

> [!Important]
> Om du kör en enda systemnodpool för AKS-klustret i en produktionsmiljö rekommenderar vi att du använder minst tre noder för nodpoolen.

För att komma igång, skapa ett AKS-kluster med en enda nodpool. I följande exempel används kommandot [az-grupp skapa][az-group-create] för att skapa en resursgrupp med namnet *myResourceGroup* i *regionen eastus.* Ett AKS-kluster med namnet *myAKSCluster* skapas sedan med kommandot [az aks create.][az-aks-create] En *--kubernetes-version* av *1.15.7* används för att visa hur du uppdaterar en nodpool i följande steg. Du kan ange valfri [Kubernetes-version som stöds][supported-versions].

> [!NOTE]
> SKU *för grundläggande* belastningsutjämnare **stöds inte** när du använder flera nodpooler. Som standard skapas AKS-kluster *Standard* med standardbelastningsutjämnaren SKU från Azure CLI- och Azure-portalen.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

Det tar några minuter att skapa klustret.

> [!NOTE]
> För att säkerställa att klustret fungerar tillförlitligt bör du köra minst 2 (två) noder i standardnodpoolen, eftersom viktiga systemtjänster körs över den här nodpoolen.

När klustret är klart använder du kommandot [az aks get-credentials][az-aks-get-credentials] för att hämta klusterautentiseringsuppgifterna för användning med: `kubectl`

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Lägga till en nodpool

Klustret som skapades i föregående steg har en enda nodpool. Nu ska vi lägga till en andra nodpool med kommandot [az aks nodepool add.][az-aks-nodepool-add] I följande exempel skapas en nodpool med namnet *mynodepool* som kör *3* noder:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> Namnet på en nodpool måste börja med en gemen och kan bara innehålla alfanumeriska tecken. För Linux-nodpooler måste längden vara mellan 1 och 12 tecken, för Windows-nodpooler måste längden vara mellan 1 och 6 tecken.

Om du vill se status för nodpoolerna använder du kommandot [az aks-nodpool och][az-aks-nodepool-list] anger resursgrupp och klusternamn:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Följande exempelutdata visar att *mynodepool* har skapats med tre noder i nodpoolen. När AKS-klustret skapades i föregående steg skapades en *standardnodpool1* med ett nodantal *på 2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Om ingen *VmSize* anges när du lägger till en nodpool är standardstorleken *Standard_DS2_v3* för Windows-nodpooler och *Standard_DS2_v2* för Linux-nodpooler. Om ingen *OrchestratorVersion* anges, är det standard samma version som kontrollplanet.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Lägga till en nodpool med ett unikt undernät (förhandsgranskning)

En arbetsbelastning kan kräva att dela upp ett klusters noder i separata pooler för logisk isolering. Den här isoleringen kan stödjas med separata undernät som är dedikerade till varje nodpool i klustret. Detta kan hantera krav som att ha icke-sammanhängande virtuellt nätverksadressutrymme för att dela mellan nodpooler.

#### <a name="limitations"></a>Begränsningar

* Alla undernät som har tilldelats nodepools måste tillhöra samma virtuella nätverk.
* Systempoddar måste ha åtkomst till alla noder i klustret för att tillhandahålla kritiska funktioner som DNS-upplösning via coreDNS.
* Tilldelning av ett unikt undernät per nodpool är begränsad till Azure CNI under förhandsversionen.
* Det går inte att använda nätverksprinciper med ett unikt undernät per nodpool under förhandsversionen.

Om du vill skapa en nodpool med ett dedikerat undernät skickar du undernätsresurs-ID:n som en ytterligare parameter när du skapar en nodpool.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Uppgradera en nodpool

> [!NOTE]
> Uppgraderings- och skalningsåtgärder på en kluster- eller nodpool kan inte utföras samtidigt om ett fel returneras. I stället måste varje åtgärdstyp slutföras på målresursen före nästa begäran på samma resurs. Läs mer om detta på vår [felsökningsguide](https://aka.ms/aks-pending-upgrade).

När AKS-klustret skapades från början `--kubernetes-version` i det första steget angavs en av *1.15.7.* Detta anger Kubernetes-versionen för både kontrollplanet och standardnodpoolen. Kommandona i det här avsnittet förklarar hur du uppgraderar en enda specifik nodpool.

Förhållandet mellan uppgradering av Kubernetes-versionen av kontrollplanet och nodpoolen förklaras i [avsnittet nedan](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> NodpoolEN OS-avbildningsversionen är knuten till Kubernetes-versionen av klustret. Du får bara uppgraderingar av OS-avbildningar efter en klusteruppgradering.

Eftersom det finns två nodpooler i det här exemplet måste vi använda [az aks nodepool-uppgradering][az-aks-nodepool-upgrade] för att uppgradera en nodpool. Låt oss uppgradera *mynodepool* till Kubernetes *1.15.7*. Använd kommandot [az aks nodepool upgrade][az-aks-nodepool-upgrade] för att uppgradera nodpoolen, som visas i följande exempel:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Ange status för nodpoolerna igen med kommandot [az aks-nodpool.][az-aks-nodepool-list] Följande exempel visar att *mynodepool* är i *uppgraderingsläge* till *1.15.7:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter att uppgradera noderna till den angivna versionen.

Du bör också uppgradera alla nodpooler i ett AKS-kluster till samma Kubernetes-version. Standardbeteendet `az aks upgrade` är att uppgradera alla nodpooler tillsammans med kontrollplanet för att uppnå den här justeringen. Möjligheten att uppgradera enskilda nodpooler kan du utföra en rullande uppgradering och schemalägga poddar mellan nodpooler för att upprätthålla programmets drifttid inom ovanstående begränsningar som nämns.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Uppgradera ett klusterkontrollplan med flera nodpooler

> [!NOTE]
> Kubernetes använder [standardversionsschemat för semantisk versionshantering.](https://semver.org/) Versionsnumret uttrycks som *x.y.z*, där *x* är huvudversionen, *y* är den delversion, och *z* är patchversionen. Till exempel, i version *1.12.6*, 1 är huvudversionen, 12 är den delversion och 6 är patchversionen. Kubernetes-versionen av kontrollplanet och den första nodpoolen anges när klustret skapas. Alla ytterligare nodpooler har sin Kubernetes-version inställd när de läggs till i klustret. Kubernetes-versionerna kan skilja sig mellan nodpooler samt mellan en nodpool och kontrollplanet.

Ett AKS-kluster har två klusterresursobjekt med Kubernetes-versioner associerade.

1. Ett klusterkontrollplan Kubernetes version.
2. En nodpool med kubernetes-version.

Ett kontrollplan mappar till en eller flera nodpooler. Hur en uppgraderingsåtgärd fungerar beror på vilket Azure CLI-kommando som används.

Uppgradering av ett AKS-kontrollplan kräver att du använder `az aks upgrade`. Det här kommandot uppgraderar kontrollplansversionen och alla nodpooler i klustret.

Om du `az aks upgrade` utfärdar `--control-plane-only` kommandot med flaggan uppgraderas endast klusterkontrollplanet. Ingen av de associerade nodpoolerna i klustret ändras.

Uppgradering av enskilda nodpooler kräver att du använder `az aks nodepool upgrade`. Det här kommandot uppgraderar endast målnodpoolen med den angivna Kubernetes-versionen

### <a name="validation-rules-for-upgrades"></a>Valideringsregler för uppgraderingar

Giltiga Kubernetes-uppgraderingar för ett klusters kontrollplan och nodpooler valideras av följande uppsättningar regler.

* Regler för giltiga versioner för att uppgradera nodpooler:
   * Nodpoolversionen måste ha samma *huvudversion* som kontrollplanet.
   * *Nodpoolens delversion* måste finnas i två *delversioner* av kontrollplansversionen.
   * Nodpoolversionen kan inte vara `major.minor.patch` större än kontrollversionen.

* Regler för att skicka en uppgraderingsåtgärd:
   * Du kan inte nedgradera kontrollplanet eller en nodpool Kubernetes-version.
   * Om en kubernetes-version av nodpool inte anges beror beteendet på vilken klient som används. Deklarationen i Resource Manager-mallar återgår till den befintliga versionen som definierats för nodpoolen om den används, om ingen är inställd på att kontrollplansversionen används för att falla tillbaka.
   * Du kan antingen uppgradera eller skala ett kontrollplan eller en nodpool vid en viss tidpunkt, du kan inte skicka flera åtgärder på ett enda kontrollplan eller nodpoolresurs samtidigt.

## <a name="scale-a-node-pool-manually"></a>Skala en nodpool manuellt

När programarbetsbelastningens krav ändras kan du behöva skala antalet noder i en nodpool. Antalet noder kan skalas upp eller ned.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Om du vill skala antalet noder i en nodpool använder du kommandot [az aks-nodpool.][az-aks-nodepool-scale] I följande exempel skalas antalet noder i *mynodepool* till *5:*

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Ange status för nodpoolerna igen med kommandot [az aks-nodpool.][az-aks-nodepool-list] I följande exempel visas att *mynodepool* är i *skalningsläge* med ett nytt antal *5* noder:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter innan skalningen slutförs.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Skala en viss nodpool automatiskt genom att aktivera kluster automatisk skalning

AKS erbjuder en separat funktion för att automatiskt skala nodpooler med en funktion som kallas [kluster automatisk skalbar](cluster-autoscaler.md). Den här funktionen kan aktiveras per nodpool med unika minsta och högsta skalningsantal per nodpool. Lär dig hur du [använder kluster automatisk skalbar per nodpool](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Ta bort en nodpool

Om du inte längre behöver en pool kan du ta bort den och ta bort de underliggande VM-noderna. Om du vill ta bort en nodpool använder du kommandot [az aks nod pool delete][az-aks-nodepool-delete] och anger nodpoolnamnet. I följande exempel tas den *mynoodepool som* skapats i föregående steg bort:

> [!CAUTION]
> Det finns inga återställningsalternativ för dataförlust som kan uppstå när du tar bort en nodpool. Om poddar inte kan schemaläggas på andra nodpooler är dessa program inte tillgängliga. Se till att du inte tar bort en nodpool när program som används inte har säkerhetskopiering av data eller möjlighet att köras på andra nodpooler i klustret.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Följande exempelutdata från kommandot [az aks-nodpool list][az-aks-nodepool-list] visar att *mynodepool* är i *läget Ta bort:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter att ta bort noderna och nodpoolen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Ange en vm-storlek för en nodpool

I föregående exempel för att skapa en nodpool användes en standardstorlek för virtuella datorer för noderna som skapats i klustret. Ett vanligare scenario är att du skapar nodpooler med olika vm-storlekar och funktioner. Du kan till exempel skapa en nodpool som innehåller noder med stora mängder CPU eller minne, eller en nodpool som ger GPU-stöd. I nästa steg använder du [taints och tolerations för](#schedule-pods-using-taints-and-tolerations) att tala om för Kubernetes scheduler hur du begränsar åtkomsten till poddar som kan köras på dessa noder.

I följande exempel skapar du en GPU-baserad nodpool som använder *den Standard_NC6* VM-storleken. Dessa virtuella datorer drivs av NVIDIA Tesla K80-kortet. Information om tillgängliga vm-storlekar finns i [Storlekar för virtuella Linux-datorer i Azure][vm-sizes].

Skapa en nodpool med kommandot [az aks nod pool add][az-aks-nodepool-add] igen. Den här gången anger du namnet *gpunodepool*och använder parametern `--node-vm-size` för att ange *Standard_NC6* storlek:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Följande exempelutdata från kommandot [az aks-nodpool visar][az-aks-nodepool-list] att *gpunodepool* *skapar* noder med den angivna *VmSize:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter för *gpunodepool* att framgångsrikt skapas.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Schemalägg poddar med taints och tolerationer

Du har nu två nodpooler i klustret - standardnodpoolen som ursprungligen skapades och den GPU-baserade nodpoolen. Använd kommandot [kubectl get noder][kubectl-get] för att visa noderna i klustret. Följande exempelutdata visar noderna:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Kubernetes-schemaläggaren kan använda taints och tolerationer för att begränsa vilka arbetsbelastningar som kan köras på noder.

* En **behäftning** tillämpas på en nod som anger att endast specifika poddar kan schemaläggas på dem.
* En **tolerans** appliceras sedan på en pod som tillåter dem att *tolerera* en nods färg.

Mer information om hur du använder avancerade kubernetes-schemalagda funktioner finns [i Metodtips för avancerade schemaläggarfunktioner i AKS][taints-tolerations]

I det här exemplet använder du en behäftning på din GPU-baserade nod med kommandot --nod-taints. Ange namnet på den GPU-baserade noden från `kubectl get nodes` utdata från föregående kommando. Fläcken används som ett *key=value* pair och sedan ett schemaläggningsalternativ. I följande exempel används *sku=gpu-paret* och definierar poddar som annars har *noschedule-möjligheten:*

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

I följande grundläggande exempel använder YAML-manifestet en tolerans för att låta Kubernetes-schemaläggaren köra en NGINX-pod på den GPU-baserade noden. Ett lämpligare, men tidskrävande exempel för att köra ett Tensorflow-jobb mot MNIST-datauppsättningen, finns i [Använda GPU:er för beräkningsintensiva arbetsbelastningar på AKS][gpu-cluster].

Skapa en `gpu-toleration.yaml` fil med namnet och kopiera i följande exempel YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Schemalägg podden `kubectl apply -f gpu-toleration.yaml` med kommandot:

```console
kubectl apply -f gpu-toleration.yaml
```

Det tar några sekunder att schemalägga pod och dra NGINX bilden. Använd kommandot [kubectl describe pod][kubectl-describe] för att visa pod-status. Följande kondenserade exempelutdata visar *sku=gpu:NoSchedule-toleransen* tillämpas. I avsnittet händelser har schemaläggaren tilldelat podden till *aks-gpunodepool-28993262-vmss00000* GPU-baserad nod:

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Endast poddar som har denna tolerans tillämpas kan schemaläggas på noder i *gpunodepool*. Alla andra pod skulle schemaläggas i *nodepool1* nodpoolen. Om du skapar ytterligare nodpooler kan du använda ytterligare fläckar och tolerationer för att begränsa vilka poddar som kan schemaläggas på dessa nodresurser.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Ange en befläcka, etikett eller tagg för en nodpool

När du skapar en nodpool kan du lägga till fläckar, etiketter eller taggar i nodpoolen. När du lägger till en befläcka, etikett eller tagg får alla noder i nodpoolen också den där färg-, etikett- eller taggen.

Om du vill skapa en nodpool med en besudlad använder du [az aks nodepool add][az-aks-nodepool-add]. Ange namnet *taintnp* och `--node-taints` använd parametern för att ange *sku=gpu:NoSchedule* för fläcken.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Följande exempelutdata från kommandot [az aks nodepool list][az-aks-nodepool-list] visar att *taintnp* *skapar* noder med de angivna *noderna:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

Den besudlade informationen visas i Kubernetes för hantering av schemaläggningsregler för noder.

Du kan också lägga till etiketter i en nodpool när nodpoolen skapas. Etiketter som anges vid nodpoolen läggs till i varje nod i nodpoolen. Dessa [etiketter visas i Kubernetes][kubernetes-labels] för hantering av schemaläggningsregler för noder.

Om du vill skapa en nodpool med en etikett använder du [az aks nodepool add][az-aks-nodepool-add]. Ange namnet *labelnp* och `--labels` använd parametern för att ange *dept=IT* och *costcenter=9999* för etiketter.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Etiketten kan bara ställas in för nodpooler när nodpoolerna skapas. Etiketter måste också vara ett nyckel-/värdepar och ha en [giltig syntax][kubernetes-label-syntax].

Följande exempelutdata från kommandot [az aks nodepool list][az-aks-nodepool-list] visar att *labelnp* *skapar* noder med angiven *nodLabels:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

Du kan använda en Azure-tagg på nodpooler i AKS-klustret. Taggar som tillämpas på en nodpool tillämpas på varje nod i nodpoolen och sparas genom uppgraderingar. Taggar tillämpas också på nya noder som läggs till i en nodpool under skalningsåtgärder. Om du lägger till en tagg kan du hjälpa till med uppgifter som principspårning eller kostnadsuppskattning.

Skapa en nodpool med [az aks-nodpoolen][az-aks-nodepool-add]add . Ange namnet *tagnodepool* och `--tag` använd parametern för att ange *dept=IT* och *costcenter=9999* för taggar.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Du kan också `--tags` använda parametern när du använder az [aks nodepool update-kommando][az-aks-nodepool-update] samt när klustret skapas. När klustret `--tags` skapas tillämpar parametern taggen på den första nodpoolen som skapats med klustret. Alla taggnamn måste följa begränsningarna i [Använd-taggar för att organisera dina Azure-resurser][tag-limitation]. Om du uppdaterar en `--tags` nodpool med parametern uppdateras alla befintliga taggvärden och eventuella nya taggar läggs till. Om din nodpool till exempel hade *dept=IT* och *costcenter=9999* för taggar och du uppdaterade den med *team=dev* och *costcenter=111* för taggar, skulle du nodepool ha *dept=IT*, *costcenter=111*och *team=dev* för taggar.

Följande exempelutdata från kommandot [az aks nodepool list][az-aks-nodepool-list] visar att *tagnodepool* *skapar* noder med den angivna *taggen:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Hantera nodpooler med hjälp av en Resource Manager-mall

När du använder en Azure Resource Manager-mall för att skapa och hanterade resurser kan du vanligtvis uppdatera inställningarna i mallen och distribuera om för att uppdatera resursen. Med nodpooler i AKS kan den första nodpoolprofilen inte uppdateras när AKS-klustret har skapats. Det här problemet innebär att du inte kan uppdatera en befintlig Resource Manager-mall, göra en ändring i nodpoolerna och distribuera om. I stället måste du skapa en separat Resource Manager-mall som uppdaterar endast nodpoolerna för ett befintligt AKS-kluster.

Skapa en mall `aks-agentpools.json` som och klistra in följande exempelmanifest. I den här exempelmallen konfigureras följande inställningar:

* Uppdaterar *Linux-nodpoolen* *myagentpool* för att köra tre noder.
* Anger att noderna i nodpoolen ska köra Kubernetes version *1.15.7*.
* Definierar nodstorleken *som Standard_DS2_v2*.

Redigera dessa värden efter behov av att uppdatera, lägga till eller ta bort nodpooler efter behov:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Distribuera den här mallen med kommandot [az group deployment create,][az-group-deployment-create] som visas i följande exempel. Du uppmanas att ange det befintliga AKS-klustrets namn och plats:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Du kan lägga till en tagg i nodpoolen genom att lägga till *taggegenskapen* i mallen, som visas i följande exempel.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Det kan ta några minuter att uppdatera AKS-klustret beroende på vilka inställningar och åtgärder som du definierar i Resource Manager-mallen.

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>Tilldela en offentlig IP per nod för en nodpool (förhandsgranskning)

> [!WARNING]
> Under förhandsversionen av tilldelningen av en offentlig IP per nod kan den inte användas med *standardbelastningsutjämnar-SKU:n i AKS* på grund av möjliga belastningsutjämna regler som står i konflikt med vm-etablering. Som ett resultat av den här begränsningen stöds inte Windows-agentpooler med den här förhandsgranskningsfunktionen. I förhandsgranskningen måste du använda *SKU för grundläggande belastningsutjämning* om du behöver tilldela en offentlig IP per nod.

AKS-noder kräver inte sina egna offentliga IP-adresser för kommunikation. Scenarier kan dock kräva att noder i en nodpool tar emot sina egna offentliga IP-adresser. Ett vanligt scenario är för spelarbetsbelastningar, där en konsol måste upprätta en direkt anslutning till en virtuell dator i molnet för att minimera hopp. Det här scenariot kan uppnås på AKS genom att registrera dig för en förhandsgranskningsfunktion, Node Public IP (preview).

Registrera dig för noden Public IP-funktionen genom att utfärda följande Azure CLI-kommando.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

När du har registrerat dig har du distribuerat [above](#manage-node-pools-using-a-resource-manager-template) en Azure Resource `enableNodePublicIP` Manager-mall enligt samma instruktioner som ovan och lägga till den booleska egenskapen i agentPoolProfiles. Ange värdet `true` till som standard är `false` det inställt som om det inte angavs. 

Den här egenskapen är en egenskap för endast skapa tid och kräver en minsta API-version av 2019-06-01. Detta kan tillämpas på både Linux- och Windows-nodpooler.

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln har du skapat ett AKS-kluster som innehåller GPU-baserade noder. Om du vill minska onödiga kostnader kanske du vill ta bort *gpunodepoolen*eller hela AKS-klustret.

Om du vill ta bort den GPU-baserade nodpoolen använder du kommandot [az aks nodepool delete][az-aks-nodepool-delete] som visas i följande exempel:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Om du vill ta bort själva klustret använder du kommandot [az group delete][az-group-delete] för att ta bort AKS-resursgruppen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [systemnodpooler][use-system-pool].

I den här artikeln lärde du dig hur du skapar och hanterar flera nodpooler i ett AKS-kluster. Mer information om hur du styr poddar över nodpooler finns [i Metodtips för avancerade schemaläggarfunktioner i AKS][operator-best-practices-advanced-scheduler].

Mer om hur du skapar och använder windows server-behållarnodpooler finns [i Skapa en Windows Server-behållare i AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-system-pool]: use-system-pools.md
