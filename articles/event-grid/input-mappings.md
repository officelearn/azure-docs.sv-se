---
title: Mappa anpassat fält till Azure Event Grid-schema
description: Beskriver hur du konverterar ditt anpassade schema i Azure Event Grid-schemat.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 32f93f383ec4044afb0696fcef1705c9ed65d673
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38578925"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mappa eget fält till Event Grid-schema

Om dina händelsedata inte matchar den förväntade [Event Grid-schemat](event-schema.md), du kan fortfarande använda Event Grid på vägen händelse till prenumeranter. Den här artikeln beskriver hur du mappar ditt schema i Event Grid-schemat.

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

När du skapar ett anpassat ämne, ange mappning från din ursprungliga händelse i event grid-schemat. Det finns tre egenskaper som du använder för att anpassa mappningen:

* Den `--input-schema` parametern anger vilken typ av schema. De tillgängliga alternativen är *cloudeventv01schema*, *customeventschema*, och *eventgridschema*. Standardvärdet är eventgridschema. När du skapar anpassade mappningen mellan ditt schema och event grid-schemat kan du använda customeventschema. Använd cloudeventv01schema när händelser är i CloudEvents-schema.

* Den `--input-mapping-default-values` parametern anger standardvärden för fält i Event Grid-schemat. Du kan ange standardvärden för *ämne*, *händelsetyp*, och *dataversion*. Normalt använder du den här parametern när din anpassade schemat inte innehåller ett fält som överensstämmer med någon av dessa tre fält. Du kan till exempel ange den dataversion har alltid värdet **1.0**.

* Den `--input-mapping-fields` parametern mappar fält från ditt schema i event grid-schemat. Du anger värden i blankstegsavgränsad nyckel/värde-par. Använd namnet på event grid fältet Namn på nyckeln. Använd namnet på fältet som värde. Du kan använda nyckelnamnen för *id*, *avsnittet*, *eventtime*, *ämne*, *händelsetyp*, och *dataversion*.

I följande exempel skapas ett anpassat ämne med några mappade och standardfält:

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>Prenumerera på event grid-ämne

Prenumerera på anpassat ämne, ange det schema som du vill använda för att ta emot händelser. Du använder den `--event-delivery-schema` parametern och ge den värdet *cloudeventv01schema*, *eventgridschema*, eller *inputeventschema*. Standardvärdet är eventgridschema.

I exemplen i det här avsnittet används en Queue storage för händelsehanteraren. Mer information finns i [dirigera anpassade händelser till Azure Queue storage](custom-event-to-queue-storage.md).

I följande exempel prenumererar på ett event grid-ämne och använder standardschemat för event grid:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

I nästa exempel används inmatningsschemat för händelsen:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Publicera händelsen till ämnet

Du är nu redo att skicka en händelse till det anpassade ämnet och se resultatet av mappningen. Följande skript för att publicera en händelse i den [exempel schemat](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Nu kan du titta på Queue storage. De två prenumerationerna levereras händelser i olika scheman.

Den första prenumerationen används event grid-schemat. Formatet på levererade händelsen är:

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Dessa fält som innehåller mappningar från det anpassade ämnet. **myEventTypeField** mappas till **händelsetyp**. Standardvärdena för **DataVersion** och **ämne** används. Den **Data** objekt innehåller fälten schemat ursprungliga händelse.

Den andra prenumerationen används inmatningshändelse schemat. Formatet på levererade händelsen är:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Observera att de ursprungliga fält har levererats.

## <a name="next-steps"></a>Nästa steg

* Information om händelseleverans och återförsök, [Event Grid meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
