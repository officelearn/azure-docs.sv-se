---
title: Time Series-fråga | Microsoft Docs
description: Axure Time Series Insights (förhandsversion) Time Series-fråga
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: f5370d354833e9507d0794d7bc407a7ea2294e1a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856942"
---
# <a name="time-series-query"></a>Time Series-fråga

**Time Series fråga** (TSQ) gör det enklare att beräkna och hämta Time Series-data som lagras i Time Series Insights (TSI) i stor skala. TSQ utnyttjar databaserad definitioner för att omvandla och hämta data från storage. Detta sker från **Time Series modeller** (TSM) eller via infogad variabel definitioner.

TSQ hämtar data på två olika sätt. TSQ kan hämta data eftersom det registreras från käll-providern eller kan minska datamängden, eller kan rekonstruera signaler som utnyttjar den angivna metoden så att kunder utföra åtgärder för att omvandla, kombinera och utföra beräkningar på time series-data.

Time Series Insights-Data kan nås via den inbyggda TSI-uppdateringen Explorer eller Surface offentliga API: et. TSQ erbjuder också ett Uttrycksspråk så att du kan skapa egna mer avancerade TSI-frågor. Nedan visas våra mål med TSQ:

![Målet][1]

## <a name="core-apis"></a>Core API: er

Nedan visas core API: er som vi har stöd för.

![tsq][2]

### <a name="getmodelsettings-api"></a>getModelSettings API

GetModelSettings-API används för att returnera den automatiskt skapade modellen för Partitionsnyckeln miljöer.

GetModelSettings API använder följande parametrar:

* *namn på*: namnet på den modell som du vill hämta.
* *timeSeriesIdProperties*

  * *Namn*
  * *typ*

* *defaultTypeID*

#### <a name="getmodelsettings-json-request-and-response-example"></a>getModelSettings JSON begäranden och svar-exempel

Får en hämta HTTP-begäran:

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

### <a name="gettypes-api"></a>getTypes API

GetTypes API returnerar alla Time Series-typer och deras associerade variabler för modellen.

GetTypes API använder följande parametrar:

* *typeId*: oföränderligt Unik identifierare för typen.
* *timeSeriesId*: den **Time Series-ID** är den unika nyckeln för data i händelseströmmen och modell. Den här nyckeln är vad TSI använder för att partitionera data.
* *Beskrivning av*: beskrivning för.
* *hierarchyIds*: en matris med associerade hierarchyIds för typen.
* *instanceFields*:
  * *tillstånd*
  * *Stad*

