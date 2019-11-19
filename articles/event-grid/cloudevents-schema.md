---
title: Använda Azure Event Grid med händelser i CloudEvents-schemat
description: Beskriver hur du ställer in CloudEvents-schemat för händelser i Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 8925110511f6c63a42dd9b121429ac7264cd4aa4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170236"
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

En detaljerad beskrivning av tillgängliga fält, deras typer och definitioner i CloudEvents v 0,1 finns [här](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

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
  --input-schema cloudeventv01schema
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
  -InputSchema CloudEventV01Schema
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
  --event-delivery-schema cloudeventv01schema
```

Om du använder PowerShell använder du:
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

 För närvarande kan du inte använda en Event Grid-utlösare för en Azure Functions-app när händelsen levereras i CloudEvents-schemat. Använd en HTTP-utlösare. Exempel på hur du implementerar en HTTP-utlösare som tar emot händelser i CloudEvents-schemat finns i [använda en HTTP-utlösare som en Event Grid trigger](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger).

## <a name="next-steps"></a>Nästa steg

* Information om övervakning av händelse leveranser finns i [övervaka Event Grid meddelande leverans](monitor-event-delivery.md).
* Vi rekommenderar att du testar, kommenterar och [bidrar](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) till CloudEvents.
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
