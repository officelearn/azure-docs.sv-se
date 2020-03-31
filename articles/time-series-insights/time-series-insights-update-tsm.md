---
title: Tidsseriemodell – insikter i Azure-tidsserier | Microsoft-dokument
description: Läs mer om Time Series-modellen i förhandsversionen av Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476662"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Förhandsversion av Time Series-modellen i Azure Time Series Insights

I den här artikeln beskrivs Time Series Model, funktionerna och hur du börjar bygga och uppdatera dina egna modeller i förhandsversionen av Azure Time Series Insights.

> [!TIP]
>  * Gå till [Demomiljön Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) för ett exempel på en live Time Series Model.Go to the Contoso Wind Farm demo environment for a live Time Series Model example.
> * Läs om förhandsversionen av [Azure Time Series Insights](time-series-insights-update-explorer.md) om du vill lära dig hur du navigerar i användargränssnittet för tidsseriemodell.
> * Lär dig hur du [arbetar med Time Series Model](time-series-insights-update-how-to-tsm.md) med hjälp av webbutforskaren Time Series Insights.

## <a name="summary"></a>Sammanfattning

Traditionellt saknar data som samlas in från IoT-enheter kontextuell information, vilket gör det svårt att hitta och analysera sensorer snabbt. Den främsta motivationen för Time Series Model är att förenkla att hitta och analysera IoT- eller Time Series-data. Det uppnår detta mål genom att möjliggöra kurering, underhåll och anrikning av tidsseriedata för att förbereda konsumentklara datauppsättningar för analys.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: Contosos nya smarta ugn

**Tänk på det fiktiva scenariot med en Contoso smart ugn.** I det här scenariot, anta att varje Contoso smart ugn har fem temperatursensorer, en för var och en av fyra toppbrännare och en för ugnen själv. Tills nyligen skickade varje Contoso temperatursensor, lagras och visualiserade sina data individuellt. För sin köksapparat övervakning, Contoso förlitade sig på grundläggande diagram, en för varje enskild sensor.

Contoso var nöjd med sina ursprungliga data och visualiseringslösningar, men flera begränsningar blev uppenbara:

* Kunderna ville veta hur varmt den totala ugnen skulle bli när de flesta av de bästa brännare var på. Contoso hade svårare att analysera och presentera ett enhetligt svar om villkoren i den totala ugnen.
* Contoso-tekniker ville kontrollera att de bästa brännare som körs samtidigt inte skulle resultera i ineffektivt effektdragning. Det fanns svårigheter att korsreferera vilka temperatur- och spänningssensorer som var förknippade med varandra och hur man lokaliserar dem i butiken.
* Contoso kvalitetssäkringsteamet ville granska och jämföra historiken mellan två sensorversioner. Det var svårt att avgöra vilka data som tillhörde vilken sensorversion.

Utan möjlighet att strukturera, organisera och definiera den övergripande modellen för smarta ugnstidsserier, upprätthålls varje temperatursensor ur led, isolerade och mindre informativa datapunkter. Det var svårare att omvandla dessa datapunkter till användbara insikter eftersom varje datauppsättning levde oberoende av de andra.

Dessa begränsningar visade vikten av smarta data aggregering och visualisering verktyg för att följa Contoso nya ugn:

* Datavisualisering visar sig vara användbart när du kan associera och kombinera data till en praktisk vy. Ett exempel är att visa spänningssensorer tillsammans med temperaturgivare.
* Det kan vara svårt att hantera flerdimensionella data för flera entiteter tillsammans med funktioner för jämförelse, zoomning och tidsintervall.

**Time Series Model är en praktisk lösning** för många av de scenarier som påträffas i det här fiktiva exemplet:

[![Exempel på smart ugnsdiagram i Tidsseriemodell](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Time Series Model spelar en viktig roll i frågor och navigering eftersom det kontextualiserar data genom att tillåta jämförelser som ska dras över tidsintervall och mellan sensor- och enhetstyper. **A**. 
* Data kontextualiseras ytterligare eftersom data som sparas i Tidsseriemodellen bevarar tidsseriefrågeberäkningar som variabler och återanvänder dem vid frågetid.
* Time Series Model organiserar och sammanställer data för förbättrade visualiserings- och hanteringsfunktioner. **B)** 

### <a name="key-capabilities"></a>De viktigaste funktionerna

Med målet att göra det enkelt och enkelt att hantera sammanhangskontextualisering i tidsserier möjliggör Time Series Model följande funktioner i förhandsversionen av Time Series Insights. Det hjälper dig:

* Skapa och hantera beräkningar eller formler som utnyttjar skalärfunktioner, aggregerade åtgärder och så vidare.
* Definiera överordnade och underordnade relationer för att aktivera navigering, sökning och referens.
* Definiera egenskaper som är associerade med instanserna, definierade som *förekomstfält,* och använd dem för att skapa hierarkier.

### <a name="components"></a>Komponenter

Time Series Model har tre kärnkomponenter:

* [Time Series-modellinstanser](#time-series-model-instances)
* [Hierarkier i tidsseriemodell](#time-series-model-hierarchies)
* [Modelltyper för tidsserier](#time-series-model-types)

Dessa komponenter kombineras för att ange en tidsseriemodell och för att organisera dina Azure Time Series Insights-data.

[![Översiktsdiagram för tidsseriemodell](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

En tidsseriemodell kan skapas och hanteras via [Time Series Insights Preview-gränssnittet.](time-series-insights-update-how-to-tsm.md) Inställningar för tidsseriemodell kan hanteras via [API:et för modellinställningar](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Time Series-modellinstanser

Time Series *Model-instanser* är virtuella representationer av själva tidsserien.

I de flesta fall identifieras instanser unikt av **deviceId** eller **assetId**, som sparas som tidsserie-ID: n.

Instanser har beskrivande information som är associerad med dem som kallas *förekomstegenskaper*, till exempel ett tidsserie-ID, typ, namn, beskrivning, hierarkier och förekomstfält. Instansegenskaperna innehåller åtminstone hierarkiinformation.

*Förekomstfält* är en samling beskrivande information som kan innehålla värden för hierarkinivåer, samt tillverkare, operator och så vidare.

När en händelsekälla har konfigurerats för time series insights-miljön identifieras och skapas instanser automatiskt i en tidsseriemodell. Instanserna kan skapas eller uppdateras via time series insights-utforskaren med hjälp av tidsseriemodellfrågor.

[Contoso Wind Farm demo](https://insights.timeseries.azure.com/preview/samples) ger flera levande exempel.

[![Exempel på tidsseriemodell](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Instansegenskaper

Instanser definieras av **timeSeriesId**, **typeId**, **namn**, **beskrivning,** **hierarkiIds**och **instanceFields**. Varje förekomst mappar till endast en *typ*och en eller flera *hierarkier*.

| Egenskap | Beskrivning |
| --- | ---|
| timeSeriesId (timeSeriesId) | UUID för den tidsserie som instansen är associerad med. |
| typeId (typeId) | UUID för typen Av tidsseriemodell som instansen är associerad med. Som standard associeras alla identifierade nya instanser till en standardtyp.
| namn | Namnegenskapen är valfri och skiftlägeskänslig. **name** Om **namnet** inte är tillgängligt, är det standard **timeSeriesId**. Om ett namn anges är **timeSeriesId** fortfarande tillgängligt i [brunnen](time-series-insights-update-explorer.md#4-time-series-well). |
| description | En textbeskrivning av förekomsten. |
| hierarkiIds | Definierar vilka hierarkier instansen tillhör. |
| instanceFields | Egenskaperna för en instans och statiska data som definierar en instans. De definierar värden för hierarki- eller icke-hierarkiegenskaper samtidigt som de stöder indexering för att utföra sökåtgärder. |

> [!NOTE]
> Hierarkier skapas med hjälp av förekomstfält. Ytterligare **instanceFields** kan läggas till för ytterligare instansegenskapsdefinitioner.

Instanser har följande JSON-representation:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> För Time Series Insights Instance API och skapa, läsa, uppdatera och ta bort (CRUD) stöd, läs [data fråga artikeln](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) och instans API REST [dokumentation](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hierarkier i tidsseriemodell

*Tidsseriemodellhierarkier* ordnar instanser genom att ange egenskapsnamn och deras relationer.

Du kan konfigurera flera hierarkier i en viss Time Series Insights-miljö. En Time Series-modellinstans kan mappas till en enda hierarki eller flera hierarkier (många-till-många-relation).

[Demoklientgränssnittet i Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) visar en standardinstans och typhierarki.

[![Exempel på hierarki i tidsseriemodell](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Hierarkidefinition

Hierarkier definieras av **hierarki-ID,** **namn**och **källa**.

| Egenskap | Beskrivning |
| ---| ---|
| id | Den unika identifieraren för hierarkin, som används, till exempel när du definierar en instans. |
| namn | En sträng som används för att ange ett namn för hierarkin. |
| källa | Anger organisationshierarkin eller sökvägen, som är en överordnad underordnad toppordning för den hierarki som användarna vill skapa. Mappinstansfälten för överordnade och underordnade egenskaper. |

Hierarkier representeras i JSON som:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

I föregående JSON exempel:

* `Location`definierar en hierarki `states` med `cities`överordnad och underordnad . Var `location` och `states`en kan ha flera `cities`, som i sin tur kan ha flera .
* `ManufactureDate`definierar en hierarki `year` med `month`överordnad och underordnad . Var `ManufactureDate` och `years`en kan ha flera `months`, som i sin tur kan ha flera .

> [!TIP]
> För Time Series Insights Instance API och CRUD-stöd läser du [datafrågeartikeln](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) och [hierarki-API REST-dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Exempel på hierarki

Tänk dig ett exempel där `building` `floor`hierarki `room` **H1** har , och som en del av dess **instansFieldNames** definition:

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Med tanke på de förekomstfält som används i föregående definition och flera tidsserier visas hierarkiattributen och hierarkivärdena som visas i följande tabell:

| Id för tidsserier | Förekomstfält |
| --- | --- |
| ID1 | "byggnad" = "1000", "golv" = "10", "rum" = "55"  |
| ID2 | "byggnad" = "1000", "rum" = "55" |
| ID3 | "golv" = "10" |
| ID4 | "byggnad" = "1000", "golv" = "10"  |
| ID5 | Ingen av "byggnad", "golv", eller "rum" är inställd. |

Time Series **ID1** och **ID4** visas som en del av hierarkiN **H1** i Utforskaren för [Azure Time Series Insights](time-series-insights-update-explorer.md) eftersom de har helt definierat och korrekt beställt *byggnads-,* *golv-* och rumsparametrar. *room*

De andra klassificeras under *Ej efterälskade instanser* eftersom de inte överensstämmer med den angivna datahierarkin.

## <a name="time-series-model-types"></a>Modelltyper för tidsserier

Tidsseriemodelltyper hjälper dig att definiera variabler eller formler för att göra beräkningar. *types* Typer är associerade med en specifik Time Series Insights-instans.

En typ kan ha en eller flera variabler. En tidsseriemodellinstans kan till exempel vara av typen *Temperatursensor*, som består av variablerna *avg temperatur,* *min temperatur*och *max temperatur*.

[Contoso Wind Farm-demon](https://insights.timeseries.azure.com/preview/samples) visualiserar flera tidsseriemodelltyper som är associerade med deras respektive instanser.

[![Exempel på typ av tidsseriemodell](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> För Time Series Insights Instance API och CRUD-stöd läser du artikeln [Datafrågefrågor](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) och [typ API REST-dokumentationen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Egenskaper för typ

Tidsseriemodelltyper definieras av **id,** **namn,** **beskrivning**och **variabler**.

| Egenskap | Beskrivning |
| ---| ---|
| id | UUID för typen. |
| namn | En sträng som används för att ange ett namn för typen. |
| description | En strängbeskrivning för typen. |
| Variabler | Ange variabler som är associerade med typen. |

Typerna överensstämmer med följande JSON-exempel:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Variabler

Time Series Insights-typer kan ha många variabler som anger formel- och beräkningsregler för händelser.

Varje variabel kan vara en av tre *typer:* *numerisk,* *kategorisk*och *aggregerad*.

* **Numeriska** sorter arbetar med kontinuerliga värden. 
* **Kategoriska** typer arbetar med en definierad uppsättning diskreta värden.
* **Aggregerade** värden kombinerar flera variabler av ett enda slag (antingen alla numeriska eller alla kategoriska).

I följande tabell visas vilka egenskaper som är relevanta för varje variabel typ.

[![Variabeltabell för tidsseriemodell](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Numeriska variabler

| Variabel egenskap | Beskrivning |
| --- | ---|
| Variabelt filter | Filter är valfria villkorssatser för att begränsa antalet rader som övervägs för beräkning. |
| Variabelt värde | Telemetrivärden som används för beräkning som kommer från enheten eller sensorerna eller omvandlas med hjälp av tidsserieuttryck. Numeriska slagvariabler måste vara av typen *Dubbel*.|
| Variabel interpolation | Interpolation anger hur du rekonstruerar en signal med hjälp av befintliga data. *Steg-* och linjärinterpoleringsalternativ är tillgängliga för numeriska variabler. *Linear* |
| Variabel aggregering | Stöd beräkning genom *Medel,* *Min*, *Max*, *Summa*, *Antal*, *Första*, *Sista* och tidsvägda operatorer (*Medel*, *Min*, *Max*, *Summa*, *Vänster).* |

Variablerna överensstämmer med följande JSON-exempel:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Kategoriska variabler

| Variabel egenskap | Beskrivning |
| --- | ---|
| Variabelt filter | Filter är valfria villkorssatser för att begränsa antalet rader som övervägs för beräkning. |
| Variabelt värde | Telemetrivärden som används för beräkning som kommer från enheten eller sensorerna. Kategoriska slagvariabler måste vara antingen *Långa* eller *Sträng*. |
| Variabel interpolation | Interpolation anger hur du rekonstruerar en signal med hjälp av befintliga data. *Alternativet* Steginterpolering är tillgängligt för kategoriska variabler. |
| Variabla kategorier | Kategorier skapar en mappning mellan värdena som kommer från enheten eller sensorerna till en etikett. |
| Standardkategori för variabel | Standardkategorin är för alla värden som inte mappas i egenskapen "kategorier". |

Variablerna överensstämmer med följande JSON-exempel:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Aggregerade variabler

| Variabel egenskap | Beskrivning |
| --- | ---|
| Variabelt filter | Filter är valfria villkorssatser för att begränsa antalet rader som övervägs för beräkning. |
| Variabel aggregering | Stöd beräkning genom *Medel,* *Min*, *Max*, *Summa*, *Antal*, *Första*, *Sista*. |

Variablerna överensstämmer med följande JSON-exempel:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Variabler lagras i typdefinitionen av en tidsseriemodell och kan tillhandahållas infogade via [Fråge-API:er](time-series-insights-update-tsq.md) för att åsidosätta den lagrade definitionen.

## <a name="next-steps"></a>Nästa steg

- Läs [Azure Time Series Insights Preview lagring och inträngning](./time-series-insights-update-storage-ingress.md).

- Lär dig mer om vanliga time series-modellåtgärder i [datamodellering i förhandsversionen av Azure Time Series Insights](./time-series-insights-update-how-to-tsm.md)

- Läs den nya referensdokumentationen för [tidsseriemodellen.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model)
