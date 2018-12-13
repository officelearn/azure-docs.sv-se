---
title: Time Series-modell | Microsoft Docs
description: Förstå Time Series-modell
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 5d5f94aebcd55474385e903246ce7945586456dd
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890423"
---
# <a name="time-series-model"></a>Time Series-modell

Den här dokumentet beskriver de **Tidsseriemodell** (TSM) tillsammans med Azure Time Series Insights (TSI)-uppdateringen. Den beskriver modellens egen säkerhetsuppsättning, dess funktioner och hur du kommer igång att skapa och uppdatera din egen modell.

Traditionellt har saknar data som samlas in från IoT-enheter kontextinformation, vilket gör det svårt att hitta och analysera sensorer snabbt. Det huvudsakliga skälet till TSM är att förenkla söka efter och analysera IoT-data genom att aktivera hantering, underhåll och berikande av time series-data för att förbereda konsumenter datauppsättningar. TSMs spela en viktig roll i frågor och navigering eftersom de contextualize enhets- och icke-device-entiteter. Data sparas i TSM stänger time series frågor beräkningar genom att använda de formler som lagras i dem.

![TSM][1]

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med målet att göra det enkelt och enkel att hantera time series contextualization kan TSM följande funktioner i The Azure TSI (förhandsversion):

* Möjligheten att skapa och hantera beräkningar eller formler för att omvandla data att utnyttja skalärfunktioner, sammanställd operations osv.
* Definiera överordnade och underordnade objekt för att aktivera navigering och referens för att tillhandahålla kontexten till time series telemetri.
* Definierar egenskaper som är associerade med den instanser delen av instansfält och använda dessa för att skapa hierarkier.

## <a name="times-series-model-key-components"></a>Times Series-modell nyckelkomponenter

Det finns tre viktiga komponenter i TSM:

* **Time Series-modell** *typer*
* **Time Series-modell** *hierarkier*
* **Time Series-modell** *instanser*

## <a name="time-series-model-types"></a>Time Series-modell typer

**Time Series-modell** *typer* aktivera definiera variabler eller formler för att göra beräkningar och associeras med en viss TSI-instans. En typ kan ha en eller flera variabler. Till exempel en TSI-instans kan vara av typen **temperatursensor**, som består av variabler: *genomsnittlig temperatur*, *min temperatur*, och *max temperatur*. Vi skapar en standardtyp när data börjar flöda i TSD. Den kan hämtas och uppdateras från modellinställningarna. Standardtyperna har en variabel som räknar antalet händelser.

## <a name="time-series-model-type-json-example"></a>Time Series-modell typen JSON-exempel

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
``````

Läs mer om Tidsseriemodell typer från den [referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

## <a name="variables"></a>Variabler

Azure TSI-typer har variabler, som är namngivna beräkningar över värden från händelserna. TSI variabeln definitioner innehåller formeln och alla beräkningar som regler. Variabeln definitioner är typ, värde, filter, minskning och gränser. Variabler lagras i typdefinitionen i TSM och kan anges infogade Query-API: er att åsidosätta lagrade definitionen.

Matrisen nedan fungerar som en förklaring för variabeln definitioner:

![tabell][2]

### <a name="variable-kind"></a>Variabeln typ

Följande variabel typer stöds:

* Numerisk
* Aggregera

### <a name="variable-filter"></a>Variabeln filter

Variabeln filter ange ett valfritt filtersats för att begränsa antalet rader som anses vara för beräkning, baserat på villkor.

### <a name="variable-value"></a>Variabelvärde

Variabelvärden är och ska användas i beräkningen. Detta är kolumnen i de händelser som vi ska referera till.

### <a name="variable-aggregation"></a>Variabeln aggregering

Mängdfunktionen variabeln kan en del av beräkning. TSI stöder vanliga aggregeringar (nämligen **min**, **max**, **genomsnittlig**, **summan**, och **antal**).

## <a name="time-series-model-hierarchies"></a>Time Series-modell hierarkier

Hierarkier organisera instanser genom att ange egenskapsnamn och deras relationer. Du kan ha en enda hierarki eller flera hierarkier. Dessutom kan de behöver inte vara en aktuella del av dina data, men varje instans ska mappa till en hierarki. En TSM-instans kan mappa till en enda hierarki eller flera hierarkier.

Hierarkier har definierats av **hierarki-ID**, **namn**, och **källa**. Hierarkier har sökvägar, en sökväg är uppifrån och ned överordnad-underordnad ordning i hierarkin som användaren vill skapa. Egenskaper för överordnade/underordnade mappar instansfält.

### <a name="time-series-model-hierarchy-json-example"></a>Time Series-modell hierarki JSON-exempel

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

Läs mer om Tidsseriemodell hierarkier från den [referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Hierarkin definition beteende

Överväg följande exempel där hierarki H1 har ”skapa”, ”våning” och ”plats” som en del av dess definition:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Beroende på fälten instans, hierarki-attribut och värden visas visas: 

| Time Series-ID | Instansfält |
| --- | --- |
| ID1 | ”Skapa” = ”1000”, ”våning” = ”10”, ”plats” = ”55”  |
| ID2 | ”Skapa” = ”1000”, ”plats” = ”55” |
| ID3 |  ”våning” = ”10” |
| ID4 | ”Skapa” = ”1000”, ”våning” = ”10”  |
| ID5 | |

I ovanstående exempel ID1 visas som en del av hierarkin H1 i Användargränssnittet/UX-Gränssnittet, medan resten klassificeras enligt `Unparented Instances` eftersom de inte överensstämmer med angiven data-hierarkin.

## <a name="time-series-model-instances"></a>Time Series-modell instanser

Instanser är tidsserier själva. I de flesta fall den *deviceId* eller *assetId* blir den unika identifieraren för tillgången i miljön. Instanser har beskrivande information som är kopplade till dem kallas instansegenskaper. Instansegenskaper för omfattar minst hierarkiinformation. De kan också innehålla användbar och beskrivande data som tillverkaren, operatorn och det sista datumet i tjänsten.

Instanser definieras av *timeSeriesId*, *typeId*, *hierarchyId*, och *instanceFields*. Varje instans som mappar till endast en *typ*, och en eller flera hierarkier. Instanser ärver alla egenskaper från hierarkier, medan ytterligare *instanceFields* kan läggas till för ytterligare instans egenskapsdefinition.

*instanceFields* är egenskaper för en instans och statiska data som definierar en instans. De definierar värdena för egenskaperna för hierarkin eller -hierarki också stöd för indexering för att utföra sökningar.

## <a name="time-series-model-instance-json-example"></a>Time Series-modell instans JSON-exempel

Exempel:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
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

Läs mer om Tidsseriemodell instanser från den [referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-settings-example"></a>Exempel på inställningar på Time Series-modell

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

Läs mer om Tidsseriemodell inställningar från den [referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Nästa steg

Läs den [Azure TSI (förhandsversion) lagring och ingående](./time-series-insights-update-storage-ingress.md).

Läs den om den nya [tidsseriemodell](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
