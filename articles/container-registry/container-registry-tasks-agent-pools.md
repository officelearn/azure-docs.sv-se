---
title: Använd dedikerad pool för att köra uppgifts uppgifter
description: Konfigurera en dedikerad Compute-pool (lagringspool) i registret för att köra en Azure Container Registry aktivitet.
ms.topic: article
ms.date: 04/30/2020
ms.openlocfilehash: 9eac8d620dd6ce268692494499ade39173fc9b75
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864661"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Köra en ACR-uppgift i en dedikerad agent

Konfigurera en Azure-hanterad VM-pool (*agent*) så att du kan köra [Azure Container Registry uppgifter][acr-tasks] i en dedikerad beräknings miljö. När du har konfigurerat en eller flera pooler i registret kan du välja en pool för att köra en aktivitet i stället för tjänstens standard beräknings miljö.

En agent-pool tillhandahåller:

- **Stöd för virtuella nätverk** – tilldela en modempool till ett Azure VNet, vilket ger åtkomst till resurser i det virtuella nätverket, till exempel ett behållar register, nyckel valv eller lagrings utrymme.
- **Skala efter behov** – öka antalet instanser i en lagringspool för beräknings intensiva uppgifter eller skala till noll. Faktureringen baseras på allokering.
- **Flexibla alternativ** – Välj mellan olika [Poolnivå](#pool-tiers) -och skalnings alternativ för att möta dina arbets belastnings behov.
- **Azure-hantering** – aktivitetssekvenser korrigeras och underhålls av Azure, vilket ger reserverad allokering utan att behöva underhålla de enskilda virtuella datorerna.

Den här funktionen är tillgänglig i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry SKU: er][acr-tiers].

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version och vissa [begränsningar gäller](#preview-limitations). Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).
>

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

- Task agent-pooler stöder för närvarande Linux-noder. Windows-noder stöds inte för närvarande.
- Task agent-pooler är tillgängliga i för hands versionen i följande regioner: USA, västra 2, södra centrala USA, östra USA 2 och östra USA.
- För varje register är standard kvoten för vCPU (Core) för alla agent pooler 16. Öppna en [support förfrågan][open-support-ticket] om ytterligare allokering.

## <a name="prerequisites"></a>Krav

* För att kunna använda Azure CLI-stegen i den här artikeln krävs Azure CLI version 2.3.1 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli]. Eller kör i [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Om du inte redan har ett behållar register [skapar du ett][create-reg-cli] (Premium-nivå krävs) i en för hands versions region.

## <a name="pool-tiers"></a>Nivåer för pooler

Nivåer för agenter tillhandahåller följande resurser per instans i poolen.

|Nivå  |Processor  |Minne (GB)  |
|---------|---------|---------|
|S1     |  2       |  3       |
|S2     |  4       |   8      |
|S3     |    8     |   16      |

## <a name="create-and-manage-a-task-agent-pool"></a>Skapa och hantera en aktivitetssekvens

### <a name="set-default-registry-optional"></a>Ange standard register (valfritt)

För att förenkla Azure CLI-kommandon som följer ställer du in standard registret genom att köra kommandot [AZ Configure][az-configure] :

```azurecli
az configure --defaults acr=<registryName>
```

I följande exempel förutsätter vi att du har angett standard registret. Om inte, skickar du `--registry <registryName>` en parameter i `az acr` varje kommando.

### <a name="create-agent-pool"></a>Skapa Agent-pool

Skapa en agent-pool med kommandot [AZ ACR agentpoolegenskap Create][az-acr-agentpool-create] . I följande exempel skapas en nivå i S2-poolen (4 processorer/instans). Poolen innehåller som standard 1 instans.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

Det tar flera minuter att slutföra skapandet av en agent och andra hanterings åtgärder för pooler.

### <a name="scale-pool"></a>Skala pool

Skala Poolens storlek uppåt eller nedåt med kommandot [AZ ACR agentpoolegenskap Update][az-acr-agentpool-update] . I följande exempel skalas poolen till två instanser. Du kan skala till 0 instanser.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Skapa pool i ett virtuellt nätverk

### <a name="add-firewall-rules"></a>Lägg till brand Väggs regler

Task agent-pooler kräver åtkomst till följande Azure-tjänster. Följande brand Väggs regler måste läggas till i alla befintliga nätverks säkerhets grupper eller användardefinierade vägar.

| Riktning | Protokoll | Källa         | Källport | Mål          | Mål Port | Används    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| Utgående  | TCP      | VirtualNetwork | Alla         | Storage              | 443       | Standardvärde |
| Utgående  | TCP      | VirtualNetwork | Alla         | EventHub             | 443       | Standardvärde |
| Utgående  | TCP      | VirtualNetwork | Alla         | AzureActiveDirectory | 443       | Standardvärde |
| Utgående  | TCP      | VirtualNetwork | Alla         | AzureMonitor         | 443       | Standardvärde |

> [!NOTE]
> Om aktiviteterna kräver ytterligare resurser från det offentliga Internet lägger du till motsvarande regler. De här reglerna behövs till exempel om du kör en Docker-build-uppgift som behöver hämta bas avbildningarna från Docker Hub eller återställer ett NuGet-paket.

### <a name="create-pool-in-vnet"></a>Skapa pool i VNet

I följande exempel skapas en modempool *i under nätet undernät för* nätverks- *myvnet*:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Kör uppgift i agenten

I följande exempel visas hur du anger en agents-pool när en aktivitet köas.

> [!NOTE]
> Om du vill använda en modempool i en ACR-aktivitet kontrollerar du att poolen innehåller minst en instans.
>

### <a name="quick-task-run"></a>Snabb uppgifts körning

Köa en snabb körning på agenten med hjälp av kommandot [AZ ACR build][az-acr-build] och skicka `--agent-pool` parametern:

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

### <a name="automatically-triggered-task"></a>Automatiskt utlöst uppgift

Du kan till exempel skapa en schemalagd aktivitet i lagringspoolen med [AZ ACR Task Create][az-acr-task-create], som skickar `--agent-pool` parametern.

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --commit-trigger-enabled false
```

Verifiera uppgifts installationen genom att köra [AZ ACR Task Run][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Status för programpoolstillstånd

Du hittar antalet körningar som är schemalagda för agenten genom att köra [AZ ACR agentpoolegenskap show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Nästa steg

Fler exempel på behållar avbildnings avbildningar och underhåll i molnet finns i [själv studie serien med ACR tasks](container-registry-tutorial-quick-task.md).



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
