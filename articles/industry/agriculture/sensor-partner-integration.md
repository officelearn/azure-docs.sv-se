---
title: Integrering av sensor partner
description: Beskriver integrering av sensor partner
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 7a85ed93d9ee01255d809cce84ebe24e6c3f71d1
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847399"
---
# <a name="sensor-partner-integration"></a>Integrering av sensor partner

Den här artikeln innehåller information om Azure FarmBeats **Translator** -komponenten som möjliggör integrering av sensor partner.

Med den här komponenten kan partners utveckla sensorer som integreras med FarmBeats, genom att använda vår API och skicka kundenhets data och telemetri till FarmBeats-datahubben. Data visualiseras med FarmBeats-acceleratorn. Data kan användas för data fusion och för att bygga dator språk/artificiella informations modeller.

## <a name="link-farmbeats-account"></a>Länka FarmBeats-konto

När kunderna har köpt och distribuerat enheter/sensorer kan de komma åt enhets data och telemetri på enhets partners SaaS portal (program vara som en tjänst). Enhets partner måste göra det möjligt för kunder att länka sitt konto till sin FarmBeats-instans på Azure. Följande autentiseringsuppgifter krävs för att kunna fylla i med kund-/system integrerare:

   - Visnings namn (ett valfritt fält för användare som definierar ett namn för den här integrationen)
   - API-slutpunkt
   - Klient-ID:t
   - Klientorganisations-ID
   - Klienthemlighet
   - EventHub-anslutningssträng
   - Startdatum

   > [!NOTE]
   > Start datum aktiverar historisk data inmatning, d.v.s. data från det datum som anges av användaren.

## <a name="unlink-farmbeats"></a>Ta bort länk till FarmBeats

Kunder kan ta bort kopplingen mellan en befintlig FarmBeats-integrering. Avlänkning av FarmBeats ska inte ta bort några enhets-/sensor-metadata som har skapats i kundens data hubb. Att ta bort länkar gör följande:

   - Stoppar telemetri-flöde.
   - Tar bort och raderar integrerings uppgifterna på enhets partnern.

## <a name="edit-farmbeats-integration"></a>Redigera FarmBeats-integrering

Kunden kan redigera FarmBeats-integrerings inställningarna om klient hemligheten eller anslutnings strängen ändras. I det här fallet kan kunden bara redigera följande fält:

   - Visnings namn (om tillämpligt)
   - Klient hemlighet (ska visas i formatet "2x8 * * * * * * * * *" eller Visa/Dölj i stället för klartext)
   - Anslutnings sträng (ska visas i formaten "2x8 * * * * * * * * * *" eller Visa/Dölj i stället för klartext)

## <a name="view-last-telemetry-sent"></a>Visa senaste telemetri som skickats

Du kan visa tidsstämpel för senaste **telemetri**som har skickats. Detta är den tid då den senaste Telemetrin skickades till FarmBeats.

## <a name="translator-development"></a>Translator-utveckling

**REST API-baserad integrering**

Sensor data integrerings funktionerna i FarmBeats exponeras via REST API. Funktionerna omfattar metadata-definition, enhets-/sensor etablering, enhet och sensor hantering.

**Inmatning av telemetri**

Telemetri-data mappas till ett kanoniskt meddelande som publiceras på Azure Event Hub för bearbetning. Azure EventHub är en tjänst som gör det möjligt att använda real tids data (telemetri) från anslutna enheter och program.

**API-utveckling**

API: erna innehåller Swagger teknisk dokumentation. Mer information om API: er och deras motsvarande förfrågningar/svar finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)

**Autentisering**

FarmBeats utnyttjar Microsoft Azure Active Directory autentisering. Azure App Service tillhandahåller stöd för inbyggd autentisering och auktorisering.

