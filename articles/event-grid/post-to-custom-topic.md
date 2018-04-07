---
title: Skicka händelsen till anpassad Azure händelse rutnätet ämne
description: Beskriver hur du publicerar en händelse till ett eget avsnitt för rutnät för Azure-händelse
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 1c23aef0773ffddbc26e4090ecf137b632394ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Efter att anpassade avsnittet för rutnät för Azure-händelse

Den här artikeln beskriver hur du publicerar en händelse till ett eget avsnitt. Formatet för post- och händelsen data visas. Den [serviceavtalet (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) gäller bara poster som matchar det förväntade formatet.

## <a name="endpoint"></a>Slutpunkt

När du skickar HTTP POST till en anpassad avsnittet använda URI-format: `https://<topic-endpoint>?api-version=2018-01-01`.

En giltig URI är till exempel: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Om du vill hämta slutpunkten för en anpassad avsnittet med Azure CLI, använder du:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Om du vill hämta slutpunkten för en anpassad avsnittet med Azure PowerShell, använder du:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Sidhuvud

I en begäran innehåller ett värde med ett huvud med namnet `aeg-sas-key` som innehåller en nyckel för autentisering.

Ett ogiltigt huvudvärde är till exempel `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Om du vill hämta nyckel för en anpassad avsnittet med Azure CLI, använder du:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Om du vill hämta nyckel för en anpassad avsnittet med PowerShell använder du:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Händelsedata

För anpassade ämnen innehåller på översta nivån data samma fält som standard resurs användardefinierade händelser. En av dessa egenskaper är en data-egenskap som innehåller egenskaper som är unika för det anpassade avsnittet. När händelsen utgivare bestämmer du egenskaperna för detta dataobjekt. Använd följande schema:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

En beskrivning av de här egenskaperna finns [Azure händelse rutnätet Händelseschema](event-schema.md).

Ett giltigt händelsenamn data schema är till exempel:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Svar

Efter bokföring till avsnittet slutpunkten får du svar. Svaret är en standard HTTP-svarskoden. Några vanliga svar är:

|Resultat  |Svar  |
|---------|---------|
|Lyckades  | 200 OK  |
|Felaktig slutpunkt | 404 Hittades inte |
|Ogiltig snabbtangent | 401 obehörig |
|Händelsedata är i felaktigt format | 400 Felaktig förfrågan |

För fel har meddelandetexten följande format:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du övervakar händelse leveranser finns [övervakaren händelse rutnätet meddelandeleverans](monitor-event-delivery.md).
* Läs mer om autentiseringsnyckeln [händelse rutnätet säkerhets- och autentiseringstjänster](security-authentication.md).
* Mer information om hur du skapar en prenumeration på Azure händelse rutnätet finns [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).
