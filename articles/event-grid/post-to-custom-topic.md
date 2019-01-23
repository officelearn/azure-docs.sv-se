---
title: Skicka händelsen till anpassat Azure Event Grid-ämne
description: Beskriver hur man publicerar en händelse till ett anpassat ämne för Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: b219e9475151ecd14d8b45db9501a06cde05875b
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470604"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Skicka till anpassat ämne för Azure Event Grid

Den här artikeln beskriver hur man publicerar en händelse till ett anpassat ämne. Den visar formatet för post- och händelse-data. Den [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) gäller endast för inlägg som matchar det förväntade formatet.

## <a name="endpoint"></a>Slutpunkt

När du skickar HTTP POST till ett anpassat ämne, använder du URI-format: `https://<topic-endpoint>?api-version=2018-01-01`.

En giltig URI är till exempel: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Om du vill hämta slutpunkten för ett anpassat ämne med Azure CLI, använder du:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Om du vill hämta slutpunkten för ett anpassat ämne med Azure PowerShell använder du:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Huvud

Inkludera ett huvudvärde med namnet i begäran, `aeg-sas-key` som innehåller en nyckel för autentisering.

Ett ogiltigt huvudvärde är till exempel `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Om du vill hämta nyckel för ett anpassat ämne med Azure CLI, använder du:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Om du vill hämta nyckel för ett anpassat ämne med PowerShell använder du:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Händelsedata

Anpassade ämnen innehåller översta data samma fält som standard resurs-definierade händelser. En av dessa egenskaper är en data-egenskap som innehåller egenskaper som är unika för det anpassade ämnet. När händelseutfärdare bestämmer du egenskaperna för dataobjektet. Använd följande schema:

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

En beskrivning av de här egenskaperna finns i [Azure Event Grid Händelseschema](event-schema.md). När skicka händelser till en event grid-ämne kan matrisen ha en total storlek på upp till 1 MB. Varje händelse i matrisen är begränsat till 64 KB.

En giltig data-Händelseschema är till exempel:

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

När du publicera till avsnittet slutpunkten får du ett svar. Svaret är en HTTP-svarskoden som standard. Vissa vanliga svar är:

|Resultat  |Svar  |
|---------|---------|
|Lyckades  | 200 OK  |
|Händelsedata är i felaktigt format | 400 Felaktig förfrågan |
|Ogiltig åtkomstnyckel | 401 Ej behörig |
|Felaktig slutpunkt | 404 Hittades inte |
|Matris eller händelse överskrider storleksgränsen | 413 nyttolast som är för stor |

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

* Information om hur du övervakar händelse leveranser finns i [övervaka Event Grid meddelandeleverans](monitor-event-delivery.md).
* Läs mer om autentiseringsnyckeln [Event Grid säkerhet och autentisering](security-authentication.md).
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
