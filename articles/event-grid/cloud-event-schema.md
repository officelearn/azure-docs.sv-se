---
title: Använda Azure Event Grid med händelser i CloudEvents-schemat
description: Beskriver hur du använder CloudEvents-schemat för händelser i Azure Event Grid. Tjänsten stöder händelser i JSON-implementeringen av molnhändelser.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394389"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>CloudEvents v1.0-schema med eventrutnätet

Förutom [standardhändelseschemat](event-schema.md)stöder Azure Event Grid inbyggt händelser i [JSON-implementeringen av CloudEvents v1.0-](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) och [HTTP-protokollbindning](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) är en [öppen specifikation](https://github.com/cloudevents/spec/blob/v1.0/spec.md) för att beskriva händelsedata.

CloudEvents förenklar interoperabilitet genom att tillhandahålla ett gemensamt händelseschema för publicering och förbruka molnbaserade händelser. Det här schemat möjliggör enhetliga verktyg, standardsätt för routning & hantering av händelser och universella sätt att deserialisera det yttre händelseschemat. Med ett gemensamt schema kan du lättare integrera arbete på olika plattformar.

CloudEvents byggs av flera [medarbetare](https://github.com/cloudevents/spec/blob/master/community/contributors.md), inklusive Microsoft, via [Cloud Native Computing Foundation](https://www.cncf.io/). Det är för närvarande tillgänglig som version 1.0.

I den här artikeln beskrivs CloudEvents-schemat med Event Grid.

## <a name="sample-event-using-cloudevents-schema"></a>Exempel på händelse med CloudEvents-schema

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

En detaljerad beskrivning av tillgängliga fält, deras typer och definitioner i CloudEvents v1.0 [finns här](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Huvudenvärdena för händelser som levereras i CloudEvents-schemat och `content-type`eventrutnätet-schemat är desamma förutom . För CloudEvents-schema är `"content-type":"application/cloudevents+json; charset=utf-8"`det huvudvärdet . För event grid-schema är `"content-type":"application/json; charset=utf-8"`det huvudvärdet .

## <a name="event-grid-for-cloudevents"></a>Händelserutnät för CloudEvents

Du kan använda Event Grid för både indata och utdata av händelser i CloudEvents-schemat. Du kan använda CloudEvents för systemhändelser, till exempel Blob Storage-händelser och IoT Hub-händelser och anpassade händelser. Det kan också omvandla dessa händelser på tråden fram och tillbaka.


| Indataschema       | Utdataschema
|--------------------|---------------------
| CloudEvents-format | CloudEvents-format
| Format för händelserutnät  | CloudEvents-format
| Format för händelserutnät  | Format för händelserutnät

För alla händelsescheman kräver Event Grid validering när du publicerar till ett händelserutnätsämne och när du skapar en händelseprenumeration. Mer information finns i [Säkerhet och autentisering av Händelserutnät](security-authentication.md).

## <a name="next-steps"></a>Nästa steg
Se [Hur du använder CloudEvents v1.0-schema med Event Grid](cloudevents-schema.md).  
