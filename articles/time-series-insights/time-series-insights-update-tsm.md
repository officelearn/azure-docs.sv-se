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
ms.date: 11/30/2018
ms.openlocfilehash: 4cb83b61068922002c0c308f4d129a2e069beadd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856926"
---
# <a name="time-series-model"></a>Time Series-modell

Den här dokumentet beskriver de **Tidsseriemodell** (TSM) tillsammans med Azure Time Series Insights (TSI)-uppdateringen. Den beskriver modellens egen säkerhetsuppsättning, dess funktioner och hur du kommer igång att skapa och uppdatera din egen modell.

Traditionellt har saknar data som samlas in från IoT-enheter kontextinformation, vilket gör det svårt att hitta och analysera sensorer snabbt. Det huvudsakliga skälet till TSM är att förenkla söka efter och analysera IoT-data genom att aktivera hantering, underhåll och berikande av time series-data för att förbereda konsumenter datauppsättningar. **Time Series-modeller** spela en viktig roll i frågor och navigering eftersom de contextualize enhets- och icke-device-entiteter. Data sparas i TSM stänger time series frågor beräkningar genom att använda de formler som lagras i dem.

![TSM][1]

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med målet att göra det enkelt och enkel att hantera time series contextualization kan TSM följande funktioner i Azure Time Series Insights (förhandsversion):

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

## <a name="time-series-model-type-json-request-and-response-example"></a>Time Series-modell typ begäranden och svar exempel JSON

Får en HTTP POST-begäran:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/types/$batch?api-version=API_VERSION
```

| Namn | Beskrivning | Exempel |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Namnet på din miljö  | `environment123` |
| API_VERSION  |  API-specifikation | `2018-11-01-preview` |

Med följande JSON brödtext och variabeln attribut:

| Attribut | Obligatorisk eller valfri |
| --- | --- |
| **typ**  |  Krävs  |
| **Filter**  |  Valfri |
| **värde**  | Null eller har inte angetts  |
| **interpolering**  |  Valfri |
| **Sammansättning**  |  Krävs |

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleType",
            "description": "This is type 2",
            "variables": {
                "Avg Temperature": {
                    "kind": "numeric",
                    "filter": null,
                    "value": { "tsx": "$event.temperature.Double" },
                    "interpolation": "None",
                    "aggregation": {"tsx": "avg($value)"}
                },
                "Count Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "count()"}
                },
                "Min Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "min($event.temperature)"}
                },
            }
        }
    ]
}
```

Svar:

```JSON
{
    "get": null,
    "put": [
        {
            "timeSeriesType": {
                "id": "fc4f526c-da6e-4b85-87f7-16f6cf9b69be",
                "name": "type2",
                "description": "This is type 2",
                "variables": {
                    "Avg Temperature": {
                        "kind": "numeric",
                        "filter": null,
                        "value": { "tsx": "$event.temperature.Double" },
                        "interpolation": "None",
                        "aggregation": {"tsx": "avg($value)"}
                    },
                    "Count Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "count()"}
                    },
                    "Min Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "min($event.temperature)"}
                    }
                }
            },
            "error": null
        }
    ]
}
``````

En **standard** *typ* JSON-svar:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "someType1",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

## <a name="variables"></a>Variabler

Azure TSI-typer har variabler, är dessa namngivna beräkningar över värden från händelserna. TSI variabeln definitioner innehåller formeln och alla beräkningar som regler. Variabeln definitioner är typ, värde, filter, minskning och gränser. Variabler lagras i typdefinitionen i TSM och kan anges infogade Query-API: er att åsidosätta lagrade definitionen.

Matrisen nedan fungerar som en förklaring för variabeln definitioner:

![tabell][2]

### <a name="variable-kind"></a>Variabeln typ

Följande variabel typer stöds:

* Numeriska – kontinuerlig

### <a name="variable-filter"></a>Variabeln filter

Variabeln filter ange ett valfritt filtersats för att begränsa antalet rader som anses vara för beräkning, baserat på villkor.

### <a name="variable-value"></a>Variabelvärde

Variabelvärden är och ska användas i beräkningen. Detta är kolumnen i de händelser som vi ska referera till.

### <a name="variable-interpolation"></a>Variabeln interpolering

Att konvertera en uppsättning värden till ett värde per ett intervall som kallas för en variabel av. Variabeln sänkt kan vara sammanställda inspelade data från källan eller rekonstruerad signaler med interpolering och sammanställa eller rekonstruerade signaler med interpolering och samlar. Variabeln gränser kan läggas till interpolation, de här alternativen kan beräkningar för att inkludera händelser utanför sökintervallet.

Azure TSI (förhandsversion) har stöd för följande variabel interpolation: `linear`, `stepright`, och `none`.

### <a name="variable-aggregation"></a>Variabeln aggregering

Mängdfunktionen variabeln kan en del av beräkning. Om variabeln interpolering är `null` eller `none`, och sedan TSI stöder vanliga aggregeringar (nämligen **min**, **max**, **genomsnittlig**, **summan** , och **antal**). Om variabeln interpolering är `stepright` eller `linear`, och sedan TSI stöder **twmin**, **högsta temperatur vid drift**, **twavg**, och **twsum**. Antal kan inte anges i interpolation.

## <a name="time-series-model-hierarchies"></a>Time Series-modell hierarkier

Hierarkier organisera instanser genom att ange egenskapsnamn och deras relationer. Du kan ha en enda hierarki eller flera hierarkier. Dessutom kan de behöver inte vara en aktuella del av dina data, men varje instans ska mappa till en hierarki. En TSM-instans kan mappa till en enda hierarki eller flera hierarkier.

Hierarkier har definierats av **hierarki-ID**, **namn**, och **källa**. Hierarkier har sökvägar, en sökväg är uppifrån och ned överordnad-underordnad ordning i hierarkin som användaren vill skapa. Egenskaper för överordnade/underordnade mappar instansfält.

### <a name="time-series-model-hierarchy-json-request-and-response-example"></a>Time Series-modell hierarki begäranden och svar exempel JSON

Får en HTTP POST-begäran:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/hierarchies/$batch?api-version=API_VERSION
```

