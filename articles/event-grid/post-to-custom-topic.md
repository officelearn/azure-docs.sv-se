---
title: Publicera händelse i anpassat Azure Event Grid-ämne
description: I den här artikeln beskrivs hur du publicerar en händelse i ett anpassat ämne. Den visar formatet på post- och händelsedata.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 0afad249f71a36bf7552da499e985b68d48ee7a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721565"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Publicera i anpassat ämne för Azure Event Grid

I den här artikeln beskrivs hur du publicerar en händelse i ett anpassat ämne. Den visar formatet på post- och händelsedata. [Servicenivåavtalet (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) gäller endast inlägg som matchar det förväntade formatet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Slutpunkt

När http-post skickas till ett anpassat ämne `https://<topic-endpoint>?api-version=2018-01-01`använder du URI-formatet: .

En giltig URI är `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`till exempel: .

Om du vill hämta slutpunkten för ett anpassat ämne med Azure CLI använder du:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Om du vill hämta slutpunkten för ett anpassat ämne med Azure PowerShell använder du:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Huvud

I begäran ska du inkludera `aeg-sas-key` ett rubrikvärde med namnet som innehåller en nyckel för autentisering.

Ett giltigt huvudvärde är `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`till exempel .

Om du vill hämta nyckeln för ett anpassat ämne med Azure CLI använder du:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Om du vill hämta nyckeln för ett anpassat ämne med PowerShell använder du:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Händelsedata

För anpassade avsnitt innehåller data på den översta nivån samma fält som standardresursdefinierade händelser. En av dessa egenskaper är en dataegenskap som innehåller egenskaper som är unika för det anpassade avsnittet. Som händelseutgivare bestämmer du egenskaperna för det dataobjektet. Använd följande schema:

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

En beskrivning av dessa egenskaper finns i [Azure Event Grid-händelseschema](event-schema.md). När du bokför händelser i ett händelserutnätsavsnitt kan matrisen ha en total storlek på upp till 1 MB. Varje händelse i matrisen är begränsad till 64 KB (allmän tillgänglighet) eller 1 MB (förhandsversion).

> [!NOTE]
> En händelse av storlek upp till 64 KB omfattas av Service Level Agreement (General Availability) Service Level Agreement (SLA). Stödet för en händelse med storlek upp till 1 MB är för närvarande i förhandsversion. Händelser över 64 KB debiteras i steg om 64 KB. 

Ett giltigt händelsedataschema är till exempel:

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

När du har publicerat till ämnesslutpunkten får du ett svar. Svaret är en standard-HTTP-svarskod. Några vanliga svar är:

|Resultat  |Svar  |
|---------|---------|
|Lyckades  | 200 OK  |
|Händelsedata har felaktigt format | 400 dålig begäran |
|Ogiltig åtkomstnyckel | 401 Obehörig |
|Felaktig slutpunkt | 404 – Hittades inte |
|Matris eller händelse överskrider storleksgränser | 413 Nyttolast för stor |

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

* Information om hur du övervakar händelseleveranser finns i [Övervaka leverans av händelserutnätsmeddelanden](monitor-event-delivery.md).
* Mer information om autentiseringsnyckeln finns i [säkerhet och autentisering av Händelserutnät](security-authentication.md).
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
