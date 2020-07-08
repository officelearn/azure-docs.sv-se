---
title: Lägger till stöd för lång data typ | Microsoft Docs
description: Stöd för långa data typer
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: dpalled
ms.openlocfilehash: ebb62b67b56134902f2752b43dd25fb0a7c6ccd4
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045781"
---
# <a name="adding-support-for-long-data-type"></a>Lägger till stöd för lång data typ

De här ändringarna kommer endast att tillämpas på för hands versions miljöer (PAYG). Om du har en standard miljö för SKU, kan du bortse från dessa ändringar.

Vi gör ändringar i hur vi lagrar och indexerar numeriska data i Azure Time Series Insights för hands version som kan påverka dig. Om du påverkas av något av fallen nedan gör du nödvändiga ändringar så snart som möjligt. Dina data kommer att börja indexeras som långa och dubblas mellan 29 juni och 30 juni 2020, beroende på din region. Om du har några frågor eller problem med den här ändringen kan du skicka ett support ärende via Azure Portal och nämna denna kommunikation.

Den här ändringen påverkar dig i följande fall:

1. Om du för närvarande använder modell variabler för tids serier och bara skickar heltals data typer i dina telemetridata.
1. Om du för närvarande använder modell variabler för tids serier och skickar data typerna integral och data som inte är Integral i dina telemetridata.
1. Om du använder kategoriska-variabler för att mappa heltals värden till kategorier.
1. Om du använder Java Script SDK för att bygga ett anpassat klient program.
1. Om du närmar dig gränsen för 1 000-egenskaps namn i varmt lagrings utrymme (WS) och skickar både heltals-och data som inte är Integral kan du Visa antalet egenskaper som ett mått i [Azure Portal](https://portal.azure.com/).

Om någon av ovanstående fall gäller för dig måste du göra ändringar i din modell för att kunna hantera den här ändringen. Uppdatera Time Series-uttrycket i din variabel definition i både TSD Explorer och i en anpassad klient med hjälp av våra API: er med de rekommenderade ändringarna. Se nedan för mer information.

Beroende på din IoT-lösning och begränsningar kanske du inte har insyn i de data som skickas till din TSD PAYG-miljö. Om du är osäker på om dina data endast är integrala eller både integrala och inintegrala har du några alternativ. Du kan vänta tills funktionen släpps och utforska dina obehandlade händelser i användar gränssnittet i Utforskaren för att förstå vilka egenskaper som har sparats i två separata kolumner. Du kan förebyggande syfte göra ändringarna nedan för alla numeriska taggar eller tillfälligt dirigera en delmängd av händelser till lagring för att bättre förstå och utforska ditt schema. Om du vill lagra händelser aktiverar du [händelse fångsten](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) för Event Hubs eller [dirigerar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#azure-storage) från IoT Hub till Azure Blob Storage. Data kan också observeras via [Event Hub Explorer](https://marketplace.visualstudio.com/items?itemName=Summer.azure-event-hub-explorer)eller med hjälp av [händelse bearbetnings värden](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send#receive-events). Om du använder IoT Hub kan du läsa mer [i dokumentationen om](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) hur du kommer åt den inbyggda slut punkten.

Observera att om du påverkas av dessa ändringar och inte kan göra dem med ovanstående datum, kan det uppstå ett avbrott där de inaktuella tidsserie-variablerna som nås via fråge-API: erna eller Time Series Insights Explorer kommer att returnera *Null* (d.v.s. Visa inga data i Utforskaren).

## <a name="recommended-changes"></a>Rekommenderade ändringar

Fall 1 & 2: **använda variabler för tids serie modeller och skicka endast heltals data typer eller skicka både integrala och inintegrala typer i telemetridata.**

Om du för närvarande skickar språktelemetri-data delas dina data in i två kolumner: "propertyValue_double" och "propertyValue_long".

Dina heltals data skrivs till "propertyValue_long" när ändringarna börjar gälla och sedan tidigare skrevs in (och framtida inmatade) numeriska data i "propertyValue_double" inte kopieras över.

Om du vill fråga efter data över de här två kolumnerna för egenskapen "propertyValue" måste du använda den skalära funktionen *sammanslagning ()* i din TSX. Funktionen accepterar argument av samma datatyp och returnerar det första värdet som inte är null i argument listan (Läs mer om användning [här](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions)).

### <a name="variable-definition-in-time-series-explorer---numeric"></a>Variabel definition i Time Series Explorer – numerisk

*Definition av föregående variabel:*

[![Definition av föregående variabel](media/time-series-insights-long-data-type/var-def-previous.png)](media/time-series-insights-long-data-type/var-def-previous.png#lightbox)

*Ny variabel definition:*

[![Ny variabel definition](media/time-series-insights-long-data-type/var-def.png)](media/time-series-insights-long-data-type/var-def.png#lightbox)

Du kan också använda *"sammanslagning ($Event. propertyValue. Double, toDouble ($Event. propertyValue. Long))"* som ett anpassat [tids serie uttryck.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---numeric"></a>Infogad variabel definition med Time Series-frågas-API: n-numeriskt

*Definition av föregående variabel:*

    "PropertyValueVariable": {

        "kind": "numeric",

        "value": {

            "tsx": "$event.propertyValue.Double"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

*Ny variabel definition:*

    "PropertyValueVariable ": {

        "kind": "numeric",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, toLong($event.propertyValue.Double))"

        },

        "filter": null,

        "aggregation": {

            "tsx": "avg($value)"

Du kan också använda *"sammanslagning ($Event. propertyValue. Double, toDouble ($Event. propertyValue. Long))"* som ett anpassat [tids serie uttryck.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Vi rekommenderar att du uppdaterar dessa variabler på alla platser som de kan användas (tids serie modell, sparade frågor, Power BI anslutnings frågor).

Fall 3: **använda kategoriska-variabler för att mappa heltals värden till kategorier**

Om du för närvarande använder kategoriska-variabler som mappar heltals värden till kategorier, använder du förmodligen funktionen toLong för att konvertera data från Double-typ till en lång typ. Precis som i exemplen ovan måste du slå samman kolumnerna Double och Long types.

### <a name="variable-definition-in-time-series-explorer---categorical"></a>Variabel definition i Time Series Explorer – kategoriska

*Definition av föregående variabel:*

[![Definition av föregående variabel](media/time-series-insights-long-data-type/var-def-cat-previous.png)](media/time-series-insights-long-data-type/var-def-cat-previous.png#lightbox)

*Ny variabel definition:*

[![Ny variabel definition](media/time-series-insights-long-data-type/var-def-cat.png)](media/time-series-insights-long-data-type/var-def-cat.png#lightbox)

Du kan också använda *"sammanslagning ($Event. propertyValue. Double, toDouble ($Event. propertyValue. Long))"* som ett anpassat [tids serie uttryck.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

Kategoriska-variabler kräver fortfarande att värdet är av en heltals typ. Data typen för alla argument i sammanslagning () måste vara av typen Long i det anpassade [tids serie uttrycket.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="inline-variable-definition-using-time-series-query-apis---categorical"></a>Infogad variabel definition med Time Series-fråge-API: er – kategoriska

*Definition av föregående variabel:*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "tolong($event.propertyValue.Double)"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

*Ny variabel definition:*

    "PropertyValueVariable_Long": {

        "kind": "categorical",

        "value": {

            "tsx": "coalesce($event.propertyValue.Long, tolong($event.propertyValue.Double))"

        },

        "categories": [

        {
            "label": "Good",

            "values": [0, 1, 2 ]

        },

        {

            "label": "Bad",

            "values": [ 3, 4 ]

        } ],

        "defaultCategory": {

            "label": "Unknown"

        }

    }

Kategoriska-variabler kräver fortfarande att värdet är av en heltals typ. Data typen för alla argument i sammanslagning () måste vara av typen Long i det anpassade [tids serie uttrycket.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

> [!NOTE]
> Vi rekommenderar att du uppdaterar dessa variabler på alla platser som de kan användas (tids serie modell, sparade frågor, Power BI anslutnings frågor).

Fall 4: **använda Java Script SDK för att bygga ett anpassat klient program**

Om du påverkas av fall 1-3 ovan och skapar anpassade program, måste du uppdatera dina frågor för att använda funktionen *sammanslagning ()* , som visas i exemplen ovan.

Fall 5: **närmar sig det varmt arkivet 1 000 egenskaps gräns**

Om du är en användare av varma butiker med ett stort antal egenskaper och tror att den här ändringen skulle leda till att din miljö överförs över egenskapen för 1 000 WS-namn, skickar du ett support ärende via Azure Portal och nämner denna kommunikation.

## <a name="next-steps"></a>Nästa steg

* Se den [här](concepts-supported-data-types.md) artikeln för att visa en fullständig lista över data typer som stöds.
