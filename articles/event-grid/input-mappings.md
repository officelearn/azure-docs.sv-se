---
title: Mappa anpassat fält till Azure händelse rutnätet schema
description: Beskriver hur du omvandla det anpassa schemat till Azure händelse rutnätet schemat.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 1e809f83b43c32031b66c8f470575da6e9fcdc56
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mappa fält till händelse rutnätet schema

Om din händelsedata inte matchar den förväntade [händelse rutnätet schemat](event-schema.md), du kan fortfarande använda händelsen rutnätet på vägen händelsen till prenumeranter. Den här artikeln beskriver hur du mappar schemat till händelse rutnätet schemat.

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

Trots att formatet inte matchar det nödvändiga schemat, kan händelse rutnätet du mappa fält till schemat. Eller, du kan få värdena i det ursprungliga schemat.

## <a name="create-custom-topic-with-mapped-fields"></a>Skapa anpassade avsnittet med mappade fält

När du skapar en anpassad avsnittet Ange mappning från din ursprungliga händelsen till händelse rutnätet schemat. Det finns tre egenskaper som du använder för att anpassa mappningen:

* Den `--input-schema` parametern anger vilken typ av schema. Tillgängliga alternativ är *cloudeventv01schema*, *customeventschema*, och *eventgridschema*. Standardvärdet är eventgridschema. När du skapar anpassade mappning mellan schemat och rutnätet Händelseschema Använd customeventschema. När händelser finns i CloudEvents schemat använder du cloudeventv01schema.

* Den `--input-mapping-default-values` parametern anger standardvärden för fält i händelsen rutnätet schemat. Du kan ange standardvärden för *ämne*, *eventtype*, och *dataversion*. Normalt använder du den här parametern när det anpassa schemat inte innehåller ett fält som motsvarar en av dessa tre fält. Du kan till exempel ange att dataversion alltid som **1.0**.

* Den `--input-mapping-fields` parametern mappar fält från schemat till rutnätet Händelseschema. Du kan ange värden i blankstegsavgränsad nyckel/värde-par. Namn på nyckeln använda namnet på fältet händelse rutnätet. Använd namnet på fältet för värdet. Du kan använda nyckelnamn för *id*, *avsnittet*, *eventtime*, *ämne*, *eventtype*, och *dataversion*.

I följande exempel skapar en anpassad avsnittet med några mappas och fält som standard:

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

## <a name="subscribe-to-event-grid-topic"></a>Prenumerera på händelsen rutnätet avsnittet

När du prenumererar på avsnittet anpassade, ange det schema som du vill använda för att ta emot händelser. Du använder den `--event-delivery-schema` parametern och ange det till *cloudeventv01schema*, *eventgridschema*, eller *inputeventschema*. Standardvärdet är eventgridschema.

Exemplen i det här avsnittet kan du använda en Queue storage för händelsehanteraren. Mer information finns i [vidarebefordra anpassade händelser till Azure Queue storage](custom-event-to-queue-storage.md).

I följande exempel prenumererar på en händelse rutnätet avsnittet och använder standardschemat för rutnät i händelsen:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

I nästa exempel används inmatningsschemat i händelsen:

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Publicera händelsen till avsnittet

Du är nu redo att skicka en händelse till anpassade ämnet och se resultatet av mappningen. Följande skript för att publicera en händelse i den [exempel schemat](#original-event-schema):

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Nu kan du titta på Queue storage. Två prenumerationer leverera händelser i olika scheman.

Första prenumerationen används Händelseschema rutnätet. Formatet på händelsen levererat är:

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

Dessa fält innehåller mappningar från avsnittet anpassade. **myEventTypeField** mappas till **EventType**. Standardvärdena för **DataVersion** och **ämne** används. Den **Data** objektet innehåller de ursprungliga händelse schemafält.

Andra prenumerationen används indatahändelsen schemat. Formatet på händelsen levererat är:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Observera att de ursprungliga fälten har levererats.

## <a name="next-steps"></a>Nästa steg

* Information om händelsen leverans och omförsök, [händelse rutnätet meddelandeleverans och försök igen](delivery-and-retry.md).
* En introduktion till händelse rutnätet finns [om händelsen rutnätet](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).
