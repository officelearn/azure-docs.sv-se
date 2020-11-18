---
title: Uppgradera ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du uppgraderar ett Azure Kubernetes service-kluster (AKS) för att få de senaste funktionerna och säkerhets uppdateringarna.
services: container-service
ms.topic: article
ms.date: 11/17/2020
ms.openlocfilehash: 262905c9f840850795ba9555912e81eca61369d1
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94683241"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Uppgradera ett AKS-kluster (Azure Kubernetes Service)

Som en del av livs cykeln för ett AKS-kluster måste du ofta uppgradera till den senaste versionen av Kubernetes. Det är viktigt att du tillämpar de senaste Kubernetes säkerhets versionerna eller uppgraderar för att få de senaste funktionerna. Den här artikeln visar hur du uppgraderar huvud komponenterna eller en enskild standardnode-pool i ett AKS-kluster.

Information om AKS-kluster som använder flera resurspooler eller Windows Server-noder finns i [uppgradera en Node-pool i AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI-version 2.0.65 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

> [!WARNING]
> En AKS kluster uppgradering utlöser en Cordon och tömmer dina noder. Om du har en låg beräknings kvot som är tillgänglig kan uppgraderingen Miss lyckas. Mer information finns i [öka kvoterna](../azure-portal/supportability/resource-manager-core-quotas-request.md) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Sök efter tillgängliga AKS-kluster uppgraderingar

Om du vill kontrol lera vilka Kubernetes-versioner som är tillgängliga för klustret använder du kommandot [AZ AKS get-uppgraderingar][az-aks-get-upgrades] . Följande exempel söker efter tillgängliga uppgraderingar till klustret med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> När du uppgraderar ett AKS-kluster som stöds kan Kubernetes minor-versioner inte hoppas över. Till exempel tillåts uppgraderingar mellan *1.12. x*  ->  *1.13. x* eller *1.13. x*  ->  *1.14. x* , men *1.12.* x  ->  *1.14. x* är inte.
>
> Uppgradera från *1.12. x*  ->  *1.14. x* genom att först uppgradera från *1.12.* x  ->  *1.13. x* och sedan uppgradera från *1.13. x*  ->  *1.14. x*.
>
> Det går bara att hoppa över flera versioner när du uppgraderar från en version som inte stöds tillbaka till en version som stöds. Du kan t. ex. uppgradera från en *1.10. x* --> en 1.15 som stöds *. x* kan slutföras.

Följande exempel på utdata visar att klustret kan uppgraderas till versioner *1.13.9* och *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Om ingen uppgradering är tillgänglig kommer du att få:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Anpassa uppgradering av nod överspänning

> [!Important]
> Nod toppar kräver en prenumerations kvot för det begärda Max antalet överspännings antal för varje uppgraderings åtgärd. Till exempel har ett kluster som har 5 noder, var och en med antalet 4 noder, totalt 20 noder. Om varje nod har ett högsta värde för överspänning på 50% krävs ytterligare beräknings-och IP-kvot för 10 noder (2 noder * 5 pooler) för att uppgraderingen ska slutföras.
>
> Om du använder Azure-CNI kontrollerar du att det finns tillgängliga IP-adresser i under nätet samt [uppfyller IP-kraven för Azure cni](configure-azure-cni.md).

Som standard konfigurerar AKS uppgraderingar till överspänning med en ytterligare nod. Ett standardvärde för en för inställningen Max spänning gör att AKS minimerar arbets belastnings störningar genom att skapa ytterligare en nod innan Cordon/dräneringen av befintliga program ersätter en äldre version av noden. Det maximala spänning svärdet kan anpassas per Node-pool för att möjliggöra en kompromiss mellan uppgraderings hastighet och uppgraderings avbrott. Genom att öka det maximala spänning svärdet slutförs uppgraderings processen snabbare, men om du anger ett stort värde för maximal överspänning kan det orsaka avbrott under uppgraderings processen. 

Till exempel ger ett högsta värde för överspänning på 100% den snabbaste möjliga uppgraderings processen (dubblera antalet noder), men gör också att alla noder i Node-poolen töms samtidigt. Du kanske vill använda ett högre värde, till exempel för test miljöer. För produktionspooler, rekommenderar vi en max_surge inställning på 33%.

AKS accepterar både heltals värden och ett procent värde för maximal överspänning. Ett heltal, till exempel "5", indikerar fem ytterligare noder till överspänning. Värdet "50%" indikerar ett överspännings värde på hälften av det aktuella antalet noder i poolen. Högsta värde för överspännings procent kan vara minst 1% och högst 100%. Ett procent värde avrundas uppåt till närmaste antal noder. Om max värdet för maximal spänning är lägre än det aktuella antalet noder vid tidpunkten för uppgraderingen, används det aktuella antalet noder för max värdet för överspänning.

Under en uppgradering kan Max värdet för överspänning vara minst 1 och ett högsta värde som motsvarar antalet noder i nodens resurspool. Du kan ange större värden, men det maximala antalet noder som används för maximal spänning är inte högre än antalet noder i poolen vid tidpunkten för uppgraderingen.

Fram till CLI version 2.16.0 + behöver du använda *AKS-Preview CLI-* tillägget för att använda maximal överspänning. Använd kommandot [AZ Extension Add][az-extension-add] och Sök efter eventuella tillgängliga uppdateringar med kommandot [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> Inställningen Max spänning i en Node-pool är permanent.  Vid efterföljande Kubernetes-uppgraderingar eller uppgraderingar av Node-versioner används den här inställningen. Du kan när som helst ändra det maximala spänning svärdet för dina nodkonfigurationer. För produktionspooler, rekommenderar vi en inställning för max-överspänning på 33%.

Använd följande kommandon för att ange max värde för toppar för nya eller befintliga nodkonfigurationer.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Uppgradera ett AKS-kluster

Med en lista över tillgängliga versioner för ditt AKS-kluster använder du kommandot [AZ AKS Upgrade][az-aks-upgrade] för att uppgradera. Under uppgraderings processen lägger AKS till en ny buffert (eller så många noder som har kon figurer ATS i [maximal spänning](#customize-node-surge-upgrade)) till klustret som kör den angivna Kubernetes-versionen. Sedan kommer den att [Cordon och tömma][kubernetes-drain] en av de gamla noderna för att minimera störningar i program som körs (om du använder maximal överspänning kommer det att [Cordon och tömma][kubernetes-drain] sig så många noder på samma gång som antalet buffertplatser som anges). När den gamla noden är helt dränerad kommer den att återavbildas för att ta emot den nya versionen och den blir noden buffert för följande nod som ska uppgraderas. Den här processen upprepas tills alla noder i klustret har uppgraderats. I slutet av processen tas den sista avtömda noden bort, vilket bibehåller det befintliga antalet noder i agenten.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Det tar några minuter att uppgradera klustret, beroende på hur många noder du har.

> [!NOTE]
> Det finns en total tillåten tid för att en kluster uppgradering ska slutföras. Den här tiden beräknas genom att ta produkten av `10 minutes * total number of nodes in the cluster` . Till exempel i ett kluster med 20 noder, måste uppgraderings åtgärder lyckas på 200 minuter eller så kan AKS inte utföra åtgärden för att undvika ett oåterkalleligt kluster tillstånd. Om du vill återställa vid uppgraderings fel, försök att uppgradera igen när tids gränsen har nåtts.

För att bekräfta att uppgraderingen har slutförts använder du kommandot [AZ AKS show][az-aks-show] :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Följande exempel på utdata visar att klustret nu kör *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Nästa steg

Den här artikeln visar hur du uppgraderar ett befintligt AKS-kluster. Mer information om hur du distribuerar och hanterar AKS-kluster finns i självstudierna.

> [!div class="nextstepaction"]
> [AKS-självstudier][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
