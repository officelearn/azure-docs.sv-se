---
title: Använda flera nodpooler i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och hanterar flera nodpooler för ett kluster i Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/29/2019
ms.author: iainfou
ms.openlocfilehash: 1c24bbb9433e4164d4b2f6ce1ac7bd726cc36356
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506909"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Förhandsversion – skapa och hantera flera nodpooler för ett kluster i Azure Kubernetes Service (AKS)

I Azure Kubernetes Service (AKS) noder i samma konfiguration grupperas tillsammans i *nodpooler*. Dessa nodpooler innehålla de underliggande virtuella datorer som kör dina program. Det inledande antalet noder och deras storlek (SKU) definieras när du skapar ett AKS-kluster, vilket skapar en *standard nodpool*. Du kan skapa ytterligare nodpooler för att stödja program som har olika beräkning eller lagringsbehov. Exempelvis kan du använda dessa ytterligare nodpooler för att tillhandahålla GPU: er för beräkningsintensiva program eller få åtkomst till SSD-lagring med höga prestanda.

Den här artikeln visar hur du skapar och hanterar flera nodpooler i ett AKS-kluster. Den här funktionen är för närvarande en förhandsversion.

> [!IMPORTANT]
> AKS-förhandsversionsfunktioner är självbetjäning och delta i. Förhandsversioner tillhandahålls för att samla in feedback och buggar från vår community. De stöds dock inte av teknisk support för Azure. Om du skapar ett kluster eller lägga till dessa funktioner i befintliga kluster, stöds klustret inte förrän funktionen är inte längre i förhandsversion och uppgraderas till allmän tillgänglighet (GA).
>
> Om du stöter på problem med funktioner i förhandsversion [öppna ett ärende på AKS GitHub-lagringsplatsen] [ aks-github] med namnet på funktionen för förhandsgranskning i rubriken för bugg.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI version 2.0.61 eller senare installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installera CLI-tillägg för aks-förhandsversion

CLI-kommandon för att skapa och hantera flera nodpooler är tillgängliga i den *aks-förhandsversion* CLI-tillägg. Installera den *förhandsversionen av aks* Azure CLI tillägget med hjälp av den [az-tillägget lägger du till] [ az-extension-add] kommandot, som visas i följande exempel:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Om du tidigare har installerat den *förhandsversionen av aks* tillägg, installera alla tillgängliga uppdateringar med hjälp av den `az extension update --name aks-preview` kommando.

### <a name="register-multiple-node-pool-feature-provider"></a>Registrera flera noden pool funktionen-providern

Om du vill skapa ett AKS-kluster som kan använda flera nodpooler först aktivera två flaggor för funktionen på din prenumeration. Flera noder poolen kluster använder en VM-skalningsuppsättning (VMSS) för att hantera distributionen och konfigurationen av Kubernetes-noderna. Registrera den *MultiAgentpoolPreview* och *VMSSPreview* funktionen flaggar med hjälp av den [az funktionen registrera] [ az-feature-register] kommandot enligt den följande exempel:

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> Alla AKS-kluster som du skapar när du har registrerat den *MultiAgentpoolPreview* använda förhandsupplevelsen för klustret. Om du vill fortsätta att skapa kluster av regelbundna, fullt stöd inte aktivera förhandsversionsfunktioner för produktion-prenumerationer. Använd en separat test- eller Azure-prenumeration för att testa förhandsversionsfunktioner.

Det tar några minuter för statusen att visa *registrerad*. Du kan kontrollera statusen registrering med den [az funktionslistan] [ az-feature-list] kommando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

När du är klar kan du uppdatera registreringen av den *Microsoft.ContainerService* resursprovidern med hjälp av den [az provider register] [ az-provider-register] kommando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som har stöd för flera nodpooler:

* Flera nodpooler är bara tillgängliga för kluster som skapas när du har registrerat den *MultiAgentpoolPreview* och *VMSSPreview* funktioner för din prenumeration. Du kan inte lägga till eller hantera nodpooler med ett AKS-kluster som skapats innan dessa funktioner som har registrerats.
* Du kan inte ta bort den första nod-adresspoolen.
* Tillägg till routning för HTTP-program kan inte användas.

Den här funktionen är i förhandsversion, begränsningar gäller följande ytterligare:

* AKS-klustret kan ha högst åtta nodpooler.
* AKS-klustret kan ha högst 400 noder poolerna åtta noden.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

Kom igång genom att skapa ett AKS-kluster med en enda nod-pool. I följande exempel används den [az gruppen skapa] [ az-group-create] kommando för att skapa en resursgrupp med namnet *myResourceGroup* i den *eastus* region. Ett AKS-kluster med namnet *myAKSCluster* skapas sedan med hjälp av den [az aks skapa] [ az-aks-create] kommando. En *--kubernetes-version* av *1.12.6* används för att visa hur du uppdaterar en nodpool i följande steg. Du kan ange någon [Kubernetes-version som stöds][supported-versions].

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
    --kubernetes-version 1.12.6
