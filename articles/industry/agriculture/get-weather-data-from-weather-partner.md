---
title: Hämta väder data från väder partner
description: Den här artikeln beskriver hur du hämtar väder data från partner.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 7666ee1a81c2ed93ee5e246b3ec79f056f9d63ab
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187786"
---
# <a name="get-weather-data-from-weather-partners"></a>Hämta väder data från väder partner

Med Azure FarmBeats kan du samla väder data från dina väder data leverantörer med hjälp av ett Docker-baserat anslutnings ramverk. Med hjälp av det här ramverket implementerar väder data leverantörer en Docker som kan integreras med FarmBeats. För närvarande stöds följande väder data leverantörer:

  ![DTN](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)

  [DTN](https://www.dtn.com/dtn-content-integration/)

Väder data kan användas för att generera instruktions bara insikter och skapa AI/ML-modeller på FarmBeats.

## <a name="before-you-start"></a>Innan du börjar

Se till att du har installerat FarmBeats för att få väder data. **Väder integrering stöds i version 1.2.11 eller senare**. Information om hur du installerar Azure-FarmBeats finns i [Installera FarmBeats](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Aktivera väder integrering med FarmBeats

Följ stegen nedan om du vill börja få väder data på din FarmBeats-data hubb:

1. Gå till FarmBeats data Hub Swagger (https://farmbeatswebsite-api.azurewebsites.net/swagger)

2. Gå till/partner-API och gör en POST-begäran med följande indata-nytto last:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
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

   Om du till exempel vill hämta väder data från DTN använder du nytto lasten nedan. Du kan ändra namnet och beskrivningen enligt dina önskemål.

   > [!NOTE]
   > Steget nedan kräver en API-nyckel. kontakta DTN för att få samma för din DTN-prenumeration.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > Mer information om partner-objektet finns i [bilaga](get-weather-data-from-weather-partner.md#appendix)

   Föregående steg etablerar resurserna för att tillåta Docker att köras i kundens FarmBeats-miljö.  

   Det tar ungefär 10-15 minuter att etablera ovanstående resurser.

3. Kontrol lera status för/partner-objektet som du skapade i steg 2. Det gör du genom att göra en GET-begäran på/partner-API och kontrol lera **statusen** för partner-objektet. När FarmBeats etablerar partnern har statusen **aktive**rad.

4. Gå till/JobType-API: et och gör en GET-begäran på samma sätt. Sök efter väder jobb som skapas som en del av processen för att lägga till samarbets partners i steg 2. Fältet **pipelineName** i väder jobben kommer att ha följande format: "partner-name_partner-type_job-Name".

5. Nu har din FarmBeats-instans en aktiv väderleks data partner och du kan köra jobb för att begära väder data för en viss plats (latitud/longitud) och ett datum intervall. JobType (s) innehåller information om vilka parametrar som krävs för att köra väder jobb.

   För DTN skapas till exempel följande JobType (er):
   
   - get_dtn_daily_observations (få dagliga observationer för en plats och en tids period)
   - get_dtn_daily_forecasts (Hämta dagliga prognoser för en plats och en tids period)
   - get_dtn_hourly_observations (få observationer per timme för en plats och en tids period)
   - get_dtn_hourly_forecasts (Hämta Tim prognoser för en plats och en tids period)

6. Anteckna **ID: t** och parametrarna för JobType.

7. Gå till/Jobs-API och gör en POST-begäran på/Jobs med följande indata-nytto last:

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

   Om du till exempel vill köra **get_dtn_daily_observations**använder du följande nytto last:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Föregående steg kommer att köra väder jobben enligt definitionen i partner Docker och mata in väder data i FarmBeats. Du kan kontrol lera jobbets status genom att göra en GET-begäran på/Jobs och leta efter **currentState** i svaret. När den är klar är currentState inställd på **slutförd**.

## <a name="query-ingested-weather-data"></a>Fråga med inmatade väder data

När väder jobben har slutförts kan du fråga inmatade väder data för att bygga modeller eller åtgärds bara insikter med hjälp av FarmBeats Datahub REST API: er.

### <a name="query-using-rest-api"></a>Fråga med REST API

Följ stegen nedan om du vill fråga efter väder data med FarmBeats REST API:

1. I FarmBeats data hubb Swagger ( https://yourdatahub.azurewebsites.net/swagger) navigerar du till/WeatherDataLocation-API och gör en get-begäran. Svaret kommer att ha/WeatherDataLocation-objekt som skapats för den plats (latitud/longitud) som har angetts som en del av jobb körningen. Anteckna **ID: t** och **weatherDataModelId** för objekten.

2. Gör ett GET/{ID} på/WeatherDataModel-API: et för **weatherDataModelId** som anges i steg 1. "Väder data modellen" har alla metadata och all information om inmatade väder data. **Väder måttet** i **väder data modellens** objekt har till exempel information om vilken väder information som stöds och i vilka typer och enheter. Exempel:

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

   Anteckna svaret från GET/{ID}-anropet för väder data modellen.

3. Navigera till **telemetri** -API och gör en post-begäran med följande indata-nytto last:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. Svaret som innehåller väder data som är tillgängligt för det angivna tidsintervallet ser ut så här:

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

I föregående exempel har svaret data för två tidsstämplar tillsammans med mått namnet ("temperatur") och värden för rapporterade väder data i de två tidsstämplarna. Du måste referera till den associerade väder data modellen (enligt beskrivningen i steg 2 ovan) för att tolka typen och enheten för de rapporterade värdena.

## <a name="troubleshoot-job-failures"></a>Felsöka jobb fel

Om du vill felsöka jobb fel kan du söka efter jobb loggarna. Följ [anvisningarna här](troubleshoot-azure-farmbeats.md#weather-data-job-failures) för samma.


## <a name="appendix"></a>Bilaga

|        Partner   |  Information   |
| ------- | -------             |
|     DockerDetails – imageName         |          Namn på Docker-avbildning. Till exempel docker.io/mydockerimage (bild i hub.docker.com) eller myazureacr.azurecr.io/mydockerimage (bild i Azure Container Registry) och så vidare. Om inget register anges är standard hub.docker.com      |
|          DockerDetails - imageTag             |         Taggnamn för Docker-avbildningen. Standardvärdet är "senaste"     |
|  DockerDetails – autentiseringsuppgifter      |  Autentiseringsuppgifter för att få åtkomst till den privata Docker. Detta kommer att tillhandahållas av partnern till kunden   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU. Se [här](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för alla virtuella Linux-datorer som är tillgängliga. Giltiga värden är: "Small", "ExtraLarge", "Large", "A8", "A9", "medium", "A5", "A6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1" , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24" , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms". **Standardvärdet är "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Nej. dedikerade noder för batch-pool. Standardvärdet är 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch-ID för Node-agent. För närvarande stöds endast batch Node-agenten "batch. Node. Ubuntu 18,04".    |
| DockerDetails - partnerCredentials | autentiseringsuppgifter för anrop av partner-API i Docker. Partnern måste ge den här informationen till sina kunder baserat på mekanismen för autentisering som stöds, t. ex. Användar namn/lösen ord eller API-nycklar. |
| partnerType | "Väder" (andra partner typer i FarmBeats är "sensor" och "bilder")  |
|  name   |   Partnerns önskade namn i FarmBeats-systemet   |
|  beskrivning |  Beskrivning   |

## <a name="next-steps"></a>Nästa steg

Du har nu frågat sensor data från din Azure FarmBeats-instans. Nu kan du lära dig hur du [genererar kartor](generate-maps-in-azure-farmbeats.md#generate-maps) för dina grupper.
