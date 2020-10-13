---
title: Väderpartnerintegration
description: Den här artikeln beskriver hur en väder data leverantör kan integreras med FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: a2677b5343b2d65a39e7c9f6d5006db599c1ac73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86497003"
---
# <a name="weather-partner-integration"></a>Väderpartnerintegration

Den här artikeln innehåller information om Azure FarmBeats **Connector** Docker-komponenten som väder data leverantörer kan utveckla för att integrera med FarmBeats genom att använda API: er och skicka väder data till FarmBeats. När data är tillgängliga i FarmBeats kan de användas för data fusion och för att skapa maskin inlärnings-och artificiell Intelligence-modeller.

 > [!NOTE]
 > I den här dokumentationen kommer vi att använda en referens implementering som skapats med NOAA från Azure Open data uppsättningar och är tillgänglig på [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > Motsvarande Docker-avbildning finns på [https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

En väder partner måste tillhandahålla en Docker-avbildning/ett program (med specifikationer som nämns nedan) och vara värd för Docker-avbildningen i ett behållar register som kunder kan komma åt. Väder partnern måste tillhandahålla följande information till sina kunder:

- Docker-avbildnings-URL
- Docker-bildtagg
- Nycklar/autentiseringsuppgifter för att komma åt Docker-avbildningen
- Kundspecifika API-nycklar/autentiseringsuppgifter för att komma åt data från väder partnerns system
- Information om VM-SKU (partners kan ge detta om deras Docker har särskilda krav på virtuella datorer, annars kan kunder välja mellan stödda VM-SKU: er i Azure)

Med hjälp av ovanstående Docker-information registrerar kunden en väder partner i sin FarmBeats-instans. Mer information om hur kunder kan använda Docker för att mata in väder data i FarmBeats finns i hand boken för att [Hämta väder data](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner)

## <a name="connector-docker-development"></a>Ansluta Docker-utveckling

**REST API-baserad integrering**

FarmBeats-API: erna innehåller teknisk dokumentation för Swagger. Information om alla API: er och deras motsvarande förfrågningar och svar finns i [FarmBeats Swagger](https://aka.ms/farmbeatsswagger). 

Om du har installerat FarmBeats kan du komma åt din FarmBeats-Swagger på `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Observera att "-API" läggs till i FarmBeats-webbplatsens namn.
API-slutpunkten kommer att vara: `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub-lib

FarmBeats kommer att tillhandahålla en lib som kan användas av väder partnern. Lib är för närvarande tillgänglig som en del av referens implementeringen [här](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). I framtiden kommer samma att vara tillgänglig som ett SDK för flera språk.

### <a name="authentication"></a>Autentisering

**Autentisering med FarmBeats-API: er**

FarmBeats använder Bearer-autentisering och API: er kan nås genom att tillhandahålla en åtkomsttoken i rubrik avsnittet i begäran enligt nedan:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Åtkomsttoken kan begäras från en Azure-funktion som körs på kundens FarmBeats-instans. URL: en för Azure-funktionen kommer att tillhandahållas till Docker-programmet som ett argument och åtkomsttoken kan hämtas genom att göra en GET-begäran på URL: en. Svaret från webb adressen kommer att innehålla åtkomsttoken. Datahub-lib tillhandahåller hjälp funktioner som gör det möjligt för partner att hämta åtkomsttoken. Mer information finns [här](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py).

Åtkomsttoken är bara giltig i några timmar och måste begäras igen när den upphör att gälla.

**Autentisering med API: er på partner sidan**

För att kunderna ska kunna autentisera med API: er för partner sidan under Docker-körningen måste kunderna ange autentiseringsuppgifterna under partner registreringen på följande sätt:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API-tjänsten serialiserar den här dikteringen och lagrar den i ett nyckel [valv](https://docs.microsoft.com/azure/key-vault/basic-concepts).

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) används för att dirigera väder jobb och snurra resurser för att köra Docker-koden. Det ger också en mekanism för att skicka data säkert till den virtuella dator där Docker-jobbet körs. API-autentiseringsuppgifterna, som nu lagras på ett säkert sätt i nyckel valvet, läses som säkra strängar från nyckel valvet och blir tillgängliga som utökade egenskaper i arbets katalogen i Docker-behållaren som activity.jspå (sökvägen till filen är "/mnt/working_dir/activity.jspå") Docker-koden kan läsa autentiseringsuppgifterna från den här filen under körnings tid för att få åtkomst till API: er för partner sidan för kundens räkning. Autentiseringsuppgifterna är tillgängliga i filen enligt följande:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Observera att "partnerCredentials" kommer att vara tillgängligt på det exakta sätt som tillhandahölls av kunden under partner registreringen

FarmBeats-lib tillhandahåller hjälp funktioner som gör det möjligt för partner att läsa autentiseringsuppgifterna från aktivitets egenskaperna. Mer information finns [här](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py).

Filens livs längd är bara under Docker-kod körningen och kommer att tas bort när Docker-körningen har slutförts.

Mer information om hur ADF-pipelines och aktiviteter fungerar finns i [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) .

**Rubriker för HTTP-begäran**

Här är de vanligaste begärandehuvuden som måste anges när du gör ett API-anrop till FarmBeats.

**Sidfot** | **Beskrivning och exempel**
--- | ---
Content-Type | Formatet för begäran (Content-Type: Application/ <format> ). För FarmBeats Datahub-API: er är formatet JSON. Innehålls typ: Application/JSON
Auktorisering | Anger den åtkomsttoken som krävs för att göra ett API-anrop. Auktorisering: Bearer <Access-Token>
Acceptera | Svars formatet. För FarmBeats Datahub-API: er är formatet JSON. Acceptera: Application/JSON

## <a name="data-format"></a>Dataformat

JSON är ett gemensamt språk oberoende data format som ger en enkel text representation av godtyckliga data strukturer. Mer information finns i [JSON.org](http://json.org).

## <a name="docker-specifications"></a>Docker-specifikationer

Docker-programmet måste ha två komponenter: **bootstrap** och **Jobs**. Det kan finnas mer än ett jobb.

### <a name="bootstrap"></a>Bootstrap

Den här komponenten bör köras när kunden initierar Docker-registreringen på FarmBeats. Argumenten (arg1, ARG2) som skickas till det här programmet är:

- FarmBeats API-slutpunkt: FarmBeats API-slutpunkt för API-begäranden: Detta är slut punkten för att göra API-anrop till FarmBeats-distributionen.
- URL för Azure-funktion: det här är din egen personliga slut punkt som ger dig din åtkomsttoken för FarmBeats-API: er. Genom att bara anropa en GET på den här URL: en, kommer du att hämta åtkomsttoken i sitt svar.

Startens ansvar är att skapa nödvändiga metadata så att användarna kan köra dina jobb för att få väder data. Referera till referens implementeringen [här](https://github.com/azurefarmbeats/noaa_docker). Du kan uppdatera bootstrap_manifest.jspå filen efter behov och Start programmet för referens skapar nödvändiga metadata åt dig.

Följande metadata skapas som en del av den här processen. 

 > [!NOTE]
 > **Observera** att om du uppdaterar bootstrap_manifest.jspå filen som anges i [referens implementeringen](https://github.com/azurefarmbeats/noaa_docker), behöver du inte skapa nedanstående metadata eftersom Start programmet skapar samma baserat på manifest filen.

- /**WeatherDataModel**: en WeatherDataModel är en modell som representerar väder data och som motsvarar olika data uppsättningar som tillhandahålls av källan. Till exempel kan en DailyForecastSimpleModel tillhandahålla genomsnittlig temperatur, fuktighet och fällning en gång om dagen medan en DailyForecastAdvancedModel kan ge mycket mer information om varje timme. Du kan skapa valfritt antal WeatherDataModels.
- /**JobType**: FarmBeats har ett utöknings Bart jobb hanterings system. Som en väder data leverantör har du olika data uppsättningar/API: er (till exempel GetDailyForecasts) – du kan aktivera dem i FarmBeats som JobType. När en JobType har skapats kan en kund utlösa jobb av den typen för att få väder data för deras plats/intresse grupp (se JobType och jobb-API: er i [FarmBeats Swagger](https://aka.ms/farmbeatsswagger)).

### <a name="jobs"></a>Jobb

Den här komponenten kommer att anropas varje gång en FarmBeats-användare kör ett jobb av din/JobType som du skapade som en del av start processen. Kommandot Docker kör för jobbet definieras som en del av **/JobType** som du skapade.
- Jobbets ansvar är att hämta data från källan och skicka det till FarmBeats. De parametrar som krävs för att hämta data ska definieras som en del av/JobType i Start processen.
- Som en del av jobbet måste programmet skapa en **/WeatherDataLocation** baserat på/WeatherDataModel som skapades som en del av start processen. **/WeatherDataLocation** motsvarar en plats (lat/long) som tillhandahålls av användaren som en parameter för jobbet.

### <a name="details-of-the-objects"></a>Information om objekten

  WeatherDataModel | Beskrivning |
  --- | ---
  Namn  | Namnet på väder data modellen |
  Beskrivning  | Ange en meningsfull beskrivning av modellen. |
  Egenskaper  | Ytterligare egenskaper som definieras av data leverantören. |
  weatherMeasures > namn  | Namnet på väder måttet. Till exempel humidity_max |
  weatherMeasures > datatype  | antingen Double eller Enum. Om Enum, krävs measureEnumDefinition |
  weatherMeasures > measureEnumDefinition  | Krävs endast om datatype är Enum. Till exempel {"noregn": 0, "snö": 1, "Drizzle": 2, "regn": 3} |
  weatherMeasures > typ  | typ av data för väder telemetri. Till exempel "RelativeHumidity". Följande är systemdefinierade typer: AmbientTemperature, nounit, CO2, djup, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrat, O2, PH, fosfat, PointInTime, kalium, press, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volym, WindDirection, WindRun, WindSpeed, evapotranspiration, parivärde. Om du vill lägga till mer läser du/ExtendedType-API: et eller i [avsnittet Lägg till typer och enheter](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) nedan
  weatherMeasures > enhet | Enhet för väder informations data. Följande är systemdefinierade enheter: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kvicksilver, PSI, MilliMeter, CentiMeter, meter, tum, fot, mil, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, examen, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, liter, VolumetricIonContent, sekunder, MilliLiter, UnixTimestamp och MicroMolPerMeterSquaredPerSecond. Om du vill lägga till mer läser du/ExtendedType-API: et eller i [avsnittet Lägg till typer och enheter](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) nedan.
  weatherMeasures > AggregationType  | Antingen ingen, genomsnitt, Max, minimum, StandardDeviation, sum, total
  weatherMeasures > djup  | Sensorns djup i centimeter. Till exempel mätningen av fukt 10 cm under marken.
  Beskrivning av weatherMeasures->  | Ge en meningsfull beskrivning av måttet. |
  **JobType** | **Beskrivning** |
  Namn  | namnet på jobbet, till exempel Get_Daily_Forecast; jobbet som kunden kommer att köra för att hämta väder data|
  pipelineDetails > parametrar > namn  | parameterns namn |
  pipelineDetails > parametrar > typ | antingen sträng, int, Float, bool, array |
  pipelineDetails > parametrar > parametrarna isrequired | booleskt sant om den obligatoriska parametern, falskt om inte; Standardvärdet är true |
  pipelineDetails > parametrar > defaultValue | Standardvärdet för parametern |
  pipelineDetails > parametrar > Beskrivning | Beskrivning av parametern |
  Egenskaper  | Ytterligare egenskaper från tillverkaren.
  Egenskaper > **programRunCommand** | Docker Kör kommando – det här kommandot körs när kunden kör väder jobbet. |
  **WeatherDataLocation** | **Beskrivning** |
  weatherDataModelId  | ID för motsvarande WeatherDataModel som skapades under start|
  location  | representerar latitud, longitud och höjning |
  Namn | Objektets namn |
  Beskrivning | Beskrivning |
  farmId | **valfritt** ID för den grupp som tillhandahålls av kunden som en del av jobb parametern |
  Egenskaper  | Ytterligare egenskaper från tillverkaren.

 Information om varje objekt och deras egenskaper finns i [Swagger](https://aka.ms/FarmBeatsSwagger).

 > [!NOTE]
 > API: erna returnerar unika ID: n för varje instans som skapats. Detta ID måste behållas av Translator för enhets hantering och metadata-synkronisering.

**Synkronisering av metadata**

Anslutningens Docker bör ha möjlighet att skicka uppdateringar för metadata. Exempel på uppdaterings scenarier är – tillägg av nya väder parametrar i väder leverantörens data uppsättning, och ytterligare funktioner (t. ex. Tillägg av 30-dagars prognos)

> [!NOTE]
> Ta bort stöds inte för tex metadata. Väder data modell.
>
> Om du vill uppdatera metadata, är det obligatoriskt att anropa/Get/{ID} på väder data modellen, uppdatera de ändrade egenskaperna och sedan göra en/Put/{ID} så att alla egenskaper som anges av användaren inte går förlorade.

## <a name="weather-data-telemetry-specifications"></a>Specifikationer för väder data (telemetri)

Väder data mappas till ett kanoniskt meddelande som skickas till en Azure Event Hub för bearbetning. Azure EventHub är en tjänst som gör det möjligt att använda real tids data (telemetri) från anslutna enheter och program. Om du vill skicka väder data till FarmBeats måste du skapa en klient som skickar meddelanden till en Event Hub i FarmBeats. Om du vill veta mer om att skicka telemetri kan du läsa [Skicka telemetri till en Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)

Här är ett exempel på en python-kod som skickar telemetri som en klient till en angiven Event Hub.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Det kanoniska meddelande formatet är följande:

```json
{
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Här är ett telemetri-meddelande:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Fel sökning och fel hantering

**Fel loggning**

Eftersom partner jobbet kommer att köras i det befintliga jobb ramverket, loggas felen på samma sätt som fel för andra befintliga jobb i FarmBeats (t. ex. GetFarmData, SensorPlacement osv.). Den ADF-aktivitet som körs i ADF-pipeline loggar både STDERR och STDOUT. Båda filerna är tillgängliga i lagrings kontot "datahublogs-XXX" i resurs gruppen FarmBeats.

Datahub-lib tillhandahåller hjälp funktioner för att aktivera loggning som en del av de övergripande Datahub-loggarna. Mer information finns [här](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py).

**Fel söknings alternativ eller support**

Om kunden inte kan ta emot väder data i den FarmBeats-instansen som angetts bör väder partnern ge support och en mekanism för att felsöka samma.

## <a name="add-extendedtype"></a>Lägg till ExtendedType

FarmBeats har stöd för att lägga till nya typer av sensor mått och enheter. Observera att en väder partner kan lägga till nya enheter/typer genom att uppdatera bootstrap_manifest.jspå filen i referens implementeringen [här](https://github.com/azurefarmbeats/noaa_docker)

Om du vill lägga till en ny WeatherMeasure-typ, till exempel "PrecipitationDepth", följer du stegen nedan.

1. Gör en GET-begäran på/ExtendedType med frågans filter-Key = WeatherMeasureType
2. Notera ID: t för det returnerade objektet.
3. Lägg till den nya typen i listan i det returnerade objektet och gör en begäran på/ExtendedType{ID} med följande nya lista. Nytto lastens nytto Last ska vara samma som det svar som tas emot ovan och den nya enheten sist i slutet av listan med värden.

Mer information om/ExtendedType-API: et finns i [Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Nästa steg

Nu har du en anslutnings Docker som kan integreras med FarmBeats. Härnäst kan du se hur du får väder data med hjälp av Docker i FarmBeats. Se [Hämta väder data](get-weather-data-from-weather-partner.md).