```

Det tar några minuter att skapa klustret.

När klustret är klart, använda den [aaz aks get-credentials] [ az-aks-get-credentials] kommando för att hämta autentiseringsuppgifter för kluster för användning med `kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Lägga till en nodpool

Klustret skapas i föregående steg har en enda nod-pool. Nu ska vi lägga till en andra nod poolen med hjälp av den [az aks nodpool lägga till] [ az-aks-nodepool-add] kommando. I följande exempel skapas en nodpool med namnet *mynodepool* som kör *3* noder:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

Om du vill se status för din nodpooler, använda den [az aks pool nodlistan] [ az-aks-nodepool-list] kommandot och ange resursnamnet grupp och klustret:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster -o table
```

Följande Exempelutdata visar att *mynodepool* har skapats med tre noder i poolen för noden. När AKS-klustret har skapats i föregående steg, en standard *nodepool1* skapades med en nod-uppräkning av *1*.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

> [!TIP]
> Om ingen *OrchestratorVersion* eller *VmSize* anges när du lägger till en nodpool noderna skapas baserat på standardinställningarna för AKS-klustret. I det här exemplet var som Kubernetes-version *1.12.6* och nodstorlek av *Standard_DS2_v2*.

## <a name="upgrade-a-node-pool"></a>Uppgradera en nodpool

När AKS-klustret har skapats i det första steget en `--kubernetes-version` av *1.12.6* har angetts. Nu ska vi uppgraderar den *mynodepool* till Kubernetes *1.12.7*. Använd den [az aks noden pool uppgraderingen] [ az-aks-nodepool-upgrade] kommando för att uppgradera nodpool som du ser i följande exempel:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.12.7 \
    --no-wait
```

Lista statusen för din nodpooler igen med den [az aks pool nodlistan] [ az-aks-nodepool-list] kommando. I följande exempel visas som *mynodepool* finns i den *uppgradera* till *1.12.7*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup    VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  ---------------  ---------------
VirtualMachineScaleSets  3        110        mynodepool  1.12.7                 100             Linux     Upgrading            myResourceGroup  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroup  Standard_DS2_v2
```

Det tar några minuter att uppgradera noder till den angivna versionen.

Som bästa praxis bör du uppgradera alla nodpooler i ett AKS-kluster till samma Kubernetes-version. Möjlighet att uppgradera enskilda nodpooler kan du utföra en löpande uppgradering och schemalägga poddar mellan nodpooler att upprätthålla programmets drifttid.

## <a name="scale-a-node-pool"></a>Skala en nodpool

Som programmet arbetsbelastning uppgiftsbehoven ändras kan du behöva skala antalet noder i poolen noden. Antalet noder som kan skalas upp eller ned.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Om du vill skala antalet noder i nodpoolen använder den [az aks noden pool skala] [ az-aks-nodepool-scale] kommando. I följande exempel skalas antalet noder i *mynodepool* till *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Lista statusen för din nodpooler igen med den [az aks pool nodlistan] [ az-aks-nodepool-list] kommando. I följande exempel visas som *mynodepool* finns i den *skalning* tillstånd med det nya antalet *5* noder:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Scaling              myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Det tar några minuter att slutföra åtgärden.

## <a name="delete-a-node-pool"></a>Ta bort en nod-adresspool

Om du inte längre behöver en pool kan du ta bort den och ta bort de underliggande Virtuella datornoder. Ta bort en nodpool genom att använda den [az aks nodpool ta bort] [ az-aks-nodepool-delete] kommandot och ange namnet på noden pool. I följande exempel tar bort den *mynoodepool* skapades i föregående steg:

> [!CAUTION]
> Det finns inga återställningsalternativ för förlust av data som kan uppstå när du tar bort en nodpool. Om poddar inte kan schemaläggas till andra nodpooler, är dessa program inte tillgängliga. Kontrollera att du inte tar bort en nodpool när program och används inte har säkerhetskopior eller möjligheten att köra på andra nodpooler i klustret.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Följande Exempelutdata från den [az aks pool nodlistan] [ az-aks-nodepool-list] kommandot visar att *mynodepool* finns i den *tas bort* tillstånd:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name        OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  ----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  5        110        mynodepool  1.12.7                 100             Linux     Deleting             myResourceGroupPools  Standard_DS2_v2
VirtualMachineScaleSets  1        110        nodepool1   1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Det tar några minuter att ta bort noder och nodpoolen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Ange en VM-storlek för en nodpool

I föregående exempel att skapa en nodpool användes standardstorleken för virtuella datorer för de noder som skapats i klustret. Ett allt vanligare scenario är att skapa nodpooler med olika storlekar på Virtuella datorer och funktioner. Du kan till exempel skapa en nodpool som innehåller noder med stora mängder CPU eller minne eller en nodpool som har stöd för GPU. I nästa steg ska du [Använd taints och tolerations][#schedule-pods-using-taints-and-tolerations] hur Kubernetes scheduler att begränsa åtkomsten till poddar som kan köras på noderna.

I följande exempel skapar en GPU-baserad nodpool som använder den *Standard_NC6* VM-storlek. Dessa virtuella datorer drivs av NVIDIA Tesla K80-kort. Information om tillgängliga storlekar finns i [storlekar för Linux-datorer i Azure][vm-sizes].

Skapa en pool med den [az aks nodpool lägga till] [ az-aks-nodepool-add] -kommandot på nytt. Den här gången anger du namnet *gpunodepool*, och använda den `--node-vm-size` parametern för att ange den *Standard_NC6* storlek:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Följande Exempelutdata från den [az aks pool nodlistan] [ az-aks-nodepool-list] kommandot visar att *gpunodepool* är *skapa* noder med den angivna *VmSize*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster -o table

AgentPoolType            Count    MaxPods    Name         OrchestratorVersion    OsDiskSizeGb    OsType    ProvisioningState    ResourceGroup         VmSize
-----------------------  -------  ---------  -----------  ---------------------  --------------  --------  -------------------  --------------------  ---------------
VirtualMachineScaleSets  1        110        gpunodepool  1.12.6                 100             Linux     Creating             myResourceGroupPools  Standard_NC6
VirtualMachineScaleSets  1        110        nodepool1    1.12.6                 100             Linux     Succeeded            myResourceGroupPools  Standard_DS2_v2
```

