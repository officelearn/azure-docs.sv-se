---
title: Händelse filtrering för Azure Event Grid
description: Beskriver hur du filtrerar händelser när du skapar en Azure Event Grid-prenumeration.
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: bc3e84037693fcd909961ba409871d947ef1de7d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574914"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Förstå händelse filtrering för Event Grid prenumerationer

I den här artikeln beskrivs olika sätt att filtrera vilka händelser som skickas till din slut punkt. När du skapar en händelse prenumeration har du tre alternativ för filtrering:

* Händelsetyper
* Ämne börjar med eller slutar med
* Avancerade fält och operatorer

## <a name="event-type-filtering"></a>Händelse typs filtrering

Som standard skickas alla [händelse typer](event-schema.md) för händelse källan till slut punkten. Du kan välja att bara skicka vissa händelse typer till din slut punkt. Du kan till exempel få ett meddelande om uppdateringar av dina resurser, men inte meddelas om andra åtgärder, t. ex. borttagningar. I så fall filtrerar du efter `Microsoft.Resources.ResourceWriteSuccess` händelse typ. Ange en matris med händelse typerna eller ange `All` för att hämta alla händelse typer för händelse källan.

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

När du publicerar händelser till anpassade ämnen skapar du ämnen för dina händelser som gör det enkelt för prenumeranter att veta om de är intresserade av evenemanget. Prenumeranter använder egenskapen subject för att filtrera och dirigera händelser. Överväg att lägga till sökvägen till platsen där händelsen inträffade, så att prenumeranter kan filtrera efter segment i den sökvägen. Med hjälp av sökvägen kan prenumeranter begränsa eller filtrera händelser på ett stort sätt. Om du anger en tre segment Sök väg som `/A/B/C` i ämnet kan prenumeranter filtrera efter det första segmentet `/A` för att få en bred uppsättning händelser. Dessa prenumeranter får händelser med ämnen som `/A/B/C` eller `/A/D/E` . Andra prenumeranter kan filtrera efter `/A/B` för att få en smalare uppsättning händelser.

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
* värden – värdet eller värdena som ska jämföras med nyckeln.

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

### <a name="operators"></a>Operatorer

De tillgängliga operatorerna för **tal** är:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* Numberin
* NumberNotIn

Den tillgängliga operatorn för **booleska värden** är: 
- BoolEquals

Tillgängliga operatorer för **strängar** är:

* StringContains
* StringBeginsWith
* StringEndsWith
* Strängin
* StringNotIn

Alla sträng jämförelser är **inte** Skift läges känsliga.

> [!NOTE]
> Om händelse-JSON inte innehåller den avancerade filter nyckeln är filtret evaulated som **ej matchat** för följande operatorer: 
> - NumberGreaterThan
> - NumberGreaterThanOrEquals
> - NumberLessThan
> - NumberLessThanOrEquals
> - Numberin
> - BoolEquals
> - StringContains
> - StringBeginsWith
> - StringEndsWith
> - Strängin
> 
>Filtret är evaulated **matchat** för följande operatorer:
> - NumberNotIn
> - StringNotIn

### <a name="key"></a>Nyckel

För händelser i Event Grid schemat använder du följande värden för nyckeln:

* ID
* Ämne
* Ämne
* Typ
* DataVersion
* Händelse data (t. ex. data. KEY1)

För händelser i Cloud Events-schemat använder du följande värden för nyckeln:

* EventId
* Källa
* Typ
* EventTypeVersion
* Händelse data (t. ex. data. KEY1)

Använd händelse data fält (t. ex. data. KEY1) för anpassade indata-schema.

### <a name="values"></a>Värden

Värdena kan vara:

* antal
* sträng
* boolean
* matris

### <a name="limitations"></a>Begränsningar

Avancerad filtrering har följande begränsningar:

* 5 avancerade filter och 25 filter värden i alla filter per Event Grid-prenumeration
* 512 tecken per sträng värde
* Fem värden för **in** -och **not** -operatorer
* Nycklar med **`.` (punkt)** -tecknen i dem. Till exempel: `http://schemas.microsoft.com/claims/authnclassreference` eller `john.doe@contoso.com` . För närvarande finns det inget stöd för escape-tecken i nycklar. 

Samma nyckel kan användas i mer än ett filter.

### <a name="examples"></a>Exempel

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>Strängin

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>Numberin

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>Nästa steg

* Information om hur du filtrerar händelser med PowerShell och Azure CLI finns i [Filtrera händelser för Event Grid](how-to-filter-events.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
