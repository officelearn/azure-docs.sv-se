---
title: Händelse som filtrering för Azure Event Grid
description: Beskriver hur du kan filtrera händelser när du skapar en Azure Event Grid-prenumeration.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 87599b05a3569bf6f28880352185a131f48a7f52
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470641"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Förstå händelse filtrering för Event Grid-prenumerationer

Den här artikeln beskrivs olika sätt att filtrera vilka händelser som ska skickas till din slutpunkt. När du skapar en händelseprenumeration, finns det tre alternativ för filtrering:

* Händelsetyper
* Ämnet som börjar med eller slutar med
* Avancerade fält och operatorer

## <a name="event-type-filtering"></a>Typ av händelsefiltrering

Som standard alla [händelsetyper](event-schema.md) för händelsekällan skickas till slutpunkten. Du kan välja att skicka endast vissa typer av till din slutpunkt. Exempel: du kan hämta meddelandet om uppdateringar till dina resurser, men meddelas inte för andra åtgärder som borttagningar. I så fall kan filtrera efter den `Microsoft.Resources.ResourceWriteSuccess` händelsetyp. Ange en matris med händelsetyperna eller ange `All` att hämta alla händelsetyper för händelsekällan.

JSON-syntaxen för att filtrera efter händelsetyp är:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Ämne filtrering

För enkel filtrering efter ämne, ange ett start- och för ämnet. Du kan till exempel ange ämnet slutar med `.txt` att endast hämta händelser relaterade till att ladda upp en textfil till storage-konto. Eller, du kan filtrera ämnet börjar med `/blobServices/default/containers/testcontainer` att hämta alla händelser för den behållaren men inte andra behållare i lagringskontot.

När du publicerar händelser till anpassade ämnen, skapa ämnen för händelser som gör det enkelt för prenumeranter att veta om de är intresserad av händelsen. Prenumeranter använda egenskapen ämne på filtret och dirigera händelser. Överväg att lägga till sökvägen för där händelsen har inträffat, så prenumeranter kan filtrera efter segment i sökvägen. Sökvägen kan prenumeranter att snävare eller brett Filtrera händelser. Om du anger en sökväg för tre segment som `/A/B/C` på ämnesraden prenumeranter kan filtrera efter det första segmentet `/A` att hämta en rad olika händelser. Dessa prenumeranter får händelser med ämnen som `/A/B/C` eller `/A/D/E`. Andra prenumeranter kan filtrera efter `/A/B` att hämta en smalare uppsättning händelser.

JSON-syntaxen för att filtrera efter händelsetyp är:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Avancerad filtrering

Om du vill filtrera efter värden i fälten och anger jämförelseoperatorn, använder du alternativet för avancerad filtrering. I avancerad filtrering, anger du den:

* typ av frågeoperator - typ av jämförelse.
* nyckel - fältet i informationen om händelsen som du använder för filtrering. Det kan vara ett tal, ett booleskt värde eller en sträng.
* värdet eller värdena - värdet eller värdena ska jämföras med nyckeln.

JSON-syntaxen för att använda avancerade filter är:

```json
"filter": {
  "advancedFilters": [
    {
      "operatorType": "NumberGreaterThanOrEquals",
      "key": "Data.Key1",
      "value": 5
    },
    {
      "operatorType": "StringContains",
      "key": "Subject",
      "values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>Operator

Tillgängliga operatörer för tal är:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

Operatorn tillgängliga för booleska värden är: BoolEquals

Tillgängliga operatörer för strängar är:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Alla strängjämförelser är fallet insensitve.

### <a name="key"></a>Nyckel

Använd följande värden för händelser i Event Grid-schemat för nyckeln:

* Id
* Avsnitt
* Subjekt
* Händelsetyp
* DataVersion
* Händelsedata (till exempel Data.key1)

Använd följande värden för händelser i molnet händelseschemat för nyckeln:

* EventId
* Källa
* Händelsetyp
* EventTypeVersion
* Händelsedata (till exempel Data.key1)

Använda datafälten som händelse (till exempel Data.key1) för anpassade inmatningsschemat.

### <a name="values"></a>Värden

Värdena kan vara:

* nummer
* sträng
* boolesk
* matris

### <a name="limitations"></a>Begränsningar

Avancerade filter har följande begränsningar:

* Fem avancerade filter per event grid-prenumeration
* 512 tecken per strängvärde
* Fem värden för **i** och **inte i** operatorer
* Nyckeln kan bara ha en kapslingsnivå (till exempel data.key1)
* Anpassad Händelsescheman kan filtreras endast på översta fält

Samma nyckel kan användas i mer än ett filter.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du filtrerar händelser med PowerShell och Azure CLI i [Filtrera händelser för Event Grid](how-to-filter-events.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).
