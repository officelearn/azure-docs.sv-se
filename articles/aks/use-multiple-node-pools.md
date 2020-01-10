---
title: Använda flera noder i Azure Kubernetes service (AKS)
description: Lär dig hur du skapar och hanterar flera Node-pooler för ett kluster i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 9c72c8431907c52dab338114ce09be139608ab0a
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768596"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Skapa och hantera flera Node-pooler för ett kluster i Azure Kubernetes service (AKS)

I Azure Kubernetes service (AKS) grupperas noderna i samma konfiguration tillsammans i *noder i pooler*. De här noderna innehåller de underliggande virtuella datorerna som kör dina program. Det ursprungliga antalet noder och deras storlek (SKU) definieras när du skapar ett AKS-kluster, vilket skapar en *standardnod*. För att stödja program som har olika beräknings-eller lagrings krav kan du skapa ytterligare noder. Använd till exempel dessa ytterligare resurspooler för att tillhandahålla GPU: er för beräknings intensiva program eller åtkomst till SSD-lagring med höga prestanda.

> [!NOTE]
> Den här funktionen ger bättre kontroll över hur du skapar och hanterar flera noder i pooler. Därför krävs separata kommandon för att skapa/uppdatera/ta bort. Tidigare kluster åtgärder via `az aks create` eller `az aks update` använt managedCluster-API: et och var det enda alternativet att ändra ditt kontroll plan och en enskild Node-pool. Den här funktionen exponerar en separat åtgärds uppsättning för agent-pooler via Agentpoolegenskap-API: et och kräver att kommandot `az aks nodepool` anges för att köra åtgärder på en enskild Node-pool.

Den här artikeln visar hur du skapar och hanterar flera resurspooler i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.76 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera Node-pooler:

* Du kan inte ta bort standard-noden (första).
* Det går inte att använda Dirigerings tillägget för HTTP-program.
* AKS-klustret måste använda standard-SKU: n för att använda flera noder, och funktionen stöds inte med Basic SKU-belastningsutjämnare.
* AKS-klustret måste använda skalnings uppsättningar för virtuella datorer för noderna.
* Namnet på en Node-pool får bara innehålla gemena alfanumeriska tecken och måste börja med en gemen bokstav. För Linux-nodkonfigurationer måste längden vara mellan 1 och 12 tecken, och längden måste vara mellan 1 och 6 tecken för Windows-noder.
* AKS-klustret kan ha högst åtta noder i pooler.
* AKS-klustret kan ha högst 800 noder i de åtta noderna i poolen.
* Alla noder i pooler måste finnas i samma VNet och undernät.
* När du skapar flera noder i klustrets skapande tid måste alla Kubernetes-versioner som används av Node-pooler matcha versions uppsättningen för kontroll planet. Detta kan uppdateras efter att klustret har etablerats med hjälp av åtgärder per nod.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Kom igång genom att skapa ett AKS-kluster med en enda Node-pool. I följande exempel används kommandot [AZ Group Create][az-group-create] för att skapa en resurs grupp med namnet *myResourceGroup* i regionen *östra* . Ett AKS-kluster med namnet *myAKSCluster* skapas sedan med kommandot [AZ AKS Create][az-aks-create] . A *--Kubernetes-versionen* av *1.13.10* används för att visa hur du uppdaterar en Node-pool i ett följande steg. Du kan ange en [Kubernetes-version som stöds][supported-versions].

> [!NOTE]
> SKU: n för *Basic* Load Balancer **stöds inte** när du använder flera noder i en pool. Som standard skapas AKS-kluster med SKU: n för *standard* belastnings utjämning från Azure CLI och Azure Portal.

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
    --kubernetes-version 1.13.10 \
    --load-balancer-sku standard
```

Det tar några minuter att skapa klustret.

> [!NOTE]
> För att säkerställa att klustret fungerar tillförlitligt bör du köra minst två (två) noder i standardnoden, eftersom viktiga system tjänster körs i den här noden.

När klustret är klart använder du kommandot [AZ AKS get-credentials][az-aks-get-credentials] för att hämta autentiseringsuppgifter för klustret för användning med `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Lägg till en Node-pool

