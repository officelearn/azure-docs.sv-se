---
title: Händelsefiltrering för Azure Event Grid
description: Beskriver hur du filtrerar händelser när du skapar en Azure Event Grid-prenumeration.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: ce1bb3760ae73a9eaeee3cde957cc94841ebdf29
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731941"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Förstå händelsefiltrering för prenumerationer på eventrutnät

I den här artikeln beskrivs olika sätt att filtrera vilka händelser som skickas till slutpunkten. När du skapar en händelseprenumeration har du tre alternativ för filtrering:

* Händelsetyper
* Ämne börjar med eller slutar med
* Avancerade fält och operatorer

## <a name="event-type-filtering"></a>Filtrering av händelsetyp

Som standard skickas alla [händelsetyper](event-schema.md) för händelsekällan till slutpunkten. Du kan välja att bara skicka vissa händelsetyper till slutpunkten. Du kan till exempel få meddelanden om uppdateringar av dina resurser, men inte aviseringar om andra åtgärder som borttagningar. I så fall filtrera `Microsoft.Resources.ResourceWriteSuccess` efter händelsetypen. Ange en matris med händelsetyperna `All` eller ange att alla händelsetyper för händelsekällan ska hämtas.

JSON-syntaxen för filtrering efter händelsetyp är:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Ämnesfiltrering

Om du vill filtrera efter ämne anger du ett start- eller slutvärde för ämnet. Du kan till exempel ange `.txt` att ämnet slutar med att bara hämta händelser relaterade till att ladda upp en textfil till lagringskonto. Du kan också filtrera ämnet `/blobServices/default/containers/testcontainer` börjar med för att hämta alla händelser för den behållaren men inte andra behållare i lagringskontot.

När du publicerar händelser i anpassade ämnen skapar du ämnen för dina evenemang som gör det enkelt för prenumeranter att veta om de är intresserade av händelsen. Prenumeranter använder ämnesegenskapen för att filtrera och dirigera händelser. Överväg att lägga till sökvägen för var händelsen inträffade, så att prenumeranter kan filtrera efter segment av sökvägen. Sökvägen gör det möjligt för prenumeranter att filtrera händelser snävt eller brett. Om du anger en `/A/B/C` sökväg med tre segment som `/A` i ämnet kan prenumeranter filtrera efter det första segmentet för att få en bred uppsättning händelser. Dessa abonnenter få evenemang `/A/B/C` `/A/D/E`med ämnen som eller . Andra prenumeranter kan filtrera efter `/A/B` att få en smalare uppsättning händelser.

JSON-syntaxen för filtrering efter ämne är:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Avancerad filtrering

Om du vill filtrera efter värden i datafälten och ange jämförelseoperatorn använder du alternativet avancerad filtrering. Vid avancerad filtrering anger du:

* operatörstyp - Typen av jämförelse.
* nyckel - Fältet i händelsedata som du använder för filtrering. Det kan vara ett tal, booleskt eller sträng.
* värden - Värdet eller värdena som ska jämföras med nyckeln.

Om du anger ett enda filter med flera värden utförs en **ELLER-åtgärd,** så värdet för nyckelfältet måste vara ett av dessa värden. Här är ett exempel:

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

Om du anger flera olika filter utförs en **AND-åtgärd,** så varje filtervillkor måste uppfyllas. Här är ett exempel: 

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

De tillgängliga operatörerna för nummer är:

* AntalTörretr
* Antal störrethanorEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn (Olikartade)
* Antal InteIn

Den tillgängliga operatören för booleans är: BoolEquals

De tillgängliga operatorerna för strängar är:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn (StringIn)
* StringNotIn

Alla strängjämförelser är case-insensitve.

### <a name="key"></a>Nyckel

För händelser i eventrutnätet-schemat använder du följande värden för nyckeln:

* ID
* Hjälpavsnitt
* Subjekt
* Eventtype
* DataVersion (DataVersion)
* Händelsedata (som Data.key1)

För händelser i cloud events-schemat använder du följande värden för nyckeln:

* EventId (på)EventId )
* Källa
* Eventtype
* EventTypeVersion
* Händelsedata (som Data.key1)

För anpassat indataschema använder du händelsedatafälten (till exempel Data.key1).

### <a name="values"></a>Värden

Värdena kan vara:

* nummer
* sträng
* boolean
* matris

### <a name="limitations"></a>Begränsningar

Avancerad filtrering har följande begränsningar:

* Fem avancerade filter per prenumeration på händelserutnät
* 512 tecken per strängvärde
* Fem värden för **i-** och **inte operatorer**

Samma nyckel kan användas i mer än ett filter.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du filtrerar händelser med PowerShell och Azure CLI finns i [Filtrera händelser för Händelserutnät](how-to-filter-events.md).
* Information om hur du snabbt kommer igång med Event Grid finns i [Skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
