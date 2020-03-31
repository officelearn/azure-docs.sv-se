---
title: Mappa anpassat fält till Azure Event Grid-schema
description: I den här artikeln beskrivs hur du konverterar ditt anpassade schema till Azure Event Grid-schemat när händelsedata inte matchar Event Grid-schemat.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721667"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mappa anpassade fält till ett Event Grid-schema

Om dina händelsedata inte matchar det förväntade [eventrutnätet-schemat](event-schema.md)kan du fortfarande använda Event Grid för att dirigera händelsen till prenumeranter. I den här artikeln beskrivs hur du mappar schemat till event grid-schemat.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Installera förhandsversionsfunktionen

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Ursprungligt händelseschema

Anta att du har ett program som skickar händelser i följande format:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Även om det formatet inte matchar det schema som krävs kan du mappa fälten till schemat i Event Grid. Du kan också ta emot värdena i det ursprungliga schemat.

## <a name="create-custom-topic-with-mapped-fields"></a>Skapa anpassat ämne med mappade fält

När du skapar ett anpassat ämne anger du hur du mappar fält från den ursprungliga händelsen till händelserutnätets schema. Det finns tre värden som du använder för att anpassa mappningen:

* **Indataschemavärdet** anger typen av schema. De tillgängliga alternativen är CloudEvents-schema, anpassat händelseschema eller Event Grid-schema. Standardvärdet är schema för Event Grid. När du skapar anpassad mappning mellan schemat och schemat för händelserutnätet använder du anpassat händelseschema. När händelser finns i CloudEvents-schemat använder du Cloudevents-schemat.

* Egenskapen **Mapping default values** anger standardvärden för fält i schemat Event Grid. Du kan ange `subject`standardvärden för , `eventtype`och `dataversion`. Vanligtvis använder du den här parametern när det anpassade schemat inte innehåller ett fält som motsvarar ett av dessa tre fält. Du kan till exempel ange att dataversionen alltid är inställd på **1.0**.

* Värdet **för mappningsfälten** mappar fält från schemat till händelserutnätets schema. Du anger värden i utrymmesavgränsade nyckel-/värdepar. Använd namnet på fältet för händelserutnätet för nyckelnamnet. Använd namnet på fältet för värdet. Du kan använda `id`nyckelnamn för `eventtype`, `dataversion` `topic`, `eventtime`, `subject`, och .

Om du vill skapa ett anpassat ämne med Azure CLI använder du:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Prenumerera på händelserutnätsämne

När du prenumererar på det anpassade avsnittet anger du det schema som du vill använda för att ta emot händelserna. Du anger CloudEvents-schemat, det anpassade händelseschemat eller eventrutnätet- schemat. Standardvärdet är schema för Event Grid.

I följande exempel prenumererar du på ett händelserutnätsämne och använder schemat För händelserutnät. Om du använder Azure CLI använder du:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

I nästa exempel används indataschemat för händelsen:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

I följande exempel prenumererar du på ett händelserutnätsämne och använder schemat För händelserutnät. Om du använder PowerShell använder du:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

I nästa exempel används indataschemat för händelsen:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publicera händelse till ämne

Du är nu redo att skicka en händelse till det anpassade ämnet och se resultatet av mappningen. Följande skript för att publicera en händelse i [exempelschemat:](#original-event-schema)

Om du använder Azure CLI använder du:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Titta nu på webhook-slutpunkten. De två prenumerationerna levererade händelser i olika scheman.

Den första prenumerationen använde händelserutnätsschema. Formatet på den levererade händelsen är:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Dessa fält innehåller mappningar från det anpassade avsnittet. **myEventTypeField** mappas till **EventType**. Standardvärdena för **DataVersion** och **Ämne** används. **Data-objektet** innehåller de ursprungliga händelseschemafälten.

Den andra prenumerationen använde indatahändelseschemat. Formatet på den levererade händelsen är:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Observera att de ursprungliga fälten levererades.

## <a name="next-steps"></a>Nästa steg

* För information om händelseleverans och återförsök, [Leverans av eventrutnätsmeddelanden och återförsök](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
