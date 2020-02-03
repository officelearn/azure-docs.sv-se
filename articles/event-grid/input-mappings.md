---
title: Mappa anpassat fält till Azure Event Grid-schema
description: I den här artikeln beskrivs hur du konverterar det anpassade schemat till Azure Event Grid-schemat när dina händelse data inte matchar Event Grid schema.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721667"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mappa eget fält till Event Grid-schema

Om dina händelse data inte matchar det förväntade [Event Grid schemat](event-schema.md)kan du fortfarande använda Event Grid för att dirigera händelser till prenumeranter. Den här artikeln beskriver hur du mappar ditt schema i Event Grid-schemat.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Installera förhandsversionsfunktionen

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Ursprungliga Händelseschema

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

Även om formatet inte matchar det begärda schemat, kan Event Grid du mappa din fält till schemat. Eller du kan få värdena i den ursprungliga scheman.

## <a name="create-custom-topic-with-mapped-fields"></a>Skapa anpassat ämne med mappade fält

När du skapar ett anpassat ämne, ange mappning från din ursprungliga händelse i event grid-schemat. Det finns tre värden som du använder för att anpassa mappningen:

* Värdet för **indatavärdet** anger typ av schema. De tillgängliga alternativen är CloudEvents-schema, schemat för anpassad händelse eller Event Grid-schemat. Standardvärdet är Event Grid-schemat. När du skapar anpassade mappningen mellan ditt schema och event grid-schemat kan du använda anpassad Händelseschema. Använd Cloudevents-schema när händelser är i CloudEvents-schema.

* Egenskapen **Mappning av standardvärden** anger standardvärden för fält i Event Grid schemat. Du kan ange standardvärden för `subject`, `eventtype`och `dataversion`. Normalt använder du den här parametern när din anpassade schemat inte innehåller ett fält som överensstämmer med någon av dessa tre fält. Du kan till exempel ange att data versionen alltid är inställd på **1,0**.

* Värdet Maps **Fields** mappar från schemat till Event Grid-schemat. Du anger värden i blankstegsavgränsad nyckel/värde-par. Använd namnet på event grid fältet Namn på nyckeln. Använd namnet på fältet som värde. Du kan använda nyckel namn för `id`, `topic`, `eventtime`, `subject`, `eventtype`och `dataversion`.

Om du vill skapa ett anpassat ämne med Azure CLI, använder du:

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

## <a name="subscribe-to-event-grid-topic"></a>Prenumerera på event grid-ämne

Prenumerera på anpassat ämne, ange det schema som du vill använda för att ta emot händelser. Du anger den CloudEvents-schema, ett schema för anpassad händelse eller en Event Grid-schemat. Standardvärdet är Event Grid-schemat.

I följande exempel prenumererar på ett event grid-ämne och använder Event Grid-schemat. Om du använder Azure CLI använder du:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

I nästa exempel används inmatningsschemat för händelsen:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

I följande exempel prenumererar på ett event grid-ämne och använder Event Grid-schemat. Om du använder PowerShell använder du:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

I nästa exempel används inmatningsschemat för händelsen:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publicera händelsen till ämnet

Du är nu redo att skicka en händelse till det anpassade ämnet och se resultatet av mappningen. Följande skript för att publicera en händelse i [exempel schemat](#original-event-schema):

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

Nu kan du titta på WebHook-slutpunkt. De två prenumerationerna levereras händelser i olika scheman.

Den första prenumerationen används event grid-schemat. Formatet på levererade händelsen är:

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

Dessa fält som innehåller mappningar från det anpassade ämnet. **myEventTypeField** mappas till **EventType**. Standardvärdena för **DataVersion** och **subject** används. **Data** -objektet innehåller de ursprungliga händelse schema fälten.

Den andra prenumerationen används inmatningshändelse schemat. Formatet på levererade händelsen är:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Observera att de ursprungliga fält har levererats.

## <a name="next-steps"></a>Nästa steg

* Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