#### <a name="gettypes-json-request-and-response-example"></a>getTypes JSON begäranden och svar-exempel

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
```

### <a name="gethierarchies-api"></a>getHierarchies API

GetHierarchies API returnerar alla Time Series-hierarkier och alla deras associerade fältet sökvägar.

GetHierarchies API använder följande parametrar:

* *ID*: nyckeln unikt ID för hierarkin.
* *namn på*: namnet på hierarkin
* *Källa*
* *instanceFields*
  * *År*
  * *Månad*

#### <a name="gethierarchies-json-request-and-response-example"></a>getHierarchies JSON begäranden och svar-exempel

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

### <a name="getinstances-api"></a>getInstances API

GetInstances API används för att returnera alla Time Series-instanser och deras associerade instansfält.

GetInstances API använder följande parametrar:

* *namn på*: namnet som associeras med den instans som används för att fråga och Ersätt i UX.
* *typeId*: nyckeln unikt ID för typen.
* *timeSeriesId*: den **Time Series-ID** är den unika nyckeln för data i händelseströmmen och modell. Den här nyckeln är vad TSI använder för att partitionera data.
* *Beskrivning av*: beskrivning av instansen.
* *hierarchyIds*: en matris med en eller flera av hierarchyIds som unikt definierar varje hierarki.
* *instanceFields*:
  * *tillstånd*
  * *Stad*

#### <a name="getinstances-json-request-and-response-example"></a>getInstances JSON begäranden och svar-exempel

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

### <a name="aggregateseries-api"></a>aggregateSeries API

AggregateSeries API kan fråga och hämtning av TSI data från avbildade händelserna genom att sampla och sammanställa registreras data med hjälp av funktionerna sammanställd eller exempeldata.

AggregateSeries API använder följande parametrar:

* *timeSeriesId*: den **Time Series-ID** är den unika nyckeln för data i händelseströmmen och modell. Den här nyckeln är vad TSI använder för att partitionera data.
* *searchSpan*: timespan- och bucket-storleken för den här mängduttryck.
* *Filtret*: valfria predikat-satsen.
* *Intervall*: intervall då data ska beräknas.
* *ProjectedVariables*: variabler som omfattas ska beräknas.
* *InlineVariables(optional)*: definitioner av variabler som vi vill på antingen åsidosätta från den kommande via TSM eller angivna infogade för beräkning.

GetSeries API: et returnerar en TSV för varje variabel för varje intervall, baserat på de angivna **Time Series-ID** och en uppsättning angivna variabler. GetSeries API uppnår minskning genom att använda variabler lagras i TSM eller tillhandahålls infogade aggregate- eller exemplet data.

> [!NOTE]
> Variabeln interpolering stöds inte för närvarande.

Aggregeringstyper som stöds:

* `Min`
* `Max`
* `Sum`
* `Count`
* `Average`

#### <a name="aggregateseries-json-request-and-response-example"></a>aggregateSeries JSON begäranden och svar-exempel

Får en HTTP POST-begäran:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Namn | Beskrivning | Exempel |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Namnet på din miljö  | `environment123` |
| API_VERSION  |  API-specifikation | `2018-11-01-preview` |

Med JSON-texten:

```JSON
{
    "aggregateSeries": {
        "timeSeriesId": ["da2754af-cc51-46b3-b18f-6231f8781a12","PU.98"],
        "searchSpan": {
            "from": {"dateTime": "2016-08-01T00:00:00Z"},
            "to": {"dateTime": "2016-08-01T00:10:00Z"}
        },
        //Optional
        //If provided, Reduction should be specified in Variable Definition
        "interval": "PT1M",
        "filter": null, //Optional
        "projectedVariables": [
            "Count",
            "Average Temperature",
            "Min Temperature"
        ],
        "inlineVariables": {
            "Average Temperature": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.temperature.Double" },
                "interpolation": "None",
                "aggregation": {"tsx": "avg($value)"}
            },
            //Default Type Count Experience
            "Count": {
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
}
```

Svar:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:00Z","2016-08-01T00:02:00Z","2016-08-01T00:03:00Z","2016-08-01T00:04:00Z",
        "2016-08-01T00:05:00Z","2016-08-01T00:06:00Z","2016-08-01T00:07:00Z","2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Average Temperature",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316]
        },
        {
            "name": "Count Temperature",
            "type": "Double",
            "values": [ 60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0 ]
        },
        {
            "name": "Min Temperature",
            "type": "Double",
            "values": [ 68.645954260192127,68.790954260192578,68.990954260193249,69.190954260193919,69.39095426019459,69.590954260195261,69.790954260195932,69.9909542601966,70.190954260197273,70.390954260197944 ]
        }
    ]
}
```

### <a name="getseries-api"></a>getSeries API

GetSeries API kan fråga och hämtning av TSI data från avbildade händelserna med hjälp av data som registrerats på kabeln med hjälp av variablerna definierar i modell eller infogade som angavs.

> [!Note]
> Om interpolation och aggregering-sats har angetts i variabeln, eller intervallet anges, ignoreras den.

GetSeries API använder följande parametrar:

* *timeSeriesId*: den **Time Series-ID** är den unika nyckeln för data i händelseströmmen och modell. Den här nyckeln är vad TSI använder för att partitionera data.
* *searchSpan*: timespan- och bucket-storleken för den här mängduttryck.
* *Filtret*: valfria predikat-satsen.
* *ProjectedVariables*: variabler som omfattas ska beräknas.
* *InlineVariables(optional)*: definitioner av variabler som vi vill på antingen åsidosätta från den kommande via TSM eller angivna infogade för beräkning.

GetSeries API: et returnerar en TSV för varje variabel, baserat på de angivna **Time Series-ID** och en uppsättning angivna variabler. GetSeries API uppnår stöder inte intervall eller variabel minskning/interpolation.  

#### <a name="getseries-json-request-and-response-example"></a>getSeries JSON begäranden och svar-exempel

Får en HTTP POST-begäran:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Namn | Beskrivning | Exempel |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Namnet på din miljö  | `environment123` |
| API_VERSION  |  API-specifikation | `2018-11-01-preview` |

Med JSON-texten:

```JSON
{
    "getSeries": {
        "timeSeriesId": ["da2754af-cc51-46b3-b18f-6231f8781a12","PU.98"],
        "searchSpan": {
            "from": {"dateTime": "2016-08-01T00:00:00Z"},
            "to": {"dateTime": "2016-08-01T00:10:00Z"}
        },
        "interval": null, //Null or not specified
        "filter": null, //Optional
        "projectedVariables": [
            "Temperature",
            "Pressure",
        ],
        "inlineVariables": {
            "Temperature": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.temperature.Double" },
                "interpolation": "None", // null, not specified or ignored if specified
                "aggregation": {"tsx": "avg($value)"} //null, not specified or ignored if specified
            },
            "Pressure": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.pressure.Double" },
                "interpolation": "None", // null, not specified or ignored if specified
                "aggregation": {"tsx": "avg($value)"} //null, not specified or ignored if specified
            }
        }
    }
}
```

Svar:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:00Z","2016-08-01T00:02:00Z","2016-08-01T00:03:00Z","2016-08-01T00:04:00Z",
        "2016-08-01T00:05:00Z","2016-08-01T00:06:00Z","2016-08-01T00:07:00Z","2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Temperature",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316 ]
        },
        {
            "name": "Pressure",
            "type": "Double",
            "values": [ 68.645954260192127,68.790954260192578,68.990954260193249,69.190954260193919,69.39095426019459,69.590954260195261,69.790954260195932,69.9909542601966,70.190954260197273,70.390954260197944 ]
        }
    ]
}
```

### <a name="getevents-api"></a>getEvents API

GetEvents API kan fråga och hämtning av TSI data från händelser som de är registrerade i Time Series Insights från käll-providern.

GetEvents API använder följande parametrar:

* *timeSeriesId*: den **Time Series-ID** är den unika nyckeln för data i händelseströmmen och modell. Den här nyckeln är vad TSI använder för att partitionera data.
* *searchSpan*: timespan- och bucket-storleken för den här mängduttryck.
* *filtret*: låter dig ange predikat värden för att filtrera dina frågor, men du önskar.
* *(valfritt) projectedProperties*: gör att kolumnen/egenskaper för filtrering.

GetEvents API: et returnerar rå värden från avbildade händelserna som lagras i TSI för en viss **Time Series-ID** och tidsintervall. Det kräver inte variabeln definitioner (varken TSM eller variabel definitioner används).

### <a name="getevents-json-request-and-response-example"></a>getEvents JSON begäranden och svar-exempel

Får en HTTP POST-begäran:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Namn | Beskrivning | Exempel |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  Namnet på din miljö  | `environment123` |
| API_VERSION  |  API-specifikation | `2018-11-01-preview` |

Med JSON-texten:

```JSON
{
    "getEvents": {
        // Required and Supports 3 Key as tsId
        "timeSeriesId": [
            "PU.123","W00158","ABN.9890"
        ],
        // Required.
        "searchSpan": {
            "from": "2018-08-01T17:01:00Z",
            "to": "2018-08-20T17:01:00Z",
        },
        // Optional.
        "filter": {
            "tsx": "($event.Value != null) OR ($event.Status.String == 'Good')"
        },
        // Optional – array of simple TSXs that refer exactly one property
        // If not specified, we will return all the properties.
        "projectedProperties": [ 
            {"propertyName" : "Volts", "type": "double" } 
        ]
    }
}
```

Svar:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:03Z","2016-08-01T00:02:05Z","2016-08-01T00:03:00Z",
        "2016-08-01T00:04:00Z","2016-08-01T00:05:05Z","2016-08-01T00:06:00Z","2016-08-01T00:07:08Z",
        "2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Volts",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316 ]
        }
    ]
}
```