Klustret som skapades i föregående steg har en pool med flera noder. Nu ska vi lägga till en andra Node-pool med hjälp av kommandot [AZ AKS nodepool Add][az-aks-nodepool-add] . I följande exempel skapas en Node-pool med namnet *mynodepool* som kör *3* noder:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.12.7
```

> [!NOTE]
> Namnet på en Node-pool måste börja med en gemen bokstav och får bara innehålla alfanumeriska tecken. För Linux-nodkonfigurationer måste längden vara mellan 1 och 12 tecken, och längden måste vara mellan 1 och 6 tecken för Windows-noder.

Om du vill se status för dina nodkonfigurationer använder du kommandot [AZ AKS Node pool List][az-aks-nodepool-list] och anger resurs grupp och kluster namn:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Följande exempel på utdata visar att *mynodepool* har skapats med tre noder i Node-poolen. När AKS-klustret skapades i föregående steg skapades en standard- *nodepool1* med antalet noder *2*.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.10",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Om ingen *OrchestratorVersion* eller *VmSize* anges när du lägger till en Node-pool, skapas noderna baserat på standardvärdena för AKS-klustret. I det här exemplet var Kubernetes version *1.13.10* och Node-storlek för *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Uppgradera en Node-pool
 
> [!NOTE]
> Uppgraderings-och skalnings åtgärder i ett kluster eller en nods pool kan inte inträffa samtidigt, om ett försök till ett fel returneras. I stället måste varje åtgärds typ slutföras på mål resursen innan nästa begäran om samma resurs. Läs mer om detta i vår [fel söknings guide](https://aka.ms/aks-pending-upgrade).

När ditt AKS-kluster ursprungligen skapades i det första steget angavs en `--kubernetes-version` av *1.13.10* . Detta anger Kubernetes-versionen för både kontroll planet och standardnoden. Kommandona i det här avsnittet beskriver hur du uppgraderar en enskild viss Node-pool.

Relationen mellan att uppgradera Kubernetes-versionen av kontroll planet och Node-poolen beskrivs i [avsnittet nedan](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Operativ system avbildnings versionen för Node-poolen är kopplad till Kubernetes-versionen av klustret. Du kan bara hämta uppgraderingar av operativ Systems avbildningar efter en kluster uppgradering.

Eftersom det finns två nodkonfigurationer i det här exemplet måste vi använda [AZ AKS nodepool Upgrade][az-aks-nodepool-upgrade] för att uppgradera en Node-pool. Nu ska vi uppgradera *mynodepool* till Kubernetes *1.13.10*. Använd kommandot [AZ AKS nodepool Upgrade][az-aks-nodepool-upgrade] för att uppgradera Node-poolen, som visas i följande exempel:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.10 \
    --no-wait
```

Ange status för dina nodkonfigurationer igen med kommandot [AZ AKS Node pool List][az-aks-nodepool-list] . I följande exempel visas att *mynodepool* är i *uppgraderings* läge till *1.13.10*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter att uppgradera noderna till den angivna versionen.

Som bästa praxis bör du uppgradera alla resurspooler i ett AKS-kluster till samma Kubernetes-version. Standard beteendet för `az aks upgrade` är att uppgradera alla resurspooler tillsammans med kontroll planet för att uppnå den här justeringen. Möjligheten att uppgradera enskilda noder i pooler gör att du kan utföra en löpande uppgradering och schemalägga poddar mellan noder för att upprätthålla drift tiden för programmet inom ovannämnda begränsningar.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Uppgradera ett kluster kontroll plan med flera noder i pooler