| Namn | Beskrivning | Exempel |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Namnet på din miljö  | `environment123` |
| API_VERSION  |  API-specifikation | `2018-11-01-preview` |

Med JSON-texten:

```JSON
{
    "get": null,
    "put": [
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
    ]
}
```

Svar:

```JSON
{
    "get": null,
    "put": [
        {
            "hierarchy": {
                "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
                "name": "Location",
                "source": {
                    "instanceFieldNames": [
                        "state",
                        "city"
                    ]
                }
            },
            "error": null
        }
    ]
}
```

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

Instanser är tidsserier själva. I de flesta fall det här är den *deviceId* eller *assetId* som är den unika identifieraren för tillgången i miljön. Instanser har beskrivande information som är kopplade till dem kallas instansegenskaper. Instansegenskaper för omfattar minst hierarkiinformation. De kan också innehålla användbar och beskrivande data som tillverkaren, operatorn och det sista datumet i tjänsten.

Instanser definieras av *timeSeriesId*, *typeId*, *hierarchyId*, och *instanceFields*. Varje instans som mappar till endast en *typ*, och en eller flera hierarkier. Instanser ärver alla egenskaper från hierarkier, medan ytterligare *instanceFields* kan läggas till för ytterligare instans egenskapsdefinition.

*instanceFields* är egenskaper för en instans och statiska data som definierar en instans. De definierar värdena för egenskaperna för hierarkin eller -hierarki också stöd för indexering för att utföra sökningar.

## <a name="time-series-model-instance-json-request-and-response-example"></a>Time Series-modell instans begäranden och svar exempel JSON

Får en HTTP POST-begäran:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/instances/$batch?api-version=API_VERSION
```

| Namn | Beskrivning | Exempel |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Namnet på din miljö  | `environment123` |
| API_VERSION  |  API-specifikation | `2018-11-01-preview` |

Med JSON-texten:

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleName",
            "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
            "timeSeriesId": [
                "samplePartitionKeyValueOne"
            ],
            "description": "floor 100",
            "hierarchyIds": [
                "e37a4666-9650-42e6-a6d2-788f12d11158"
            ],
            "instanceFields": {
                "state": "California",
                "city": "Los Angeles"
            }
        }
    ]
}
```

Svar:

```JSON
{
    "get": null,
    "put": [
        {
            "instance": null,
            "error": null
        },
        {
            "instance": null,
            "error": null
        }
    ]
}
```

## <a name="time-series-model-settings-example"></a>Exempel på inställningar på Time Series-modell

Får en HTTP POST-begäran:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/modelSettings?api-version=API_VERSION
```

| Namn | Beskrivning | Exempel |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Namnet på din miljö  | `environment123` |
| API_VERSION  |  API-specifikation | `2018-11-01-preview` |

Svar:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "someType1",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

## <a name="time-series-model-limits"></a>Tidsfrister Series-modell

| Parameter |   Begränsningar |
| --- | --- |
| Objektstorlek för Service-entiteter (typer, hierarkier och instanser)|  32 KB innehåller egenskaper |
| Nycklar tillåts som TSID-egenskap som konfigurerats via Azure-portalen |   Max 3 |
| Max antal typer i en miljö |    1000|
| Max antal variabler i en typ |    50|
| Max antal hierarkier i en miljö|   32|
| Max hierarki djup | 32|
| Max antal hierarkier som är associerad med 1 instans   |21|
| Max antal instanser i en miljö |    500,000|
| Max antal instansfält per instans |   50|
| Modellera objekt upsert/Uppdatera/ta bort åtgärden per sekund   |100 per sekund|
| Time Series modellera API-begäran storlek: Batch |  8 MB eller 1000 modellobjekt (beroende på vilket som inträffar först)|
| Time Series-modell begärandestorlek: Sök/föreslå   | 32 KB|
| Time Series modellera API-begäran storlek: Batch    | 32 MB|
| Sök/föreslå är 32 MB|  32 MB|

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Nästa steg

Läs den [Azure TSI (förhandsversion) lagring och Ingångsanspråk](./time-series-insights-update-storage-ingress.md).

Läs mer om den nya [Tidsseriemodell](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
