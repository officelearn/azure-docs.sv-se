---
title: Obeställbara meddelanden och principer för återförsök för Azure Event Grid-prenumerationer
description: Beskriver hur du anpassar händelse Leveransalternativ för Event Grid. Ange ett mål för förlorade och ange hur lång tid att försöka igen leverans.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077790"
---
# <a name="dead-letter-and-retry-policies"></a>Obeställbara meddelanden och principer för återförsök

När du skapar en händelseprenumeration kan anpassa du inställningarna för händelseleverans. Den här artikeln visar hur du konfigurerar en plats för obeställbara meddelanden och anpassa återförsöksinställningar för. Information om dessa funktioner finns i [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Ange platsen för obeställbara meddelanden

Om du vill ange en plats för obeställbara meddelanden, behöver du ett storage-konto för att lagra händelser som inte kan levereras till en slutpunkt. Följande skript hämtar resurs-ID för ett befintligt lagringskonto och skapas en händelseprenumeration som använder en behållare i det lagringskontot för förlorade-slutpunkten.

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

Om du vill inaktivera dead-lettering, kör kommandot för att skapa händelseprenumerationen men inte anger ett värde för `deadletter-endpoint`. Du behöver inte ta bort händelseprenumerationen.

## <a name="set-retry-policy"></a>Ange återförsöksprincipen

När du skapar en Event Grid-prenumeration kan ange du värden för hur länge Event Grid bör försöka leverera händelsen. Event Grid som standard försöker i 24 timmar (1 440 minuter) och försöker upp till 30 gånger. Du kan ange något av dessa värden för event grid-prenumeration. Värdet för time to live-händelse måste vara ett heltal mellan 1 och 1440. Värdet för maximal leveransförsök måste vara ett heltal mellan 1 och 30.

Du kan inte konfigurera den [försök schema](delivery-and-retry.md#retry-schedule-and-duration).

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

## <a name="next-steps"></a>Nästa steg

* Ett exempelprogram som använder en Azure Function-app för att bearbeta händelser som obeställbara meddelanden, se [Azure Event Grid obeställbara-exempel för .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Information om händelseleverans och återförsök, [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