Mer information finns i [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

FarmBeats-datahubben använder Bearer-autentisering, som behöver följande autentiseringsuppgifter:
   - Klientorganisations-ID
   - Klient hemlighet
   - Klient-ID:t

Med hjälp av ovanstående autentiseringsuppgifter kan anroparen begära en åtkomsttoken som måste skickas i efterföljande API-begäranden i rubrik avsnittet enligt följande:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Nedan visas ett exempel på en python-kod som ger åtkomsttoken, som kan användas för efterföljande API-anrop till FarmBeats: 

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


**Rubriker för HTTP-begäran**

Här är de vanligaste begärandehuvuden som måste anges när du gör ett API-anrop till FarmBeats-data hubben:


**Huvud** | **Beskrivning och exempel**
--- | ---
Content-Type | Formatet för begäran (Content-Type: Application/<format>) för API-formatet FarmBeats data Hub är JSON. Innehålls typ: Application/JSON
Auktorisering | Anger den åtkomsttoken som krävs för att göra en API-auktorisering: Bearer < Access-token >
godkänt | Svars formatet. För FarmBeats-API: er för data hubb är formatet JSON accept: Application/JSON

**API-begäranden**

Om du vill göra en REST-API-begäran (representations tillstånds överföring) kombinerar du HTTP-metoden (GET, POST eller tag), URL: en till API-tjänsten, URI: n (Uniform Resource Identifier) till en resurs att fråga, skicka data till, uppdatera eller ta bort och en eller flera HTTP-förfrågningar sidhuvud. URL-tjänstens API-tjänst är API-slutpunkten som tillhandahålls av kunden. Här är ett exempel: https://\<yourdatahub-Site-Name >. azurewebsites. net

Alternativt kan du inkludera frågeparametrar på GET-anrop för att filtrera, begränsa storleken på och sortera data i svaren.

Nedanstående exempel förfrågan är att hämta listan över enheter:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
De flesta GET-, POST-och parkera-anrop kräver en text för JSON-begäran.

Nedanstående exempel förfrågan är att skapa en enhet (det här exemplet har en indatakälla för indatamängden med begär ande texten).

```
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Dataformat

JSON (JavaScript Object Notation) är ett gemensamt, språk oberoende data format som ger en enkel text representation av godtyckliga data strukturer. Mer information finns i [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Specifikationer för metadata

FarmBeats data Hub har följande API: er som gör det möjligt för enhets partner att skapa och hantera metadata för enhet/sensor.  

- /**DeviceModel** -enhets modell motsvarar enhetens metadata, till exempel tillverkare, typ av enhet, antingen gateway eller nod.  
- /**enhet** – enheten motsvarar en fysisk enhet som finns i Server gruppen.
- /**SensorModel** -sensor modell motsvarar meta-data för sensorn, till exempel tillverkare, typ av sensor, antingen analog eller digital, sensor mått såsom omgivnings temperatur, tryck osv.
- /**sensor** -sensor motsvarar en fysisk sensor som registrerar värden. En sensor är vanligt vis ansluten till en enhet med ett enhets-ID.

  Enhets modell| DeviceModel motsvarar enhetens metadata, till exempel tillverkaren, typen av enhet, antingen gateway eller nod.
  --- | ---
  Typ (nod, Gateway)  | 1 stjärna |
  Tillverkare  | 2 stjärnor |
  ProductCode  | Enhetens produkt kod eller modell namn/nummer. Till exempel EnviroMonitor # 6800 |
  Portar  | Port namn och typ (digital/analog)  |
  Namn  | Namn för att identifiera resursen. Till exempel modell namn/produkt namn |
  Beskrivning  | Ange en meningsfull beskrivning av modellen |
  Egenskaper  | Ytterligare egenskaper från tillverkaren |
  **Anordningar** | **Enheten motsvarar en fysisk enhet som finns i Server gruppen. Varje enhet har ett unikt enhets-ID** |
DeviceModelId  |ID för associerad enhets modell. |
HardwareId   |Unikt ID för enheten, till exempel MAC-adress osv.  |
ReportingInterval |Rapport intervall i sekunder |
Plats    |Enhets-latitud (-90 till + 90)/Longitude (-180 till 180)/Elevation (i meter) |
ParentDeviceId | ID för den överordnade enhet som enheten är ansluten till. Till exempel. En nod som är ansluten till en gateway. noden kommer att ha parentDeviceID som gateway |
  Namn  | Namn för att identifiera resursen.  Enhets partner måste skicka ett namn som stämmer överens med enhets namnet på enhets partner sidan. Om enhetens namn är användardefinierat på enhets partner sidan, ska samma användardefinierade namn spridas till FarmBeats  |
  Beskrivning  | Ange en meningsfull beskrivning  |
  Egenskaper  |Ytterligare egenskaper från tillverkaren  |
  **Sensor modell** | SensorModel motsvarar sensorns metadata, till exempel tillverkare, typ av sensor, antingen analog eller digital, sensor mått, till exempel omgivnings temperatur, tryck osv. |
  Typ (analog, digital)  |Nämna analog eller digital sensor|
  tillverkare  | tillverkarens namn |
  ProductCode  | Produkt kod eller modell namn/nummer. Till exempel RS-CO2-N01  |
  SensorMeasures > namn  | Sensor måttets namn. Endast gemener stöds. Ange djupet för mått från olika djup. Soil_moisture_15cm det här namnet måste till exempel vara konsekvent med telemetri-data. |
  SensorMeasures > datatype  | Datatyp för telemetri. För närvarande stöds Double  |
  sensorMeasures > typ  | Typ av mått för sensorer för telemetri. Följande är systemdefinierade typer: AmbientTemperature, CO2, djup, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrat, O2, PH, fosfat, PointInTime, kalium, press, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volym, WindDirection, WindRun, WindSpeed, evapotranspiration, parivärde. Om du vill lägga till mer, referera till/ExtendedType-API
  SensorMeasures > enhet | Enhet för data för sensor telemetri. Följande är systemdefinierade enheter: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kvicksilver, PSI, MilliMeter, CentiMeter, meter, tum, fötter, mil, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, examen, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, procent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, sekunder, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour för att lägga till mer, se/ ExtendedType-API
  SensorMeasures > aggregationType  | Antingen ingen, genomsnitt, Max, minimum, StandardDeviation
  SensorMeasures > djup  | Sensorns djup i centimeter (till exempel mätning av vatten på 10 cm under marken)
  Beskrivning av sensorMeasures->  | Ge en meningsfull beskrivning av måttet
  namn  | Namn för att identifiera resursen. Till exempel modell namn/produkt namn
  description  | Ange en meningsfull beskrivning av modellen
  properties  | Ytterligare egenskaper från tillverkaren
  **Mäta**  |
  HardwareId  | Unikt ID för sensorn som angetts per tillverkare
  SensorModelId  | ID för associerad sensor modell.
  location  | Sensor latitud (-90 till + 90)/Longitude (-180 till 180)/Elevation (i meter)
  Port > namn  |Namn och typ för den port som sensorn är ansluten till på enheten. Detta måste vara samma namn som det definieras i enhets modellen
  deviceId  | ID för den enhet som sensorn är ansluten till
  namn  | Namn för att identifiera resursen. Till exempel sensor namn/produkt namn och modell nummer/produkt kod.
  description  | Ange en meningsfull beskrivning
  properties  | Ytterligare egenskaper från tillverkaren

 Information om varje objekt och deras egenskaper finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > API: erna returnerar unika ID: n för varje instans som skapats. Detta ID måste behållas av Translator för enhets hantering och metadata-synkronisering.


**Synkronisering av metadata**

Translator ska skicka uppdateringar för metadata. Till exempel är uppdaterings scenarier – ändring av enhet/sensor namn, ändring av enhet/sensor plats.

Översättaren bör ha möjlighet att lägga till nya enheter och/eller sensorer som har installerats av användaren efter att du har länkat FarmBeats. Om en enhet/sensor har uppdaterats av användaren, ska samma sak uppdateras i FarmBeats för motsvarande enhet/sensor. Typiska scenarier för uppdaterings enhet/sensor kan vara: ändring av enhets plats, tillägg av sensorer i en nod osv.


> [!NOTE]
> Borttagning stöds inte för enhets/sensor-metadata.
>
> Om du vill uppdatera metadata, är det obligatoriskt att anropa/get/{ID} på enheten/sensorn, uppdatera de ändrade egenskaperna och sedan göra en/put/{ID} så att alla egenskaper som anges av användaren inte går förlorade

### <a name="adding-new-typesunit"></a>Lägga till nya typer/enheter

FarmBeats har stöd för att lägga till nya typer av sensor mått och enheter. För mer information om/ExtendedType-API, [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Specifikationer för telemetri

Telemetri-data mappas till ett kanoniskt meddelande som publiceras på Azure Event Hub för bearbetning. Azure EventHub är en tjänst som gör det möjligt att använda real tids data (telemetri) från anslutna enheter och program.

## <a name="send-telemetry-data-to-farmbeats"></a>Skicka telemetridata till FarmBeats

Om du vill skicka telemetridata till FarmBeats måste du skapa en klient som skickar meddelanden till en Event Hub i FarmBeats. Mer information om telemetridata finns i [Skicka telemetri till Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Här är ett exempel på en python-kod som skickar telemetri som en klient till en angiven Event Hub:

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

Det kanoniska meddelande formatet är som följer:

```
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

Alla nyckel namn i JSON-modulen för telemetri bör vara gemener, t. ex. DeviceID, sensordata osv.

Till exempel telemetri-meddelande:


```
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

## <a name="troubleshooterror-management"></a>Felsöka/fel hantering

**Felsöka alternativ/support**

Om kunden inte kan ta emot enhets data och/eller telemetri i FarmBeats-instansen som angetts bör enhets partnern ge support och en mekanism för att felsöka.

**Data kvarhållning för telemetri**

Telemetridata bör också behållas under en fördefinierad tids period så att samma kan vara användbar vid fel sökning eller omsändning av telemetri vid fel eller data förlust.

**Fel hantering/fel meddelande**

I händelse av ett fel som påverkar enhets-/sensor metadata/data integrering eller telemetri data flöde i enhets partner systemet, ska samma meddelande meddelas till kunden. En mekanism för att lösa fel bör också utformas och implementeras.

**Check lista för anslutning**

Enhets tillverkare/-partner kan ha följande Sanity test/check lista för att säkerställa att de uppgifter som kunden har angett är korrekta.

   - Kontrol lera om en åtkomsttoken tas emot med de angivna autentiseringsuppgifterna
   - Kontrol lera om ett API-anrop lyckas med åtkomsttoken mottagen
   - Kontrol lera om EventHub-klienttjänsten är upprättad

## <a name="next-steps"></a>Nästa steg

Mer information om REST API finns i [REST API](references-for-farmbeats.md#rest-api).
