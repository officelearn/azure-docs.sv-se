---
title: Hantera leveransinställningar för händelsen rutnät för Azure-prenumerationer
description: Beskriver hur du anpassar händelse Leveransalternativ för händelsen rutnät.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 65e79f492e96c418502e096b60992ba992868dd7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036504"
---
# <a name="manage-event-grid-delivery-settings"></a>Hantera inställningar för leverans av händelse rutnätet

När du skapar en händelseprenumeration kan anpassa du inställningarna för leverans av händelsen. Du kan ange hur länge händelse rutnätet försök att leverera meddelandet. Du kan ange ett lagringskonto som ska användas för att lagra händelser som inte levereras till en slutpunkt.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Ange återförsöksprincip

När du skapar en händelse rutnätet prenumeration kan ange du värden för hur länge händelse rutnätet ska försöka leverera händelsen. Händelsen rutnät som standard försöker under 24 timmar (1440 minuter) och försöker högst 30 gånger. Du kan ange något av värdena för prenumerationen händelse rutnätet.

Om du vill konfigurera händelsen time to live till ett annat värde än 1 440 minuter, använder du:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --event-ttl 720
```

Om du vill ange maximalt antal synkroniseringsförsök till ett annat värde än 30, använder du:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --max-delivery-attempts 18
```

Om du anger både `event-ttl` och `max-deliver-attempts`, händelse rutnätet använder först för att gälla för återförsök försöker.

## <a name="set-dead-letter-location"></a>Ange förlorade plats

När händelsen rutnätet inte kan skicka en händelse måste skicka den händelsen inte har levererats till ett lagringskonto. Den här processen kallas dead-lettering. Som standard Aktivera inte händelse rutnätet dead-lettering. Du måste ange ett lagringskonto för att lagra felande händelser när du skapar händelseprenumerationen för att aktivera den. Du hämtar händelser från det här lagringskontot för att lösa leveranser.

Händelsen rutnätet skickar en händelse till platsen för obeställbara om alla dess nya försök har gjorts eller om den får ett felmeddelande som anger leverans aldrig lyckas. Till exempel om händelsen rutnätet tar emot ett felaktigt format-fel när du levererar en händelse, skickar omedelbart den händelsen till platsen förlorade.

Innan du anger platsen för obeställbara måste du ha ett lagringskonto med en behållare. Anger du slutpunkten för den här behållaren när du skapar händelseprenumerationen. Slutpunkten är i formatet: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Följande skript hämtar resurs-ID för ett befintligt lagringskonto och skapar en händelseprenumeration som använder en behållare i detta lagringskonto för obeställbara slutpunkten.

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
  --endpoint <endpoint_URL>
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Att använda händelsen rutnätet för att svara på händelser som inte har [skapa en händelseprenumerationen](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) för obeställbara blobblagring. Varje gång förlorade blobblagring har tagit emot en felande händelse, meddelar händelse rutnätet din hanterare. Hanteraren svarar med åtgärder som du vill dra för att stämma av felande händelser. 

## <a name="next-steps"></a>Nästa steg

* Information om händelsen leverans och omförsök, [händelse rutnätet meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
