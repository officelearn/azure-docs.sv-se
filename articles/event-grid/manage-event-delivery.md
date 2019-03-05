---
title: Obeställbara meddelanden och principer för återförsök för Azure Event Grid-prenumerationer
description: Beskriver hur du anpassar händelse Leveransalternativ för Event Grid. Ange ett mål för förlorade och ange hur lång tid att försöka igen leverans.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 69c26ab522a925032c5a255d07489de0052756c0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340865"
---
# <a name="dead-letter-and-retry-policies"></a>Obeställbara meddelanden och principer för återförsök

När du skapar en händelseprenumeration kan anpassa du inställningarna för händelseleverans. Den här artikeln visar hur du konfigurerar en plats för obeställbara meddelanden och anpassa återförsöksinställningar för. Information om dessa funktioner finns i [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).

## <a name="set-dead-letter-location"></a>Ange platsen för obeställbara meddelanden

Om du vill ange en plats för obeställbara meddelanden, behöver du ett storage-konto för att lagra händelser som inte kan levereras till en slutpunkt. Exemplen hämta resurs-ID för ett befintligt lagringskonto. De skapa en händelseprenumeration som använder en behållare i det lagringskontot för förlorade-slutpunkten.

> [!NOTE]
> Skapa ett lagringskonto och en blob-behållare i storage innan du kör kommandona i den här artikeln.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Om du vill inaktivera dead-lettering, kör kommandot för att skapa händelseprenumerationen men inte anger ett värde för `deadletter-endpoint`. Du behöver inte ta bort händelseprenumerationen.

> [!NOTE]
> Om du använder Azure CLI på din lokala dator ska du använda Azure CLI version 2.0.56 eller större. Anvisningar om hur du installerar den senaste versionen av Azure CLI finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Om du vill inaktivera dead-lettering, kör kommandot för att skapa händelseprenumerationen men inte anger ett värde för `DeadLetterEndpoint`. Du behöver inte ta bort händelseprenumerationen.

> [!NOTE]
> Om du använder Azure PowerShell på den lokala datorn kan använda Azure PowerShell-version 1.1.0 eller större. Ladda ned och installera den senaste Azure PowerShell från [Azure hämtar](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Ange återförsöksprincipen

När du skapar en Event Grid-prenumeration kan ange du värden för hur länge Event Grid bör försöka leverera händelsen. Som standard försöker Event Grid i 24 timmar (1 440 minuter) eller 30 gånger. Du kan ange något av dessa värden för event grid-prenumeration. Värdet för time to live-händelse måste vara ett heltal mellan 1 och 1440. Värdet för max återförsök måste vara ett heltal mellan 1 och 30.

Du kan inte konfigurera den [försök schema](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Azure CLI

För att ange händelsen time-to-live för ett annat värde än 1 440 minuter, använder du:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

För att ange de maximala återförsök för ett annat värde än 30, använder du:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Om du anger både `event-ttl` och `max-deliver-attempts`, Event Grid använder först att upphöra för att gälla för att avgöra när du ska stoppa händelseleverans.

### <a name="powershell"></a>PowerShell

För att ange händelsen time-to-live för ett annat värde än 1 440 minuter, använder du:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

För att ange de maximala återförsök för ett annat värde än 30, använder du:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Om du anger både `EventTtl` och `MaxDeliveryAttempt`, Event Grid använder först att upphöra för att gälla för att avgöra när du ska stoppa händelseleverans.

## <a name="next-steps"></a>Nästa steg

* Ett exempelprogram som använder en Azure Function-app för att bearbeta händelser som obeställbara meddelanden, se [Azure Event Grid obeställbara-exempel för .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Information om händelseleverans och återförsök, [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
