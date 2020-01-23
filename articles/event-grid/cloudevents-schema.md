---
title: Använda Azure Event Grid med händelser i CloudEvents-schemat
description: Beskriver hur du använder CloudEvents-schemat för händelser i Azure Event Grid. Tjänsten stöder händelser i JSON-implementeringen av moln händelser.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: f903a358ea493cd01238339ede10b4b16f98c7c5
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514603"
---
# <a name="use-cloudevents-v10-schema-with-event-grid"></a>Använd CloudEvents v 1.0-schemat med Event Grid

Förutom dess [standard händelse schema](event-schema.md)har Azure Event Grid inbyggt stöd för händelser i [JSON-implementeringen av CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) och [http-protokoll bindning](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) är en [öppen specifikation](https://github.com/cloudevents/spec/blob/v1.0/spec.md) för att beskriva händelse data.

CloudEvents fören klar samverkan genom att tillhandahålla ett gemensamt händelse schema för publicering, och konsumera molnbaserade händelser. Det här schemat möjliggör enhetlig verktygs hantering, vanliga sätt att dirigera & hanterings händelser och Universal sätt att avserialisera det yttre händelse schemat. Med ett gemensamt schema kan du enklare integrera arbete på olika plattformar.

CloudEvents skapas av flera [medarbetare](https://github.com/cloudevents/spec/blob/master/community/contributors.md), inklusive Microsoft, via [Cloud Native Computing Foundation](https://www.cncf.io/). Den är för närvarande tillgänglig som version 1,0.

Den här artikeln beskriver hur du använder CloudEvents-schemat med Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Installera förhandsversionsfunktionen

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent-schema

Här är ett exempel på en Azure Blob Storage-händelse i CloudEvents-format:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

En detaljerad beskrivning av tillgängliga fält, deras typer och definitioner i CloudEvents v 1.0 finns [här](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Rubrik värden för händelser som levereras i CloudEvents-schemat och Event Grid schemat är desamma förutom för `content-type`. För CloudEvents-schema är detta huvud värde `"content-type":"application/cloudevents+json; charset=utf-8"`. För Event Grid schema är detta huvud värde `"content-type":"application/json; charset=utf-8"`.

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurera Event Grid för CloudEvents

Du kan använda Event Grid för både indata och utdata av händelser i CloudEvents-schemat. Du kan använda CloudEvents för system händelser, t. ex. Blob Storage händelser och IoT Hub händelser och anpassade händelser. Den kan också omvandla dessa händelser till den igen.


| Schema för indatamängd       | Schema för utdata
|--------------------|---------------------
| CloudEvents-format | CloudEvents-format
| Event Grid format  | CloudEvents-format
| CloudEvents-format | Event Grid format
| Event Grid format  | Event Grid format

För alla händelse scheman måste Event Grid verifiera vid publicering till ett event Grid-ämne och när en händelse prenumeration skapas. Mer information finns i [Event Grid säkerhet och autentisering](security-authentication.md).

### <a name="input-schema"></a>Schema för indatamängd

Du ställer in schemat för ett anpassat ämne när du skapar det anpassade ämnet.

Om du använder Azure CLI använder du:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventschemav1_0
```

Om du använder PowerShell använder du:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventSchemaV1_0
```

Den aktuella versionen av CloudEvents stöder inte batching av händelser. Publicera händelser med CloudEvent-schema till ett ämne genom att publicera varje händelse individuellt.

### <a name="output-schema"></a>Schema för utdata

Du ställer in output-schemat när du skapar händelse prenumerationen.

Om du använder Azure CLI använder du:

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventschemav1_0
```

Om du använder PowerShell använder du:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventSchemaV1_0
```

 För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i CloudEvents-schemat. Använd en HTTP-utlösare. Exempel på hur du implementerar en HTTP-utlösare som tar emot händelser i CloudEvents-schemat finns i [använda en HTTP-utlösare som en Event Grid trigger](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

 ## <a name="endpoint-validation-with-cloudevents-v10"></a>Slut punkts validering med CloudEvents v 1.0

Om du redan är bekant med Event Grid kan du vara medveten om Event Gridens slut punkts validerings hand skakning för att förhindra missbruk. CloudEvents v 1.0 implementerar egna [insemantiker för missbruks skydd](security-authentication.md#webhook-event-delivery) med hjälp av http-alternativ-metoden. Du kan läsa mer om det [här](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). När du använder CloudEvents-schemat för utdata använder Event Grid med CloudEvents v 1.0 missbruk-skyddet i stället för mekanismen för Event Grid validerings händelser.

## <a name="next-steps"></a>Nästa steg

* Information om övervakning av händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Vi rekommenderar att du testar, kommenterar och [bidrar](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) till CloudEvents.
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