Det tar några minuter för den *gpunodepool* skapas har.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Schemalägga poddar med taints och tolerations

Nu har du två nodpooler i ditt kluster, standardpoolen för noden som ursprungligen skapades och GPU-baserad nod-adresspoolen. Använd den [kubectl få noder] [ kubectl-get] kommando för att visa noder i klustret. Följande Exempelutdata visar en nod i varje nodpool:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.12.6
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.12.6
```

Kubernetes-Schemaläggaren kan använda taints och tolerations för att begränsa vilka arbetsbelastningar kan köras på noder.

* En **förorena** tillämpas på en nod som anger att endast specifika poddar kan schemaläggas på dem.
* En **toleration** sedan appliceras till en pod som låter dem *tolerera* färg för en nod.

Mer information om hur du använder avancerade Kubernetes schemalagda funktioner finns i [bästa praxis för avancerade scheduler funktioner i AKS][taints-tolerations]

I det här exemplet kan du tillämpa en färg till GPU-baserad noden med den [kubectl färg noden] [ kubectl-taint] kommando. Ange namnet på noden GPU-baserad från utdata från föregående `kubectl get nodes` kommando. Färg som används som en *key: value* och sedan ett alternativ för schemaläggning. I följande exempel används den *sku = gpu* parkopplas och definierar poddar Annars måste den *NoSchedule* möjlighet:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

Följande grundläggande exempel YAML manifestet använder en toleration för att tillåta Kubernetes-Schemaläggaren för att köra en NGINX-pod på GPU-baserad-nod. Ett mer effektivt, men tidskrävande exempel att köra ett Tensorflow-jobb mot MNIST datauppsättningen finns i [Använd GPU: er för beräkningsintensiva arbetsbelastningar på AKS][gpu-cluster].

Skapa en fil med namnet `gpu-toleration.yaml` och kopiera följande YAML:

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

Schemalägga en pod med hjälp av den `kubectl apply -f gpu-toleration.yaml` kommando:

```console
kubectl apply -f gpu-toleration.yaml
```

Det tar några sekunder att schemalägga en pod och hämta NGINX-avbildningen. Använd den [kubectl beskriver pod] [ kubectl-describe] kommando för att visa status för pod. Följande komprimerade exempel utdata visar den *sku = gpu:NoSchedule* toleration tillämpas. I avsnittet händelser scheduler har tilldelats din pod på *aks-gpunodepool-28993262-vmss000000* GPU-baserad nod:

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

Poddar som har den här färg som används kan schemaläggas på noder i *gpunodepool*. Andra pod skulle schemaläggas i den *nodepool1* nodpool. Om du skapar ytterligare nodpooler, du kan använda ytterligare taints och tolerations att begränsa vilka poddar kan schemaläggas på resurserna noden.

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln skapade du ett AKS-kluster med GPU-baserad noder. För att minska onödiga kostnader, kanske du vill ta bort den *gpunodepool*, eller hela AKS-klustret.

Ta bort poolen GPU-baserad noden genom att använda den [az aks nodpool ta bort] [ az-aks-nodepool-delete] kommandot enligt följande exempel:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Ta bort själva klustret genom att använda den [az group delete] [ az-group-delete] kommando för att ta bort resursgruppen AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du skapar och hanterar flera nodpooler i ett AKS-kluster. Läs mer om hur du styr poddar i nodpooler [bästa praxis för avancerade scheduler funktioner i AKS][operator-best-practices-advanced-scheduler].

<!-- EXTERNAL LINKS -->
[aks-github]: https://github.com/azure/aks/issues]
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
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
