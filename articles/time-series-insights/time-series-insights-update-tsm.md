---
title: Förhandsversion av Time Series-modell i Azure Time Series Insights | Microsoft Docs
description: Förstå Azure Time Series Insights Time Series-modell.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6126a9d56059435be32299bcd1f03050f031f81b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507832"
---
# <a name="time-series-model"></a>Time Series-modell

Den här artikeln beskriver Tidsseriemodell en del av förhandsversionen av Azure Time Series Insights. Det diskuterar modellens egen säkerhetsuppsättning, dess funktioner och hur du kommer igång att skapa och uppdatera din egen modell.

Traditionellt har saknar de data som samlas in från IoT-enheter kontextinformation, vilket gör det svårt att hitta och analysera sensorer snabbt. Det huvudsakliga skälet till Tidsseriemodell är att förenkla söka efter och analysera IoT-data. Det ger det här målet genom att aktivera den hantering och underhåll berikande av time series-data för att förbereda konsumenter datauppsättningar. 

Time Series modeller spela en viktig roll i frågor och navigering eftersom de contextualize enhets- och icke-device-entiteter. Data som har sparat i Tidsseriemodell Driver time series-fråga beräkningar genom att utnyttja de formler som lagras i dem.

![TSM][1]

## <a name="key-capabilities"></a>De viktigaste funktionerna

Med målet att göra det enkelt och enkel att hantera time series contextualization kan Tidsseriemodell följande funktioner i förhandsversionen av Time Series Insights. Det hjälper dig att:

* Skapa och hantera beräkningar eller formler, transformera data att utnyttja skalärfunktioner, aggregera operations och så vidare.

* Definiera överordnade och underordnade relationer för att aktivera navigering och referens och ge ett sammanhang till time series telemetri.

* Definierar egenskaper som är associerade med de instanser som en del av *instans fält* och använda dem för att skapa hierarkier.

## <a name="times-series-model-key-components"></a>Times Series-modell nyckelkomponenter

Time Series-modell har tre huvudkomponenter:

* Time Series-modell *typer*
* Time Series-modell *hierarkier*
* Time Series-modell *instanser*

## <a name="time-series-model-types"></a>Time Series-modell typer

Time Series-modell *typer* hjälper dig att definiera variabler eller formler för att göra beräkningar. Typerna är associerad med en specifik Time Series Insights-instans. En typ kan ha en eller flera variabler. Till exempel en Time Series Insights-instans kan vara av typen *temperatursensor*, som består av variabler *genomsnittlig temperatur*, *min temperatur*, och *max temperatur*. Vi skapar en standardtyp när data börjar flöda till Time Series Insights. Typ av kan hämtas och uppdateras från modellinställningarna. Standardtyperna har en variabel som räknar antalet händelser.

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
```

Mer information om Tidsseriemodell typer finns i den [referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

## <a name="variables"></a>Variabler

Time Series Insights-typerna har variabler, som är namngivna beräkningar över värden från händelserna. Time Series Insights variabeln definitioner innehåller formeln och alla beräkningar som regler. Variabeln definitioner inkluderar *typ*, *värdet*, *filter*, *minskning*, och *gränser*. Variabler lagras i typdefinitionen i Time Series-modell och kan anges infogade Query-API: er att åsidosätta lagrade definitionen.

Följande matrisen fungerar som en förklaring för variabeln definitioner:

![tabell][2]

### <a name="variable-kind"></a>Variabeln typ

Följande variabel typer stöds:

* *numeriskt*
* *Mängd*

### <a name="variable-filter"></a>Variabeln filter

Variabeln filter ange ett valfritt filtersats för att begränsa antalet rader som anses vara för beräkning, baserat på villkor.

### <a name="variable-value"></a>Variabelvärde

Variabelvärden är och ska användas i beräkningen. Detta är kolumnen i de händelser som vi ska referera till.

### <a name="variable-aggregation"></a>Variabeln aggregering

Mängdfunktionen för variabeln kan en del av beräkning. Tidserieinsikter stöder vanliga aggregeringar (nämligen *min*, *max*, *genomsnittlig*, *summan*, och *antal*).

## <a name="time-series-model-hierarchies"></a>Time Series-modell hierarkier

Hierarkier organisera instanser genom att ange egenskapsnamn och deras relationer. Du kan ha en enda hierarki eller flera hierarkier. De behöver inte vara en aktuella del av dina data, men varje instans ska mappa till en hierarki. En Tidsseriemodell-instans kan mappa till en enda hierarki eller flera hierarkier.

Hierarkier har definierats av *hierarki-ID*, *namn*, och *källa*. Hierarkier har en sökväg, vilket är en uppifrån och ned överordnad-underordnad-ordning i hierarkin som användare vill skapa. Överordnad-underordnad egenskaper för kartan *instans fält*.

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

Mer information om Tidsseriemodell hierarkier finns i den [referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Hierarkin definition beteende

Fundera på följande exempel där hierarki H1 har *att skapa*, *våning*, och *rummet* som en del av dess definition:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Beroende på den *instans fält*, hierarki-attribut och värden som ska visas som visas i följande tabell:

| Tidserie-ID | Instansfält |
| --- | --- |
| ID1 | ”Skapa” = ”1000”, ”våning” = ”10”, ”plats” = ”55”  |
| ID2 | ”Skapa” = ”1000”, ”plats” = ”55” |
| ID3 | ”våning” = ”10” |
| ID4 | ”Skapa” = ”1000”, ”våning” = ”10”  |
| ID5 | Ingen ”skapa”, ”våning” eller ”plats” har angetts |

I föregående exempel ID1 och ID4 visas som en del av hierarkin H1 i Azure Time Series Insights explorer och resten klassificeras enligt *utan överordnat objekt instanser* eftersom de inte överensstämmer med angiven data-hierarkin.

## <a name="time-series-model-instances"></a>Time Series-modell instanser

Instanser är tidsserier själva. I de flesta fall den *deviceId* eller *assetId* är den unika identifieraren för tillgången i miljön. Instanser har beskrivande information som är kopplade till dem kallas instansegenskaper. Instansegenskaper för omfattar minst hierarkiinformation. De kan också innehålla användbar och beskrivande data som tillverkaren, operatorn och det sista datumet i tjänsten.

Instanser definieras av *typeId*, *timeSeriesId*, *namn*, *beskrivning*, *hierarchyIds* , och *instanceFields*. Varje instans som mappar till endast en *typ*, och en eller flera hierarkier. Instanser ärver alla egenskaper från hierarkier, och ytterligare *instanceFields* kan läggas till för ytterligare instans egenskapsdefinition.

*instanceFields* är egenskaper för en instans och statiska data som definierar en instans. De definierar värdena för egenskaperna för hierarkin eller -hierarki också stöd för indexering för att utföra sökningar.

Den *namn* egenskapen är valfri och versaler och gemener. Om *namn* är inte tillgänglig, det som standard Time Series ID. Om en *namn* anges så är ID: T för Time Series kommer att finnas kvar i brunnen (rutnätet under diagrammen i explorer). 

## <a name="time-series-model-instance-json-example"></a>Time Series-modell instans JSON-exempel

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

Mer information om Tidsseriemodell instanser finns i den [referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

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

Mer information om Tidsseriemodell inställningar finns i den [referensdokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>Nästa steg

Se [förhandsversion av Azure Time Series Insights storage och ingående](./time-series-insights-update-storage-ingress.md).

Se den nya [Tidsseriemodell](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