### <a name="supported-query-operators"></a>Frågeoperatorer som stöds

Filtrering:

* `HAS`
* `IN`
* `AND`
* `AND NOT`
* `OR`
* `>`
* `<`
* `<=`
* `>=`
* `!=`
* `<>`
* `/`
* `*`

Temporala:

* `From`
* `To`
* `First/Last` (API)

Sammansättningsomvandling:

* `MEASURE`
* `SUM` (för ett mått)
* `COUNT` (händelser)
* `AVG` (för ett mått)
* `MIN` (för ett mått)
* `MAX` (för ett mått) ”
* `GROUP BY` (kategoriska kolumn)
* `ORDER BY ASC, DSC` (i förhållande till tidsstämpel)
* `DateHistogram` (bucket-storleken)

## <a name="tsq-api-limits"></a>TSQ API-gränser

> [!NOTE]
> Tabellen nedan anger gränser med **11/20/18**.

| Parameter | Begränsningar |
| --- | --- |
| Storleksförfrågningar med TSQ | 32 KB |
| Gräns för TSQ svar | 16 MB |
| TSQ parallell gräns | 20 per miljö |
| Hämta händelser | 16 MB storlek eller 30 sekunder tid |
| Token giltighet | 1 timme |
| Hämta serien | 500 000 intervall eller tidsstämplar eller 16 MB storlek |
| Min | Gränsen för intervallet 1 ms |
| Max | 50 planerade variabler |
| Sammanställd serien | 500 000 intervall eller tidsstämplar eller 16 MB storlek |
| Min | Gränsen för intervallet 1 ms |
| Max | 50 planerade variabler |

## <a name="time-series-query-tutorial"></a>Time Series fråga självstudien

En fullständig TSQ vägledning ges i framtiden.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>Nästa steg

Läs den [Azure TSI (förhandsversion) lagring och Ingångsanspråk](./time-series-insights-update-storage-ingress.md).

Läs mer om den nya [Tidsseriemodell](./time-series-insights-update-tsm.md).

Läs mer om [bästa praxis när du väljer en tid-ID](./time-series-insights-update-how-to-id.md).

<!-- Images -->
[1]: media/v2-update-tsq/goal.png
[2]: media/v2-update-tsq/tsq.png
