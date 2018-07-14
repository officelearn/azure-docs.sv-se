---
title: Hantera leveransinställningar för Azure Event Grid-prenumerationer
description: Beskriver hur du anpassar händelse Leveransalternativ för Event Grid.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: tomfitz
ms.openlocfilehash: e91ee640d18e2cf804be33fd130bf48737c9efb1
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035677"
---
# <a name="manage-event-grid-delivery-settings"></a>Hantera Event Grid leveransinställningar

När du skapar en händelseprenumeration kan anpassa du inställningarna för händelseleverans. Du kan ange hur länge Event Grid försöker att leverera meddelandet. Du kan ange en storage-konto som ska användas för att lagra händelser som inte kan levereras till en slutpunkt.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Ange återförsöksprincipen

När du skapar en Event Grid-prenumeration kan ange du värden för hur länge Event Grid bör försöka leverera händelsen. Event Grid som standard försöker i 24 timmar (1 440 minuter) och försöker upp till 30 gånger. Du kan ange något av dessa värden för event grid-prenumeration.

För att ange händelsen time-to-live för ett annat värde än 1 440 minuter, använder du:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Om du vill ange maximalt antal synkroniseringsförsök till ett annat värde än 30, använder du:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Om du anger både `event-ttl` och `max-deliver-attempts`, Event Grid använder först att upphöra för att gälla för omförsök.

## <a name="set-dead-letter-location"></a>Ange platsen för obeställbara meddelanden

När Event Grid inte kan skicka en händelse, kan den skicka händelsen inte har levererats till ett lagringskonto. Den här processen kallas dead-lettering. Som standard Aktivera inte Event Grid dead-lettering. Du måste ange ett lagringskonto för att lagra felande händelser när du skapar händelseprenumerationen för att aktivera den. Du hämtar händelser från det här lagringskontot för att lösa leveranser.

Event Grid skickar en händelse till förlorade platsen om det har försökt alla dess återförsök, eller om den får ett felmeddelande som anger leverans aldrig lyckas. Till exempel om Event Grid tar emot ett felaktigt format-fel när du ska leverera en händelse, skickar den omedelbart händelsen till förlorade plats.

Du måste ha ett lagringskonto med en behållare för innan du anger systemkön plats. Du kan ange slutpunkten för den här behållaren när du skapar händelseprenumerationen. Slutpunkten är i formatet: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Följande skript hämtar resurs-ID för ett befintligt lagringskonto och skapas en händelseprenumeration som använder en behållare i det lagringskontot för förlorade-slutpunkten.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Du använder Event Grid för att svara på händelser som inte har [skapa en händelseprenumeration](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) för förlorade blob-lagringen. Varje gång förlorade blobblagringen tar emot en felande händelse Event Grid meddelar din hanterare. Hanteraren svarar med åtgärder som du vill dra för att stämma av händelser som inte har levererats. 

Om du vill inaktivera dead-lettering, kör kommandot för att skapa händelseprenumerationen men inte anger ett värde för `deadletter-endpoint`. Du behöver inte ta bort händelseprenumerationen.

## <a name="next-steps"></a>Nästa steg

* Information om händelseleverans och återförsök, [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