> [!NOTE]
> Kubernetes använder standard versions schema för [semantisk versions hantering](https://semver.org/) . Versions numret uttrycks som *x. y. z*, där *x* är huvud versionen, *y* är den lägre versionen och *z* är korrigerings versionen. I version *1.12.6*1 är till exempel den högre versionen, 12 är den lägre versionen och 6 är korrigerings versionen. Kubernetes-versionen av kontroll planet och den första nodens resurspool anges när klustret skapas. Alla ytterligare noder i pooler har Kubernetes-versionen när de läggs till i klustret. Kubernetes-versionerna kan variera mellan olika resurspooler samt mellan en Node-pool och kontroll planet.

Ett AKS-kluster har två kluster resurs objekt med associerade Kubernetes-versioner.

1. En kluster kontroll plan Kubernetes-version.
2. En Node-pool med en Kubernetes-version.

Ett kontroll plan mappar till en eller flera Node-pooler. Beteendet för en uppgraderings åtgärd beror på vilket Azure CLI-kommando som används.

Att uppgradera ett AKS kontroll plan kräver att du använder `az aks upgrade`. Detta uppgraderar kontroll Plans versionen och alla noder i klustret. 

Om du utfärdar `az aks upgrade` kommandot med `--control-plane-only`-flaggan uppgraderas bara kluster kontroll planet. Ingen av de associerade noderna i klustret har ändrats.

Du måste använda `az aks nodepool upgrade`för att uppgradera enskilda noder. Detta uppgraderar bara målnoden med den angivna Kubernetes-versionen

### <a name="validation-rules-for-upgrades"></a>Verifierings regler för uppgraderingar

Giltiga Kubernetes-uppgraderingar för ett klusters kontroll plan och nodkonfigurationer verifieras av följande regel uppsättningar.

* Regler för giltiga versioner för att uppgradera Node-pooler:
   * Node-versionen måste ha samma *huvud* version som kontroll planet.
   * Den *lägre* versionen av Node-poolen måste ligga inom två *lägre* versioner av kontroll Plans versionen.
   * Node-poolens version får inte vara större än kontroll `major.minor.patch`s versionen.

* Regler för att skicka en uppgraderings åtgärd:
   * Du kan inte nedgradera kontroll planet eller en Kubernetes-version för Node-poolen.
   * Om en Kubernetes version av en nod inte anges, beror beteendet på vilken klient som används. Deklaration i Resource Manager-mallar återgår till den befintliga version som definierats för Node-poolen om den används, om ingen är inställd på kontroll Plans versionen används för att gå vidare.
   * Du kan antingen uppgradera eller skala ett kontroll plan eller en Node-pool vid en viss tidpunkt, du kan inte skicka flera åtgärder på ett enda kontroll plan eller en resurs för en resurspool samtidigt.

## <a name="scale-a-node-pool-manually"></a>Skala en adresspool manuellt

När ditt programs arbets belastnings behov ändras kan du behöva skala antalet noder i en Node-pool. Antalet noder kan skalas upp eller ned.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Om du vill skala antalet noder i en Node-pool använder du kommandot [AZ AKS Node pool Scale][az-aks-nodepool-scale] . I följande exempel skalas antalet noder i *mynodepool* till *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Ange status för dina nodkonfigurationer igen med kommandot [AZ AKS Node pool List][az-aks-nodepool-list] . I följande exempel visas att *mynodepool* är i *skalnings* tillstånd med ett nytt antal *5* noder:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter för skalnings åtgärden att slutföras.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Skala en speciell Node-pool automatiskt genom att aktivera kluster autoskalning

AKS erbjuder en separat funktion för att automatiskt skala Node-pooler med en funktion som kallas för [kluster autoskalning](cluster-autoscaler.md). Den här funktionen kan aktive ras per adresspool med unika minimi-och Max skalnings antal per Node-pool. Lär dig hur du [använder kluster autoskalning per Node-pool](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Ta bort en Node-pool

Om du inte längre behöver en pool kan du ta bort den och ta bort de underliggande VM-noderna. Om du vill ta bort en Node-pool använder du kommandot [AZ AKS Node pool Delete][az-aks-nodepool-delete] och anger namnet på noden. I följande exempel tar bort *mynoodepool* som skapats i föregående steg:

> [!CAUTION]
> Det finns inga återställnings alternativ för data förlust som kan uppstå när du tar bort en Node-pool. Om poddar inte kan schemaläggas på andra nodkonfigurationer är dessa program inte tillgängliga. Se till att du inte tar bort en resurspool när det inte finns säkerhets kopior av data i användnings program eller om du inte kan köra dem på andra noder i klustret.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

I följande exempel visas utdata från kommandot [AZ AKS Node pool List][az-aks-nodepool-list] som visar att *Mynodepool* är i *borttagnings* läge:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter att ta bort noderna och Node-poolen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Ange en VM-storlek för en Node-pool

I föregående exempel för att skapa en Node-pool användes en standard storlek för virtuella datorer för de noder som skapades i klustret. Ett vanligt scenario är att du kan skapa nodkonfigurationer med olika storlekar och kapaciteter för virtuella datorer. Du kan till exempel skapa en noduppsättning som innehåller noder med stora mängder CPU eller minne, eller en Node-pool som tillhandahåller GPU-stöd. I nästa steg ska du [använda utsmaker och tolererar](#schedule-pods-using-taints-and-tolerations) för att meddela Kubernetes Scheduler hur du begränsar åtkomsten till poddar som kan köras på dessa noder.

I följande exempel skapar du en GPU-baserad Node-pool som använder *Standard_NC6* VM-storlek. De här virtuella datorerna drivs av NVIDIA Tesla K80-kortet. Information om tillgängliga VM-storlekar finns i [storlekar för virtuella Linux-datorer i Azure][vm-sizes].

Skapa en Node-pool med kommandot [AZ AKS Node pool Add][az-aks-nodepool-add] . Den här gången anger du namnet *gpunodepool*och använder parametern `--node-vm-size` för att ange *Standard_NC6* storlek:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

I följande exempel visas utdata från kommandot [AZ AKS Node pool List][az-aks-nodepool-list] som visar att *gpunodepool* *skapar* noder med den angivna *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.10",
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
    "orchestratorVersion": "1.13.10",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter innan *gpunodepool* har skapats.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Schemalägg poddar med hjälp av utsmakar och tolererar

Nu har du två noder i klustret – standardpoolen som ursprungligen skapades och den GPU-baserade Node-poolen. Använd kommandot [kubectl get Nodes][kubectl-get] för att visa noderna i klustret. Följande exempel på utdata visar noderna:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.10
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.10
```

Kubernetes Scheduler kan använda utsmakar och tolereras för att begränsa vilka arbets belastningar som kan köras på noder.

* En **bismak** tillämpas på en nod som endast anger att vissa poddar kan schemaläggas på dem.
* En **tolererande** tillämpas sedan på en pod som gör det möjligt för dem att *tolerera* en nods smak.

Mer information om hur du använder avancerade Kubernetes schemalagda funktioner finns i [metod tips för avancerade funktioner i Schemaläggaren i AKS][taints-tolerations]

I det här exemplet ska du använda en-utsmak på den GPU-baserade noden med kommandot [kubectl--noden][kubectl-taint] . Ange namnet på den GPU-baserade noden från utdata från föregående `kubectl get nodes` kommando. Bismaken används som en *nyckel: värde* och sedan ett schemaläggnings alternativ. I följande exempel används *SKU = GPU* -paret och definierar poddar i övrigt har *noschema* -funktionen:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Följande grundläggande exempel på YAML-manifest använder en tolererare för att tillåta att Schemaläggaren för Kubernetes kör en NGINX-Pod på den GPU-baserade noden. För ett mer lämpligt, men tids krävande exempel för att köra ett Tensorflow-jobb mot MNIST-datauppsättningen, se [Använd GPU: er för beräknings intensiva arbets belastningar på AKS][gpu-cluster].

Skapa en fil med namnet `gpu-toleration.yaml` och kopiera i följande exempel YAML:

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

Schemalägg Pod med kommandot `kubectl apply -f gpu-toleration.yaml`:

```console
kubectl apply -f gpu-toleration.yaml
```

Det tar några sekunder att schemalägga Pod och hämta NGINX-avbildningen. Använd kommandot [kubectl beskriver Pod][kubectl-describe] för att Visa Pod-status. I följande komprimerade exempel utdata visas *SKU = GPU: Noschema* -tolerera används. I avsnittet Events har Scheduler tilldelat Pod till *AKS-gpunodepool-28993262-vmss000000* GPU-baserad nod:

```console
$ kubectl describe pod mypod

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

Endast poddar som har den här bismaken tillämpad kan schemaläggas på noder i *gpunodepool*. Andra pod skulle schemaläggas i *nodepool1* Node-pool. Om du skapar ytterligare noder kan du använda ytterligare utsmakar och tolererar för att begränsa vilka poddar som kan schemaläggas för dessa resurs resurser.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Hantera Node-pooler med en Resource Manager-mall

När du använder en Azure Resource Manager mall för att skapa och hanterade resurser, kan du vanligt vis uppdatera inställningarna i mallen och distribuera om för att uppdatera resursen. Med Node-pooler i AKS kan den ursprungliga nodens profil för poolen inte uppdateras när AKS-klustret har skapats. Det innebär att du inte kan uppdatera en befintlig Resource Manager-mall, göra en ändring i Node-poolerna och distribuera om. I stället måste du skapa en separat Resource Manager-mall som endast uppdaterar Node-poolerna för ett befintligt AKS-kluster.

Skapa en mall som `aks-agentpools.json` och klistra in följande exempel manifest. I den här exempel mal len konfigureras följande inställningar:

* Uppdaterar *Linux* Node-poolen med namnet *myagentpool* för att köra tre noder.
* Ställer in noderna i Node-poolen att köra Kubernetes-version *1.13.10*.
* Definierar nodens storlek som *Standard_DS2_v2*.

Redigera de här värdena som behövs för att uppdatera, lägga till eller ta bort noder i pooler efter behov:

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
            "aks": "2019-04-01"
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
            "apiVersion": "2019-04-01",
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
                "orchestratorVersion": "1.13.10"
            }
        }
    ]
}
```

Distribuera den här mallen med kommandot [AZ Group Deployment Create][az-group-deployment-create] , som du ser i följande exempel. Du uppmanas att ange det befintliga AKS-kluster namnet och platsen:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

Det kan ta några minuter att uppdatera ditt AKS-kluster beroende på de inställningar och åtgärder för Node-poolen som du definierar i Resource Manager-mallen.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>Tilldela en offentlig IP-adress per nod i en Node-pool

> [!WARNING]
> Under förhands granskningen av att tilldela en offentlig IP-adress per nod kan den inte användas med *standard load BALANCER SKU i AKS* på grund av eventuella regler för belastnings utjämning i konflikt med VM-etablering. I för hands versionen måste du använda *Basic load BALANCER SKU* om du behöver tilldela en offentlig IP-adress per nod.

AKS-noder kräver inte sina egna offentliga IP-adresser för kommunikation. Vissa scenarier kan dock kräva att noder i en Node-pool har sina egna offentliga IP-adresser. Ett exempel är spel, där en konsol behöver upprätta en direkt anslutning till en virtuell dator i molnet för att minimera hopp. Detta kan uppnås genom att registrera dig för en separat förhands gransknings funktion, offentlig IP-adress (för hands version).

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

När registreringen är klar distribuerar du en Azure Resource Manager-mall enligt samma instruktioner som [ovan](#manage-node-pools-using-a-resource-manager-template) och lägger till egenskapen booleskt värde `enableNodePublicIP` till agentPoolProfiles. Ange värdet som `true` som standard anges det som `false` om inget värde anges. Detta är endast en egenskap för att skapa en tid och kräver en lägsta API-version på 2019-06-01. Detta kan användas för både Linux-och Windows-adresspooler.

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln har du skapat ett AKS-kluster som innehåller GPU-baserade noder. För att minska onödig kostnad kanske du vill ta bort *gpunodepool*eller hela AKS-klustret.

Om du vill ta bort GPU-baserad Node-pool använder du kommandot [AZ AKS nodepool Delete][az-aks-nodepool-delete] , som visas i följande exempel:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Ta bort själva klustret genom att använda kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar och hanterar flera Node-pooler i ett AKS-kluster. Mer information om hur du styr poddar över Node-pooler finns i [metod tips för avancerade funktioner i Schemaläggaren i AKS][operator-best-practices-advanced-scheduler].

Information om hur du skapar och använder Windows Server container Node-pooler finns i [skapa en Windows Server-behållare i AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
