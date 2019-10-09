---
title: Tids serie modell i Azure Time Series Insights för hands version | Microsoft Docs
description: Förstå Azure Time Series Insights tids serie modell.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 8f4ed6de5ff47efa441c371b80670c500f57c9bb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034059"
---
# <a name="time-series-model"></a>Time Series-modell

I den här artikeln beskrivs tids serie modell delen av Azure Time Series Insights för hands version. Den diskuterar själva modellen, dess funktioner och hur du kommer igång med att skapa och uppdatera din egen modell.

Traditionellt har data som samlas in från IoT-enheter saknar sammanhangsbaserad information, vilket gör det svårt att hitta och analysera sensorer snabbt. Huvud motivation för Time Series-modellen är att förenkla sökning och analys av IoT-data. Den uppnår detta mål genom att aktivera hantering, underhåll och anrikning av Time Series-data för att förbereda konsument klara data uppsättningar.

Tids serie modeller spelar en viktig roll i frågor och navigering eftersom de sätta enhets-och entiteter som inte är enheter. Data som bevaras i Time Series-modellen har en tids serie fråga beräkningar genom att dra nytta av de formler som lagras i dem.

[Översikt över modell för @no__t 1Time-serien](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med målet att göra det enkelt och enkelt att hantera Time Series-contextualization, aktiverar Time Series-modellen följande funktioner i Time Series Insights Preview. Det hjälper dig att:

* Redigera och hantera beräkningar eller formler, transformera data med skalära funktioner, mängd åtgärder och så vidare.
* Definiera överordnad-underordnad-relationer för att aktivera navigering och referens och tillhandahålla sammanhang till telemetri för tids serier.
* Definiera egenskaper som är associerade med instans delen av *instans fälten* och Använd dem för att skapa hierarkier.

## <a name="entity-components"></a>Enhets komponenter

Tids serie modeller har tre huvud komponenter:

* <a href="#time-series-model-types">Modell typer för tids serier</a>
* <a href="#time-series-model-hierarchies">Hierarkier för tids serie modell</a>
* <a href="#time-series-model-instances">Tids serie modell instanser</a>

Dessa komponenter kombineras för att ange en tids serie modell och för att organisera dina Azure Time Series Insights data.

## <a name="time-series-model-types"></a>Modell typer för tids serier

Med modell *typer* för tids serier kan du definiera variabler eller formler för att utföra beräkningar. Typerna är associerade med en angiven Time Series Insights-instans. En typ kan ha en eller flera variabler. Till exempel kan en Time Series Insights instans vara av typen *temperatur sensor*, som består av variablerna *genomsnittlig temperatur*, *min temperatur*och *Maximal temperatur*. Vi skapar en standard typ när data börjar flöda in i Time Series Insights. Standard typen kan hämtas och uppdateras från modell inställningar. Standard typerna har en variabel som räknar antalet händelser.

### <a name="time-series-model-type-json-example"></a>Exempel på en tids serie modell typ JSON

Exempel:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

Mer information om modell typer för tids serier finns i [referens dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>Variabler

Time Series Insights typer har variabler, som är namngivna beräkningar över värden från händelserna. Time Series Insights variabel definitioner innehåller formel-och beräknings regler. Variabel definitioner är *typ*, *värde*, *filter*, *reducering*och *gränser*. Variabler lagras i typ definitionen i tids serie modellen och kan anges infogade via fråge-API: er för att åsidosätta den lagrade definitionen.

Följande matris fungerar som en förklaring för variabel definitioner:

[variabel definitions tabell för @no__t 1Type](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definition | Beskrivning |
| --- | ---|
| Variabel typ |  *Numeriska* och *aggregerade* typer stöds |
| Variabel filter | Variabel filter anger en valfri filter-sats för att begränsa antalet rader som ska beaktas för beräkning baserat på villkor. |
| Variabel värde | Variabel värden är och ska användas i beräkningen. Det relevanta fältet för att referera till för den aktuella data punkten. |
| Variabel agg regering | Mängd funktionen i variabeln möjliggör en del av beräkningen. Time Series Insights stöder reguljära mängder (dvs. *min*, *Max*, *AVG*, *Sum*och *Count*). |

## <a name="time-series-model-hierarchies"></a>Hierarkier för tids serie modell

Hierarkier organiserar instanser genom att ange egenskaps namn och deras relationer. Du kan ha en enda hierarki eller flera hierarkier. De behöver inte vara en aktuell del av dina data, men varje instans bör mappas till en hierarki. En tids serie modell instans kan mappas till en enskild hierarki eller flera hierarkier.

Hierarkier definieras av *hierarki-ID*, *namn*och *källa*. Hierarkier har en sökväg, som är en överordnad över-och underordnad-ordning i hierarkin som användare vill skapa. De överordnade-underordnade egenskaperna mappar *instans fält*.

### <a name="time-series-model-hierarchy-json-example"></a>JSON-exempel för Time Series Model-hierarki

Exempel:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

Mer information om hierarkier för Time Series-modeller finns i [referens dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Definitions beteende för hierarki

Tänk på följande exempel där hierarkin H1 har *byggnad*, *golv*och *rum* som en del av dess definition:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Beroende på *instans fälten*visas hierarkins attribut och värden som visas i följande tabell:

| Time Series-ID | Instans fält |
| --- | --- |
| ID1 | "skapa" = "1000", "golv" = "10", "rum" = "55"  |
| ID2 | "skapa" = "1000", "Room" = "55" |
| ID3 | "golv" = "10" |
| ID4 | "skapa" = "1000", "golv" = "10"  |
| ID5 | Ingen av "byggnad", "golv" eller "rum" har angetts |

I föregående exempel visas **id1** och **ID4** som en del av hierarkin H1 i Azure Time Series Insights Explorer och resten klassificeras under icke- *överordnade instanser* eftersom de inte överensstämmer med den angivna Datahierarkin.

## <a name="time-series-model-instances"></a>Tids serie modell instanser

Instanser är själva tids serien. I de flesta fall är *deviceId* eller *assetId* den unika identifieraren för till gången i miljön. Instanser har beskrivande information som är kopplad till dem som kallas instans egenskaper. Som minst innehåller instans egenskaper hierarki-information. De kan också innehålla användbara, beskrivande data som tillverkare, operatör eller senaste tjänste dag.

Instanser definieras av *typeId*, *timeSeriesId*, *Name*, *Description*, *hierarchyIds*och *instanceFields*. Varje instans mappar endast till en *typ*, och en eller flera hierarkier. Instanser ärver alla egenskaper från hierarkier och ytterligare *instanceFields* kan läggas till för definition av ytterligare instans egenskaper.

*instanceFields* är egenskaper för en instans och alla statiska data som definierar en instans. De definierar värden för hierarki-eller icke-hierarkiska egenskaper och stöder även indexering för att utföra Sök åtgärder.

Egenskapen *Name* är valfri och Skift läges känslig. Om *namnet* inte är tillgängligt kommer standardvärdet för Time Series-ID: t. Om ett *namn* anges är Time Series-ID fortfarande tillgängligt i det här (rutnätet under diagrammen i Utforskaren).

### <a name="time-series-model-instance-json-example"></a>Instans-JSON-exempel för Time Series-modell

Exempel:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Mer information om tids serie modell instanser finns i [referens dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Exempel på Inställningar för tids serie modell

Exempel:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Mer information om inställningar för tids serie modell finns i [referens dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Nästa steg

- Se [Azure Time Series Insights för hands versions lagring och inkommande](./time-series-insights-update-storage-ingress.md)trafik.

- Se den nya [tids serie modellen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).