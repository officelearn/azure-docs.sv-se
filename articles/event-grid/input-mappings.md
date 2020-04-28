---
title: Mappa ett anpassat fält till Azure Event Grid schema
description: I den här artikeln beskrivs hur du konverterar det anpassade schemat till Azure Event Grid-schemat när dina händelse data inte matchar Event Grid schema.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e8077068a265d659cf6009eb7762188637c373d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721667"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mappa anpassade fält till ett Event Grid-schema

Om dina händelse data inte matchar det förväntade [Event Grid schemat](event-schema.md)kan du fortfarande använda Event Grid för att dirigera händelser till prenumeranter. I den här artikeln beskrivs hur du mappar schemat till Event Grid schema.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Installera förhandsversionsfunktionen

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Ursprungligt händelse schema

Vi antar att du har ett program som skickar händelser i följande format:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Även om formatet inte matchar det schema som krävs, kan Event Grid mappa dina fält till schemat. Du kan också ta emot värdena i det ursprungliga schemat.

## <a name="create-custom-topic-with-mapped-fields"></a>Skapa ett anpassat ämne med mappade fält

När du skapar ett anpassat ämne anger du hur du mappar fält från din ursprungliga händelse till Event Grid-schemat. Det finns tre värden som du kan använda för att anpassa mappningen:

* Värdet för **indatavärdet** anger typ av schema. De tillgängliga alternativen är CloudEvents-schema, anpassat händelse schema eller Event Grid schema. Standardvärdet är Event Grid schema. När du skapar en anpassad mappning mellan schemat och event Grid-schemat använder du anpassade händelse scheman. Använd Cloudevents-schemat när händelser finns i CloudEvents-schemat.

* Egenskapen **Mappning av standardvärden** anger standardvärden för fält i Event Grid schemat. Du kan ange standardvärden `subject`för `eventtype`, och `dataversion`. Normalt använder du den här parametern när det anpassade schemat inte innehåller ett fält som motsvarar något av dessa tre fält. Du kan till exempel ange att data versionen alltid är inställd på **1,0**.

* Värdet Maps **Fields** mappar från schemat till Event Grid-schemat. Du anger värden i blank steg-separerade nyckel/värde-par. Använd namnet på händelse rutnäts fältet för nyckel namnet. För värdet använder du namnet på ditt fält. Du kan använda nyckel namn för `id`, `topic`, `eventtime` `subject` `eventtype`,, och `dataversion`.

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

## <a name="subscribe-to-event-grid-topic"></a>Avsnittet Prenumerera på Event Grid

När du prenumererar på det anpassade ämnet anger du det schema som du vill använda för att ta emot händelserna. Du anger CloudEvents-schema, anpassat händelse schema eller Event Grid schema. Standardvärdet är Event Grid schema.

Följande exempel prenumererar på ett event Grid-ämne och använder Event Grid-schemat. Om du använder Azure CLI använder du:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

I nästa exempel används ingångs schema för händelsen:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Följande exempel prenumererar på ett event Grid-ämne och använder Event Grid-schemat. Om du använder PowerShell använder du:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

I nästa exempel används ingångs schema för händelsen:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publicera händelse till ämne

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

Titta nu på webhook-slutpunkten. De två prenumerationerna levererade händelser i olika scheman.

Det första prenumerations schemat för Event Grid. Formatet för den levererade händelsen är:

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

Dessa fält innehåller mappningarna från det anpassade ämnet. **myEventTypeField** mappas till **EventType**. Standardvärdena för **DataVersion** och **subject** används. **Data** -objektet innehåller de ursprungliga händelse schema fälten.

Den andra prenumerationen använde schemat för indatamängds händelser. Formatet för den levererade händelsen är:

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

* Om du vill ha information om händelse leverans och försök kan du [Event Grid meddelande leverans och försöka igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
