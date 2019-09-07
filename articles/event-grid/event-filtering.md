---
title: Händelse filtrering för Azure Event Grid
description: Beskriver hur du filtrerar händelser när du skapar en Azure Event Grid-prenumeration.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: f9fca0a9fefb5959747a4492139ae422a118db02
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390179"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Förstå händelse filtrering för Event Grid prenumerationer

I den här artikeln beskrivs olika sätt att filtrera vilka händelser som skickas till din slut punkt. När du skapar en händelse prenumeration har du tre alternativ för filtrering:

* Händelse typer
* Ämne börjar med eller slutar med
* Avancerade fält och operatorer

## <a name="event-type-filtering"></a>Händelse typs filtrering

Som standard skickas alla [händelse typer](event-schema.md) för händelse källan till slut punkten. Du kan välja att bara skicka vissa händelse typer till din slut punkt. Du kan till exempel få ett meddelande om uppdateringar av dina resurser, men inte meddelas om andra åtgärder, t. ex. borttagningar. I så fall filtrerar du `Microsoft.Resources.ResourceWriteSuccess` efter händelse typ. Ange en matris med händelse typerna eller ange `All` för att hämta alla händelse typer för händelse källan.

JSON-syntaxen för att filtrera efter händelse typ är:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Ämnes filtrering

För enkel filtrering efter ämne anger du ett start-eller slut värde för ämnet. Du kan till exempel ange att ämnet slutar med `.txt` om du bara vill hämta händelser som rör överföring av en textfil till lagrings kontot. Eller så kan du filtrera ämnet börjar med `/blobServices/default/containers/testcontainer` för att hämta alla händelser för behållaren, men inte andra behållare i lagrings kontot.

När du publicerar händelser till anpassade ämnen skapar du ämnen för dina händelser som gör det enkelt för prenumeranter att veta om de är intresserade av evenemanget. Prenumeranter använder egenskapen subject för att filtrera och dirigera händelser. Överväg att lägga till sökvägen till platsen där händelsen inträffade, så att prenumeranter kan filtrera efter segment i den sökvägen. Med hjälp av sökvägen kan prenumeranter begränsa eller filtrera händelser på ett stort sätt. Om du anger en tre segment Sök väg `/A/B/C` som i ämnet kan prenumeranter filtrera efter det första segmentet `/A` för att få en bred uppsättning händelser. Dessa prenumeranter får händelser med ämnen `/A/B/C` som `/A/D/E`eller. Andra prenumeranter kan filtrera `/A/B` efter för att få en smalare uppsättning händelser.

JSON-syntaxen för att filtrera efter ämne är:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Avancerad filtrering

Använd alternativet avancerad filtrering för att filtrera efter värden i data fälten och ange jämförelse operatorn. I avancerad filtrering anger du:

* Operator typ – typen av jämförelse.
* Key – fältet i de händelse data som du använder för filtrering. Det kan vara ett tal, en boolesk sträng eller en sträng.
* värde eller värden – värdet eller värdena som ska jämföras med nyckeln.

Om du anger ett enskilt filter med flera värden utförs en- **eller** -åtgärd, så värdet för nyckel fältet måste vara ett av dessa värden. Här är ett exempel:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Om du anger flera olika filter utförs en- **och** -åtgärd, så varje filter villkor måste vara uppfyllt. Här är ett exempel: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operator"></a>Operator

De tillgängliga operatorerna för tal är:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* Numberin
* NumberNotIn

Den tillgängliga operatorn för booleska värden är: BoolEquals

Tillgängliga operatorer för strängar är:

* StringContains
* StringBeginsWith
* StringEndsWith
* Strängin
* StringNotIn

Alla sträng jämförelser är Case-insensitve.

### <a name="key"></a>Nyckel

För händelser i Event Grid schemat använder du följande värden för nyckeln:

* id
* Avsnitt
* Subject
* Händelsetyp
* DataVersion
* Händelse data (t. ex. data. KEY1)

För händelser i Cloud Events-schemat använder du följande värden för nyckeln:

* EventId
* Source
* Händelsetyp
* EventTypeVersion
* Händelse data (t. ex. data. KEY1)

Använd händelse data fält (t. ex. data. KEY1) för anpassade indata-schema.

### <a name="values"></a>Värden

Värdena kan vara:

* nummer
* sträng
* boolean
* array

### <a name="limitations"></a>Begränsningar

Avancerad filtrering har följande begränsningar:

* Fem avancerade filter per Event Grid-prenumeration
* 512 tecken per sträng värde
* Fem värden för **in** -och **not** -operatorer

Samma nyckel kan användas i mer än ett filter.

## <a name="next-steps"></a>Nästa steg

* Information om hur du filtrerar händelser med PowerShell och Azure CLI finns i [Filtrera händelser för Event Grid](how-to-filter-events.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
