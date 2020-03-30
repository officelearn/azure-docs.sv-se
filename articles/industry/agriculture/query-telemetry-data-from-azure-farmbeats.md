---
title: Frågeinterade telemetridata
description: I den här artikeln beskrivs hur du frågar inta telemetridata.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349810"
---
# <a name="query-ingested-telemetry-data"></a>Frågeinterade telemetridata

I den här artikeln beskrivs hur du frågar intasterade sensordata från Azure FarmBeats.

Intag av data från Sakernas Internet (IoT) resurser som enheter och sensorer är ett vanligt scenario i FarmBeats. Du skapar metadata för enheter och sensorer och intar sedan historiska data till FarmBeats i ett kanoniskt format. När sensordata är tillgängliga på FarmBeats Datahub kan vi fråga samma sak för att generera användbara insikter eller byggmodeller.

## <a name="before-you-begin"></a>Innan du börjar

Innan du fortsätter med den här artikeln ska du se till att du har installerat FarmBeats- och intas sensortelemetridata från dina IoT-enheter till FarmBeats.

Om du vill använda sensortelemetridata besöker [du intag av historiska telemetridata](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Innan du fortsätter måste du också se till att du är bekant med FarmBeats REST API:er eftersom du frågar intas telemetri med api:erna. Mer information om FarmBeats API:er finns i [FarmBeats REST API:er](rest-api-in-azure-farmbeats.md). **Se till att du kan göra API-begäranden till din FarmBeats Datahub-slutpunkt**.

## <a name="query-ingested-sensor-telemetry-data"></a>Frågeinterade sensortelemetridata

Det finns två sätt att komma åt och fråga telemetridata från FarmBeats:

- API och
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Fråga med REST API

Följ stegen för att fråga in de intasterade sensortelemetridata med FarmBeats REST API:er:

1. Identifiera sensorn du är intresserad av. Du kan göra detta genom att göra en GET-begäran på /Sensor API.

> [!NOTE]
> **Id** och **sensorModelId** av intresserade sensorobjektet.

2. Gör ett GET/{id} på /SensorModel API för **sensormodelId** som anges i steg 1. "Sensormodellen" har alla metadata och detaljer om den intjänade telemetrin från sensorn. **Sensormåttet** i **sensormodellobjektet** innehåller till exempel information om vilka åtgärder sensorn skickar och i vilka typer och enheter. Exempel:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Anteckna svaret från GET/{id}-anropet för sensormodellen.

3. Gör ett POST-anrop på /Telemetri API med följande indatanyttolast

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. Svaret från API:et /Telemetri kommer att se ut ungefär så här:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
I ovanstående exempelsvar ger den efterfrågade sensortelemetrin data för två tidsstämplar tillsammans med måttnamnet ("moist_soil_last") och värden för den rapporterade telemetrin i de två tidsstämplarna. Du måste hänvisa till den tillhörande sensormodellen (enligt beskrivningen i steg 2) för att tolka typ och enhet för de rapporterade värdena.

### <a name="query-using-azure-time-series-insights-tsi"></a>Fråga med Hjälp av Statistik för Azure Time Series (TSI)

FarmBeats utnyttjar [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) för att inta, lagra, fråga och visualisera data på IoT-skala – data som är mycket kontextualiserade och optimerade för tidsserier.

Telemetridata tas emot på en EventHub och bearbetas och skjuts sedan till en TSI-miljö i FarmBeats-resursgruppen. Data kan sedan efterfrågas direkt från TSD: n. Mer information finns i [TSD-dokumentation](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Följ stegen för att visualisera data på TSD:

1. Gå till **Azure Portal** > **FarmBeats DataHub-resursgrupp** > välja time **series insights-miljö** (tsi-xxxx) > **dataåtkomstprinciper**. Lägg till användare med läsar- eller deltagaråtkomst.
2. Gå till **sidan Översikt i** time series **insights-miljön** (tsi-xxxx) och välj **TIME Series Insights Explorer URL**. Du kommer nu att kunna visualisera den intjesterade telemetrin.

Förutom lagring, frågoring och visualisering av telemetri möjliggör TSD också integrering till en Power BI-instrumentpanel. För mer information, se [här]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>Nästa steg

Du har nu frågat sensordata från din Azure FarmBeats-instans. Nu lär du dig att [generera kartor](generate-maps-in-azure-farmbeats.md#generate-maps) för dina gårdar.
