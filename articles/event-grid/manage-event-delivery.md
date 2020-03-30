---
title: Principer för obeställbara brev och försök igen – Azure Event Grid
description: Beskriver hur du anpassar alternativ för händelseleverans för Event Grid. Ange ett mål med obeställbara bokstäver och ange hur länge leveransen ska återinträdes.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: spelluru
ms.openlocfilehash: caed3c077b4df5da5fd8541b2f7e85ef119604b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794040"
---
# <a name="dead-letter-and-retry-policies"></a>Principer för obeställbara bokstäver och återförsök

När du skapar en händelseprenumeration kan du anpassa inställningarna för händelseleverans. I den här artikeln visas hur du konfigurerar en plats med obeställbara bokstäver och anpassa inställningarna för återförsök. Information om dessa funktioner finns i [Leverans av meddelande från Event Grid och försök igen](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Ange plats för obeställbara brev

Om du vill ange en plats för obeställbara brev behöver du ett lagringskonto för att lagra händelser som inte kan levereras till en slutpunkt. Exemplen får resurs-ID för ett befintligt lagringskonto. De skapar en händelseprenumeration som använder en behållare i det lagringskontot för slutpunkten för obeställbara brev.

> [!NOTE]
> - Skapa ett lagringskonto och en blob-behållare i lagringen innan du kör kommandon i den här artikeln.
> - Tjänsten Event Grid skapar blobbar i den här behållaren. Namnen på blobbar har namnet på Event Grid-prenumerationen med alla bokstäver i versaler. Till exempel kan Om namnet på prenumerationen är My-Blob-Prenumeration, kommer namnen på bloberna med död bokstav att ha MY-BLOB-PRENUMERATION (myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/11111111-1111-1111-1111-1111-111111.json). Det här beteendet är att skydda mot skillnader i ärendehantering mellan Azure-tjänster.


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

Om du vill inaktivera obeställbara bokstäver kör du kommandot igen för att `deadletter-endpoint`skapa händelseprenumerationen men ger inget värde för . Du behöver inte ta bort händelseprenumerationen.

> [!NOTE]
> Om du använder Azure CLI på din lokala dator ska du använda Azure CLI version 2.0.56 eller större. Anvisningar om hur du installerar den senaste versionen av Azure CLI finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Om du vill inaktivera obeställbara bokstäver kör du kommandot igen för att `DeadLetterEndpoint`skapa händelseprenumerationen men ger inget värde för . Du behöver inte ta bort händelseprenumerationen.

> [!NOTE]
> Om du använder Azure Poweshell på din lokala dator använder du Azure PowerShell version 1.1.0 eller senare. Ladda ned och installera de senaste Azure PowerShell från [Azure-nedladdningar](https://azure.microsoft.com/downloads/).

## <a name="set-retry-policy"></a>Ange princip för återförsök

När du skapar en Event Grid-prenumeration kan du ange värden för hur länge Event Grid ska försöka leverera händelsen. Som standard försöker Event Grid i 24 timmar (1440 minuter) eller 30 gånger. Du kan ange något av dessa värden för din prenumeration på händelserutnät. Värdet för händelsetid till live måste vara ett heltal från 1 till 1440. Värdet för max återförsök måste vara ett heltal från 1 till 30.

Du kan inte konfigurera [schemat för återförsök](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Azure CLI

Om du vill ange händelsetid för att leva till ett annat värde än 1440 minuter använder du:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Om du vill ställa in maxförsöken på ett annat värde än 30 använder du:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Om du `event-ttl` anger `max-deliver-attempts`båda och används det första händelseutnätet för att avgöra när händelseleverans ska stoppas.

### <a name="powershell"></a>PowerShell

Om du vill ange händelsetid för att leva till ett annat värde än 1440 minuter använder du:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Om du vill ställa in maxförsöken på ett annat värde än 30 använder du:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Om du `EventTtl` anger `MaxDeliveryAttempt`båda och används det första händelseutnätet för att avgöra när händelseleverans ska stoppas.

## <a name="next-steps"></a>Nästa steg

* Ett exempelprogram som använder en Azure Function-app för att bearbeta händelser med obeställbara bokstäver finns i [Azure Event Grid Dead Letter Samples för .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* För information om händelseleverans och återförsök, [Leverans av eventrutnätsmeddelanden och återförsök](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
