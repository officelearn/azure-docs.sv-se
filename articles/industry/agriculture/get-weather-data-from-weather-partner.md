---
title: Hämta väderdata från väderpartner
description: I den här artikeln beskrivs hur du hämtar väderdata från partner.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266167"
---
# <a name="get-weather-data-from-weather-partners"></a>Hämta väderdata från väderpartner

Azure FarmBeats helps you to bring weather data from your weather data provider(s) using a docker-based Connector Framework. Med hjälp av det här ramverket implementerar väderdataleverantörer en docker som kan integreras med FarmBeats. För närvarande stöds följande väderdataleverantörer:

[NOAA-data från Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/)

Väderdata kan användas för att generera användbara insikter och bygga AI / ML-modeller på FarmBeats.

## <a name="before-you-start"></a>Innan du börjar

För att få väderdata, se till att du har installerat FarmBeats. **Väderintegrering stöds i version 1.2.11 eller senare**. Information om hur du installerar Azure FarmBeats finns i [Installera FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Aktivera väderintegrering med FarmBeats

Så här börjar du hämta väderdata på hubben FarmBeats och gör följande:

1. Gå till din FarmBeats Data hub swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Navigera till /Partner API och gör en POST-begäran med följande indatanyttolast:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Om du till exempel vill hämta väderdata från NOAA by Azure Open Datasets använder du nyttolasten nedan. Du kan ändra namn och beskrivning enligt dina önskemål.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > Mer information om partnerobjektet finns i [tillägg](get-weather-data-from-weather-partner.md#appendix)

   Föregående steg etablerar resurserna så att docker kan köras i kundens FarmBeats-miljö.  

   Det tar cirka 10-15 minuter att etablera ovanstående resurser.

3. Kontrollera status för det /Partner-objekt som du skapade i steg 2. Det gör du genom att göra en GET-begäran på /Partner API och kontrollera **status** för partnerobjektet. När FarmBeats har konfigurerat partnern är statusen inställd på **Aktiv**.

4. Navigera till /JobType API och gör en GET-begäran på samma. Sök efter de väderjobb som skapas som en del av partnertilläggsprocessen i steg 2. Fältet **pipelineName** i väderjobben kommer att vara av följande format: "partner-name_partner-type_job-name".

5. Nu har din FarmBeats-instans en aktiv väderdatapartner och du kan köra jobb för att begära väderdata för en viss plats (latitud/longitud) och ett datumintervall. JobType(s) har information om vilka parametrar som krävs för att köra väderjobb.

   För NOAA-data från Azure Open Datasets skapas till exempel följande JobType(s):

   - get_weather_data (Hämta ISD/historiska väderdata)
   - get_weather_forecast_data (Hämta väderdata för GFS/prognos)

6. Anteckna **ID:t** och parametrarna för JobType(s).

7. Navigera till API för jobb och gör en POST-begäran på /Jobs med följande indatanyttolast:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Om du till exempel vill köra **get_weather_data**använder du följande nyttolast:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Föregående steg kommer att köra väderjobb enligt definitionen i partnerdockern och inta väderdata till FarmBeats. Du kan kontrollera status för jobbet genom att göra en GET-begäran på /Jobs och leta efter **currentState** i svaret. När den har slutförts är currentState inställd på **Lyckades**.

## <a name="query-ingested-weather-data"></a>Frågeintade väderdata

När väderjobben är klara kan du fråga inta väderdata för att skapa modeller eller användbara insikter. Det finns två sätt att komma åt och fråga väderdata från FarmBeats:

- API och
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Fråga med REST API

Så här frågar du väderdata med Rest API i FarmBeats:

1. I din FarmBeats Data hubhttps://yourdatahub.azurewebsites.net/swagger)swagger ( navigerar du till /WeatherDataLocation API och gör en GET-begäran. Svaret kommer att ha /WeatherDataLocation-objekt som skapats för den plats (latitud/longitud) som angavs som en del av jobbkörningen. Anteckna **ID** och **weatherDataModelId** för objektet/objekten.

2. Gör ett GET/{id} på /WeatherDataModel API för **weatherDataModelId** som anges i steg 1. "Väderdatamodellen" har alla metadata och detaljer om de intjänade väderdata. **Vädermåttet** i **väderdatamodellobjektet** innehåller till exempel information om vilken väderinformation som stöds och i vilka typer och enheter. Exempel:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Anteckna svaret från GET/{id}-anropet för väderdatamodellen.

3. Navigera till **Telemetri** API och gör en POST-begäran med följande indatanyttolast:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Svaret som innehåller väderdata som är tillgängliga för det angivna tidsintervallet kommer att se ut så här:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

I föregående exempel har svaret data för två tidsstämplar tillsammans med måttnamnet ("Temperatur") och värden för de rapporterade väderdata i de två tidsstämplarna. Du måste referera till den associerade väderdatamodellen (enligt beskrivningen i steg 2 ovan) för att tolka typen och enheten för de rapporterade värdena.

### <a name="query-using-azure-time-series-insights-tsi"></a>Fråga med Hjälp av Statistik för Azure Time Series (TSI)

FarmBeats använder [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) för att inta, lagra, fråga och visualisera data på IoT-skala – data som är mycket kontextualiserade och optimerade för tidsserier.

Väderdata tas emot på en EventHub och sedan vidare till en TSI-miljö i FarmBeats resursgrupp. Data kan sedan efterfrågas direkt från TSD: n. Mer information finns i [TSD-dokumentation](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).

Följ stegen för att visualisera data på TSD:

1. Gå till **Azure portal** > **FarmBeats DataHub-resursgrupp** > välja time **series insights-miljö** (tsi-xxxx) > **dataåtkomstprinciper**. Lägg till användare med läsar- eller deltagaråtkomst.

2. Gå till **sidan Översikt i** time series **insights-miljön** (tsi-xxxx) och välj **TIME Series Insights Explorer URL**. Du kan nu visualisera intövda väderdata.

Förutom lagring, frågoring och visualisering av väderdata möjliggör TSD också integrering till en Power BI-instrumentpanel. Mer information finns i [Visualisera data från Time Series Insights i Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="appendix"></a>Bilaga

|        Partner   |  Information   |
| ------- | -------             |
|     DockerDetails - imageName         |          Docker-bildnamn. Till exempel docker.io/azurefarmbeats/farmbeats-noaa (avbildning i hub.docker.com) ELLER myazureacr.azurecr.io/mydockerimage (avbildning i Azure Container Registry) och så vidare. Om inget register tillhandahålls är standard hub.docker.com      |
|          DockerDetails - imageTag             |         Taggnamnet på dockerbilden. Standard är "senaste"     |
|  DockerDetails - autentiseringsuppgifter      |  Autentiseringsuppgifter för att komma åt den privata dockern. Detta kommer att tillhandahållas av kunden till kunden   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. Se [här](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för alla Linux virtuella maskiner tillgängliga. Giltiga värden är: "Small", "ExtraLarge", "Large", "A8", "A9", "Medium", "A5", "A6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12". "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1", "STANDARD_G2", "STANDARD_G3", "STANDARD_G4" , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9". "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6", "STANDARD_NC12", "STANDARD_NC24", "STANDARD_NC24r" , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s", "STANDARD_D2_V3". **Standard är "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedikeradComputerNodes   |  Nej. av dedikerade datornoder för batchpool. Standardvärdet är 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch Node Agent SKU ID. För närvarande stöds endast batch.node.ubuntu 18.04-batch-agent.    |
| DockerDetails - partnerCredentials | autentiseringsuppgifter för att anropa partner-API i docker. Partnern måste ge denna information till sina kunder baserat på auth mekanism som stöds t.ex. Användarnamn/lösenord eller API-nycklar. |
| partnerType (partnerTyp) | "Väder" (Andra partnertyper i FarmBeats är "Sensor" och "Bildspråk")  |
|  namn   |   Önskat namn på partnern i FarmBeats-systemet   |
|  description |  Beskrivning   |

## <a name="next-steps"></a>Nästa steg

Du har nu frågat sensordata från din Azure FarmBeats-instans. Nu lär du dig att [generera kartor](generate-maps-in-azure-farmbeats.md#generate-maps) för dina gårdar.
