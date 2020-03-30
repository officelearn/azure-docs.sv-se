---
title: Sensorpartnerintegration
description: I den här artikeln beskrivs integrering av sensorpartner.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 48a2ed5e4774ac07b4b8fa72a5ee0be86811cfb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298741"
---
# <a name="sensor-partner-integration"></a>Sensorpartnerintegration

Den här artikeln innehåller information **Translator** om Azure FarmBeats Translator-komponenten, som möjliggör integrering av sensorpartner.

Med den här komponenten kan partner integreras med FarmBeats med FarmBeats Datahub API:er och skicka kundenhetsdata och telemetri till FarmBeats Datahub. När data är tillgängliga i FarmBeats visualiseras den med FarmBeats Accelerator och kan användas för datafusion och för att bygga maskininlärningsmodeller/modeller för artificiell intelligens.

## <a name="before-you-start"></a>Innan du börjar

För att utveckla translator-komponenten behöver du följande autentiseringsuppgifter som gör det möjligt att komma åt FarmBeats API:er.

- API-slutpunkt
- Klient-ID:t
- Klientorganisations-ID
- Klienthemlighet
- Anslutningssträng för EventHub

Se det här avsnittet om du vill hämta ovanstående autentiseringsuppgifter: [Aktivera enhetsintegrering](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Utveckling av översättare

**REST API-baserad integrering**

Sensordataintegrationsfunktionerna i FarmBeats exponeras via REST API. Funktionerna omfattar metadatadefinition, enhets- och sensoretablering samt enhets- och sensorhantering.

**Intag av telemetri**

Telemetridata mappas till ett kanoniskt meddelande som publiceras på Azure Event Hubs för bearbetning. Azure Event Hubs är en tjänst som möjliggör inmatning av realtidsdata (telemetri) från anslutna enheter och program.

**API-utveckling**

API:erna innehåller teknisk dokumentation för Swagger. Mer information om API:erna och deras motsvarande begäranden eller svar finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Autentisering**

FarmBeats använder Microsoft Azure Active Directory-autentisering.Azure App Service ger inbyggt stöd för autentisering och auktorisering.

Mer information finns i [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats Datahub använder bärareautentisering, som behöver följande autentiseringsuppgifter:
   - Klientorganisations-ID
   - Klienthemlighet
   - Klient-ID:t

Med hjälp av dessa autentiseringsuppgifter kan anroparen begära en åtkomsttoken. Token måste skickas i efterföljande API-begäranden, i rubrikavsnittet, enligt följande:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Följande exempel Python-kod ger åtkomsttoken, som kan användas för efterföljande API-anrop till FarmBeats.

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP-begäranden**

Här är de vanligaste begäranderubrikerna som måste anges när du ringer ett API-anrop till FarmBeats Datahub.


**Huvudet** | **Beskrivning och exempel**
--- | ---
Content-Type | Formatet för begäran (Innehållstyp: program/<format>). För FarmBeats Datahub API:er är formatet JSON. Innehållstyp: program/json
Auktorisering | Anger den åtkomsttoken som krävs för att ringa ett API-anrop. Auktorisering: Bärare <Access-Token>
Acceptera | Svarsformatet. För FarmBeats Datahub API:er är formatet JSON. Acceptera: ansökan/json

**API-begäranden**

Om du vill göra en REST API-begäran kombinerar du HTTP-metoden (GET, POST eller PUT), URL:en till API-tjänsten, URI (Uniform Resource Identifier) till en resurs för att fråga, skicka data till, uppdatera eller ta bort och ett eller flera HTTP-begäranden. URL:en till API-tjänsten är den API-slutpunkt som du anger. Här är ett exempel: https://\<dittdatahub-website-namn>.azurewebsites.net

Du kan också inkludera frågeparametrar för GET-anrop för att filtrera, begränsa storleken på och sortera data i svaren.

Följande exempelbegäran är att hämta listan över enheter.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
De flesta GET-, POST- och PUT-samtal kräver en JSON-begäran.

Följande exempelbegäran är att skapa en enhet. (Det här exemplet har en indata-JSON med begärandetexten.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Dataformat

JSON är ett vanligt språkoberoende dataformat som ger en enkel textrepresentation av godtyckliga datastrukturer. Mer information finns [i json.org](http://json.org).

## <a name="metadata-specifications"></a>Metadataspecifikationer

FarmBeats Datahub har följande API:er som gör det möjligt för enhetspartner att skapa och hantera enhets- eller sensormetadata.

- /**DeviceModel**: DeviceModel motsvarar metadata för enheten, till exempel tillverkaren och typen av enhet, som antingen är gateway eller nod.
- /**Enhet**: Enheten motsvarar en fysisk enhet som finns på servergruppen.
- /**SensorModel**: SensorModel motsvarar metadata för sensorn, såsom tillverkaren, vilken typ av sensor, som är antingen analog eller digital, och sensorn mäter, såsom omgivningstemperatur och tryck.
- /**Sensor**: Sensorn motsvarar en fysisk sensor som registrerar värden. En sensor är vanligtvis ansluten till en enhet med ett enhets-ID.

  **EnhetModell** |  |
  --- | ---
  Typ (nod, gateway)  | Typ av enhet - Nod eller Gateway |
  Tillverkare  | Tillverkarens namn |
  Produktkod  | Enhetens produktkod eller modellnamn eller modellnummer. Till exempel EnviroMonitor #6800. |
  Portar  | Portnamn och typ, som är digitalt eller analogt.  |
  Namn  | Namn för att identifiera resurs. Till exempel modellnamn eller produktnamn. |
  Beskrivning  | Ge en meningsfull beskrivning av modellen. |
  Egenskaper  | Ytterligare egenskaper från tillverkaren. |
  **Enhet** |  |
  DeviceModelId  |ID för den associerade enhetsmodellen. |
  HardwareId (HardwareId)   |Unikt ID för enheten, till exempel en MAC-adress.  |
  RapporteringInval |Rapporteringsintervallet i sekunder. |
  Location    |Enhets latitud (-90 till +90), longitud (-180 till 180) och höjd (i meter). |
  ParentDeviceId | ID för den överordnade enhet som enheten är ansluten till. Om till exempel en nod är ansluten till en gateway har noden parentDeviceID som gateway. |
  Namn  | Namn för att identifiera resursen. Enhetspartner måste skicka ett namn som överensstämmer med enhetsnamnet på enhetspartnersidan. Om enhetsnamnet är användardefinierat på enhetspartnersidan ska samma användardefinierade namn spridas till FarmBeats.  |
  Beskrivning  | Ge en meningsfull beskrivning.  |
  Egenskaper  |Ytterligare egenskaper från tillverkaren.  |
  **SensorModel (SensorModel)** |  |
  Typ (analog, digital)  |Nämn analog eller digital sensor.|
  Tillverkare  | Tillverkarens namn. |
  Produktkod  | Produktkod eller modellnamn eller nummer. Till exempel RS-CO2-N01.  |
  SensorÅtgärder > namn  | Sensormåttets namn. Endast gemener stöds. För mätningar från olika djup anger du djupet. Till exempel soil_moisture_15cm. Det här namnet måste vara förenligt med telemetridata. |
  SensorÅtgärder > DataType  | Telemetridatatyp. För närvarande stöds dubbel. |
  SensorÅtgärder > typ  | Mättyp för sensortelemetridata. Följande är de systemdefinierade typerna: AmbientTemperature, CO2, Djup, Elektriskkonduktivitet, LeafWetness, Längd, LiquidLevel, Nitrat, O2, PH, Fosfat, PointInTime, Kalium, Tryck, RainGauge, RelativeHumidity, Salthalt, SoilMoisture, SoilTemperature, SolarRadiation, Stat, TimeDuration, UVRadiation, UVIndex, Volym, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. Mer information finns i API:et /ExtendedType.
  SensorÅtgärder > enhet | Enhet av sensortelemetridata. Följande är de systemdefinierade enheterna: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Grad, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Procent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond och InchesPerHour. Mer information finns i API:et /ExtendedType.
  SensorÅtgärder > AggregationType  | Antingen ingen, genomsnittlig, högsta, lägsta eller StandardDeviation.
  SensorÅtgärder > djup  | Sensorns djup i centimeter. Till exempel mätning av fukt 10 cm under marken.
  SensorÅtgärder > Beskrivning  | Ge en meningsfull beskrivning av mätningen.
  Namn  | Namn för att identifiera resurs. Till exempel modellnamnet eller produktnamnet.
  Beskrivning  | Ge en meningsfull beskrivning av modellen.
  Egenskaper  | Ytterligare egenskaper från tillverkaren.
  **Sensor**  |  |
  HardwareId (HardwareId)  | Unikt ID för sensorn som ställs in av tillverkaren.
  SensorModelId  | ID för den associerade sensormodellen.
  Location  | Sensor latitud (-90 till +90), longitud (-180 till 180), och höjd (i meter).
  Namn på > port  |Namn och typ på den port som sensorn är ansluten till på enheten. Detta måste vara samma namn som definieras i enhetsmodellen.
  DeviceId  | ID för den enhet som sensorn är ansluten till.
  Namn  | Namn för att identifiera resursen. Till exempel sensornamnet eller produktnamnet och modellnumret eller produktkoden.
  Beskrivning  | Ge en meningsfull beskrivning.
  Egenskaper  | Ytterligare egenskaper från tillverkaren.

 Information om var och en av objekten och deras egenskaper finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > API:erna returnerar unika ID:er för varje instans som skapas. Det här ID:t måste behållas av översättaren för enhetshantering och metadatasynkronisering.


**Synkronisering av metadata**

Översättaren ska skicka uppdateringar om metadata. Uppdateringsscenarier ändras till exempel av enhetens eller sensorns namn och ändring av enhetens eller sensorns plats.

Översättaren bör ha möjlighet att lägga till nya enheter eller sensorer som installerades av användaren post länkning av FarmBeats. På samma sätt, om en enhet eller sensor har uppdaterats av användaren, bör samma uppdateras i FarmBeats för motsvarande enhet eller sensor. Typiska scenarier som kräver uppdatering av en enhet eller sensor är en ändring av en enhetsplats eller tillägg av sensorer i en nod.


> [!NOTE]
> Borttagning stöds inte för enhets- eller sensormetadata.
>
> Om du vill uppdatera metadata är det obligatoriskt att anropa /Get/{id} på enheten eller sensorn, uppdatera de ändrade egenskaperna och sedan göra ett /Put/{id} så att alla egenskaper som anges av användaren inte går förlorade.

### <a name="add-new-types-and-units"></a>Lägga till nya typer och enheter

FarmBeats stöder att lägga till nya sensormåtttyper och enheter. Mer information om /ExtendedType-API:et finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Specifikationer för telemetri

Telemetridata mappas till ett kanoniskt meddelande som publiceras på Azure Event Hubs för bearbetning. Azure Event Hubs är en tjänst som möjliggör inmatning av realtidsdata (telemetri) från anslutna enheter och program.

## <a name="send-telemetry-data-to-farmbeats"></a>Skicka telemetridata till FarmBeats

Om du vill skicka telemetridata till FarmBeats skapar du en klient som skickar meddelanden till en händelsehubb i FarmBeats. Mer information om telemetridata finns i [Skicka telemetri till en händelsehubb](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Här är ett exempel på Python-kod som skickar telemetri som klient till en angiven händelsehubb.

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

Det kanoniska meddelandeformatet är följande:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```
Alla nyckelnamn i telemetrin JSON ska vara gemener. Exempel är deviceid och sensordata.

Här är till exempel ett telemetrimeddelande:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

> [!NOTE]
> Följande avsnitt är relaterade till andra ändringar (t.ex. Användargränssnitt, felhantering etc.) sensorpartnern kan hänvisa till vid utveckling av Translator-komponenten.


## <a name="link-a-farmbeats-account"></a>Länka ett FarmBeats-konto

När kunder har köpt och distribuerat enheter eller sensorer kan de komma åt enhetsdata och telemetri på enhetspartnernas programvara som en Tjänstportal (SaaS). Enhetspartner kan göra det möjligt för kunder att länka sitt konto till sin FarmBeats-instans på Azure genom att tillhandahålla ett sätt att ange följande autentiseringsuppgifter:

   - Visningsnamn (ett valfritt fält där användare kan definiera ett namn för den här integrationen)
   - API-slutpunkt
   - Klient-ID:t
   - Klientorganisations-ID
   - Klienthemlighet
   - Anslutningssträng för EventHub
   - Startdatum

   > [!NOTE]
   > Startdatumet aktiverar den historiska datafeeden, det vill än data från det datum som anges av användaren.

## <a name="unlink-farmbeats"></a>Ta bort länken FarmBeats

Enhetspartner kan göra det möjligt för kunder att ta bort länken till en befintlig FarmBeats-integrering. Ta bort länken till FarmBeats bör inte ta bort några enhets- eller sensormetadata som har skapats i FarmBeats Datahub. Ta bort länken gör följande:

   - Stoppar telemetriflödet.
   - Tar bort och raderar integrationsautentiseringsuppgifterna på enhetspartnern.

## <a name="edit-farmbeats-integration"></a>Redigera FarmBeats-integrering

Enhetspartner kan göra det möjligt för kunder att redigera integrationsinställningarna för FarmBeats om klienthemligheten eller anslutningssträngen ändras. I det här fallet är endast följande fält redigerbara:

   - Visningsnamn (om tillämpligt)
   - Klienthemlighet (ska visas i formatet "2x8***********" eller funktionen Visa/dölj i stället för klartext)
   - Anslutningssträng (ska visas i formatet "2x8***********" eller Visa/dölj-funktionen i stället för klartext)

## <a name="view-the-last-telemetry-sent"></a>Visa den senast skickade telemetrin

Enhetspartner kan göra det möjligt för kunder att visa tidsstämpeln för den senaste telemetrin som skickades, som finns under **Skicka telemetri**. Detta är den tidpunkt då den senaste telemetrin skickades till FarmBeats.

## <a name="troubleshooting-and-error-management"></a>Felsökning och felhantering

**Felsöka alternativ eller support**

Om kunden inte kan ta emot enhetsdata eller telemetri i den FarmBeats-instans som angetts, bör enhetspartnern ge stöd och en mekanism för felsökning.

**Lagring av telemetridata**

Telemetridata bör också lagras under en fördefinierad tidsperiod så att de kan vara användbara vid felsökning eller återsendering av telemetrin om ett fel eller dataförlust inträffar.

**Felhantering eller felmeddelande**

Om ett fel påverkar enhetens eller sensormetadata eller dataintegrations- eller telemetridataflödet i enhetspartnersystemet bör kunden få ett meddelande. En mekanism för att lösa eventuella fel bör också utformas och genomföras.

**Checklista för anslutning**

Enhetstillverkare eller partner kan använda följande checklista för att säkerställa att autentiseringsuppgifterna som tillhandahålls av kunden är korrekta:

   - Kontrollera om en åtkomsttoken tas emot med de autentiseringsuppgifter som angavs.
   - Kontrollera om ett API-anrop lyckas med åtkomsttoken som togs emot.
   - Kontrollera om EventHub-klientanslutningen har upprättats.

## <a name="next-steps"></a>Nästa steg

Mer information om REST API finns i [REST API](rest-api-in-azure-farmbeats.md).
