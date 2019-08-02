---
title: Använda flera noder i Azure Kubernetes service (AKS)
description: Lär dig hur du skapar och hanterar flera Node-pooler för ett kluster i Azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/17/2019
ms.author: mlearned
ms.openlocfilehash: 72f34d9711e1ba4658288bfdeb847632d32d0fcf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68478332"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>För hands version – skapa och hantera flera resurspooler för ett kluster i Azure Kubernetes service (AKS)

I Azure Kubernetes service (AKS) grupperas noderna i samma konfiguration tillsammans i *noder i pooler*. De här noderna innehåller de underliggande virtuella datorerna som kör dina program. Det ursprungliga antalet noder och deras storlek (SKU) definieras när du skapar ett AKS-kluster, vilket skapar en *standardnod*. För att stödja program som har olika beräknings-eller lagrings krav kan du skapa ytterligare noder. Använd till exempel dessa ytterligare resurspooler för att tillhandahålla GPU: er för beräknings intensiva program eller åtkomst till SSD-lagring med höga prestanda.

Den här artikeln visar hur du skapar och hanterar flera resurspooler i ett AKS-kluster. Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS för hands versions funktionerna är självbetjänings-och deltagande. De erbjuds att samla in feedback och buggar från vår community. I för hands versionen är dessa funktioner inte avsedda att användas för produktion. Funktioner i offentlig för hands version har stöd för bästa prestanda. Hjälp från AKS Technical Support Teams är endast tillgängligt under kontors tid Pacific-timezone (PST). Mer information finns i följande support artiklar:
>
> * [Support principer för AKS][aks-support-policies]
> * [Vanliga frågor och svar om support för Azure][aks-faq]

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.61 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera AKS-Preview CLI-tillägg

Om du vill använda flera nodepools behöver du *AKS-Preview CLI-* tillägg version 0.4.1 eller högre. Installera *AKS-Preview* Azure CLI-tillägget med kommandot [AZ Extension Add][az-extension-add] och Sök sedan efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>Registrera flera funktions leverantörer för Node pool

Om du vill skapa ett AKS-kluster som kan använda flera noder i pooler aktiverar du först två funktions flaggor i din prenumeration. Kluster med flera noder använder en skalnings uppsättning för virtuella datorer (VMSS) för att hantera distributionen och konfigurationen av Kubernetes-noderna. Registrera funktions flaggorna *MultiAgentpoolPreview* och *VMSSPreview* med hjälp av kommandot [AZ Feature register][az-feature-register] , som visas i följande exempel:

> [!CAUTION]
> När du registrerar en funktion på en prenumeration kan du för närvarande inte avregistrera funktionen. När du har aktiverat vissa för hands versions funktioner kan standarderna användas för alla AKS-kluster och sedan skapas i prenumerationen. Aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat prenumeration för att testa för hands versions funktionerna och samla in feedback.

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Alla AKS-kluster som du skapar när du har registrerat *MultiAgentpoolPreview* använder du den här för hands versionen av klustret. För att fortsätta att skapa vanliga kluster som stöds fullständigt, aktivera inte för hands versions funktioner för produktions prenumerationer. Använd en separat test-eller utvecklings-Azure-prenumeration för att testa för hands versions funktioner.

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrol lera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören *Microsoft. container service* med hjälp av [AZ Provider register][az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera Node-pooler:

* Flera noder i pooler är bara tillgängliga för kluster som skapats efter att du har registrerat *MultiAgentpoolPreview* -och *VMSSPreview* -funktionerna för din prenumeration. Du kan inte lägga till eller hantera resurspooler med ett befintligt AKS-kluster som skapats innan de här funktionerna har registrerats.
* Du kan inte ta bort den första noden.
* Det går inte att använda Dirigerings tillägget för HTTP-program.
* Du kan inte lägga till/uppdatera/ta bort resurspooler med en befintlig Resource Manager-mall som i de flesta åtgärder. Använd i stället [en separat Resource Manager-mall](#manage-node-pools-using-a-resource-manager-template) för att göra ändringar i nodkonfigurationer i ett AKS-kluster.

När den här funktionen är i för hands version gäller följande ytterligare begränsningar:

* AKS-klustret kan ha högst åtta noder i pooler.
* AKS-klustret kan ha högst 400 noder i de åtta noderna i poolen.
* Alla noder i pooler måste finnas i samma undernät

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Kom igång genom att skapa ett AKS-kluster med en enda Node-pool. I följande exempel används kommandot [AZ Group Create][az-group-create] för att skapa en resurs grupp med namnet *myResourceGroup* i regionen *östra* . Ett AKS-kluster med namnet *myAKSCluster* skapas sedan med kommandot [AZ AKS Create][az-aks-create] . A *--Kubernetes-versionen* av *1.13.5* används för att visa hur du uppdaterar en Node-pool i ett följande steg. Du kan ange en [Kubernetes-version som stöds][supported-versions].

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.5
```

Det tar några minuter att skapa klustret.

När klustret är klart använder du kommandot [AZ AKS get-credentials][az-aks-get-credentials] för att hämta autentiseringsuppgifter för klustret för användning med `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Lägg till en Node-pool

Klustret som skapades i föregående steg har en pool med flera noder. Nu ska vi lägga till en andra Node-pool med kommandot [AZ AKS Node pool Add][az-aks-nodepool-add] . I följande exempel skapas en Node-pool med namnet *mynodepool* som kör *3* noder:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Om du vill se status för dina nodkonfigurationer använder du kommandot [AZ AKS Node pool List][az-aks-nodepool-list] och anger resurs grupp och kluster namn:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

Följande exempel på utdata visar att *mynodepool* har skapats med tre noder i Node-poolen. När AKS-klustret skapades i föregående steg skapades en standard- *nodepool1* med antalet noder *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

> [!TIP]
> Om ingen *OrchestratorVersion* eller *VmSize* anges när du lägger till en Node-pool, skapas noderna baserat på standardvärdena för AKS-klustret. I det här exemplet var Kubernetes version *1.13.5* och Node-storlek för *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Uppgradera en Node-pool

När ditt AKS-kluster skapades i det första steget angavs `--kubernetes-version` en av *1.13.5* . Detta anger Kubernetes-versionen för både kontroll planet och den första noden. Det finns olika kommandon för att uppgradera Kubernetes-versionen av kontroll planet och Node-poolen. Kommandot används för att uppgradera kontroll planet, `az aks nodepool upgrade` medan används för att uppgradera en enskild Node-pool. `az aks upgrade`

Nu ska vi uppgradera *mynodepool* till Kubernetes *1.13.7*. Använd kommandot [Uppgradera AZ AKS Node pool][az-aks-nodepool-upgrade] för att uppgradera Node-poolen, som visas i följande exempel:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.7 \
    --no-wait
```

> [!Tip]
> Om du vill uppgradera kontroll planet till *1.13.7*kör `az aks upgrade -k 1.13.7`du.

Ange status för dina nodkonfigurationer igen med kommandot [AZ AKS Node pool List][az-aks-nodepool-list] . I följande exempel visas att *mynodepool* är i *uppgraderings* läge till *1.13.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.13.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Det tar några minuter att uppgradera noderna till den angivna versionen.

Som bästa praxis bör du uppgradera alla resurspooler i ett AKS-kluster till samma Kubernetes-version. Möjligheten att uppgradera enskilda noder i pooler gör att du kan utföra en löpande uppgradering och schemalägga poddar mellan noder för att upprätthålla drift tiden för programmet.

> [!NOTE]
> Kubernetes använder standard versions schema för [semantisk versions hantering](https://semver.org/) . Versions numret uttrycks som *x. y. z*, där *x* är huvud versionen, *y* är den lägre versionen och *z* är korrigerings versionen. I version *1.12.6*1 är till exempel den högre versionen, 12 är den lägre versionen och 6 är korrigerings versionen. Kubernetes-versionen av kontroll planet samt den första nodens resurspool anges när klustret skapas. Alla ytterligare noder i pooler har Kubernetes-versionen när de läggs till i klustret. Kubernetes-versionerna kan variera mellan olika resurspooler samt mellan en Node-pool och kontroll planet, men följande begränsningar gäller:
> 
> * Node-versionen måste ha samma huvud version som kontroll planet.
> * Node-versionen kan vara en lägre version som är mindre än kontroll Plans versionen.
> * En version av Node-poolen kan vara en korrigerings version så länge de andra två begränsningarna följs.
> 
> Om du vill uppgradera Kubernetes-versionen av kontroll planet använder `az aks upgrade`du. Om klustret bara har en adresspool, kommer `az aks upgrade` kommandot också att uppgradera Kubernetes-versionen för Node-poolen.

## <a name="scale-a-node-pool"></a>Skala en Node-pool

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
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.13.7                 100             Linux     Scaling              myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Det tar några minuter för skalnings åtgärden att slutföras.

## <a name="delete-a-node-pool"></a>Ta bort en Node-pool

Om du inte längre behöver en pool kan du ta bort den och ta bort de underliggande VM-noderna. Om du vill ta bort en Node-pool använder du kommandot [AZ AKS Node pool Delete][az-aks-nodepool-delete] och anger namnet på noden. I följande exempel tar bort *mynoodepool* som skapats i föregående steg:

> [!CAUTION]
> Det finns inga återställnings alternativ för data förlust som kan uppstå när du tar bort en Node-pool. Om poddar inte kan schemaläggas på andra nodkonfigurationer är dessa program inte tillgängliga. Se till att du inte tar bort en resurspool när det inte finns säkerhets kopior av data i användnings program eller om du inte kan köra dem på andra noder i klustret.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

I följande exempel visas utdata från kommandot [AZ AKS Node pool List][az-aks-nodepool-list] som visar att *Mynodepool* är i *borttagnings* läge:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.13.7                 100             Linux     Deleting             myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Det tar några minuter att ta bort noderna och Node-poolen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Ange en VM-storlek för en Node-pool

I föregående exempel för att skapa en Node-pool användes en standard storlek för virtuella datorer för de noder som skapades i klustret. Ett vanligt scenario är att du kan skapa nodkonfigurationer med olika storlekar och kapaciteter för virtuella datorer. Du kan till exempel skapa en noduppsättning som innehåller noder med stora mängder CPU eller minne, eller en Node-pool som tillhandahåller GPU-stöd. I nästa steg ska du [använda utsmaker och tolererar](#schedule-pods-using-taints-and-tolerations) för att meddela Kubernetes Scheduler hur du begränsar åtkomsten till poddar som kan köras på dessa noder.

I följande exempel skapar du en GPU-baserad Node-pool som använder den virtuella dator storleken *Standard_NC6* . De här virtuella datorerna drivs av NVIDIA Tesla K80-kortet. Information om tillgängliga VM-storlekar finns i [storlekar för virtuella Linux-datorer i Azure][vm-sizes].

Skapa en Node-pool med kommandot [AZ AKS Node pool Add][az-aks-nodepool-add] . Den här gången anger du namnet *gpunodepool*och använder `--node-vm-size` parametern för att ange *Standard_NC6* storlek:

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
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.13.5                 100             Linux     Creating             myResourceGroup  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.13.5                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Det tar några minuter innan *gpunodepool* har skapats.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Schemalägg poddar med hjälp av utsmakar och tolererar

Nu har du två noder i klustret – standardpoolen som ursprungligen skapades och den GPU-baserade Node-poolen. Använd kommandot [kubectl get Nodes][kubectl-get] för att visa noderna i klustret. Följande exempel på utdata visar en nod i varje Node-pool:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.5
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.5
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

Skapa en fil med `gpu-toleration.yaml` namnet och kopiera i följande exempel yaml:

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

Schemalägg Pod med `kubectl apply -f gpu-toleration.yaml` kommandot:

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

När du använder en Azure Resource Manager mall för att skapa och hanterade resurser, kan du vanligt vis uppdatera inställningarna i mallen och distribuera om för att uppdatera resursen. Med Node-pooler i AKS kan den ursprungliga nodens profil för poolen inte uppdateras när AKS-klustret har skapats. Det innebär att du inte kan uppdatera en befintlig Resource Manager-mall, göra en ändring i Node-poolerna och distribuera om. I stället måste du skapa en separat Resource Manager-mall som endast uppdaterar agent poolerna för ett befintligt AKS-kluster.

Skapa en mall som `aks-agentpools.json` och klistra in följande exempel manifest. I den här exempel mal len konfigureras följande inställningar:

* Uppdaterar *Linux* -agenttjänsten med namnet *myagentpool* för att köra tre noder.
* Ställer in noderna i Node-poolen att köra Kubernetes-version *1.13.5*.
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
            "orchestratorVersion": "1.13.5"
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
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
