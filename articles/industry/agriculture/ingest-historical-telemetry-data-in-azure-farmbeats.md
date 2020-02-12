---
title: Mata in historiska telemetridata
description: I den här artikeln beskrivs hur du matar in historiska telemetridata.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0d220d1d88d9d761d9f0eba6187abefb372681be
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77131887"
---
# <a name="ingest-historical-telemetry-data"></a>Mata in historiska telemetridata

I den här artikeln beskrivs hur du matar in historiska sensor data i Azure FarmBeats.

Att mata in historiska data från Sakernas Internet-resurser (IoT) som enheter och sensorer är ett vanligt scenario i FarmBeats. Du skapar metadata för enheter och sensorer och matar sedan in historiska data till FarmBeats i kanoniskt format.

## <a name="before-you-begin"></a>Innan du börjar

Innan du fortsätter med den här artikeln måste du kontrol lera att du har installerat FarmBeats och samlat in historiska data från dina IoT-enheter.
Du måste också aktivera partner åtkomst som anges i följande steg.

## <a name="enable-partner-access"></a>Aktivera partner åtkomst

Du måste aktivera partner integrering till din Azure FarmBeats-instans. Det här steget skapar en klient som har åtkomst till din Azure FarmBeats-instans som din enhets partner och ger dig följande värden som krävs i följande steg:

- API-slut punkt: Detta är Datahub-URL, till exempel https://\<Datahub >. azurewebsites. net.
- Klient-ID:t
- Klientorganisations-ID
- Klienthemlighet
- EventHub-anslutningssträng

Följ de här stegen.

>[!NOTE]
> Du måste vara administratör för att utföra följande steg.

1. Hämta [zip-filen](https://aka.ms/farmbeatspartnerscriptv2)och extrahera den till den lokala enheten. Det kommer att finnas en fil i zip-filen.
2. Logga in på https://portal.azure.com/ och gå till Azure Active Directory-> App-registreringar

3. Klicka på den app-registrering som skapades som en del av din FarmBeats-distribution. Det får samma namn som din FarmBeats-Datahub.

4. Klicka på "exponera ett API" – > på Lägg till ett klient program och ange **04b07795-8ddb-461A-BBEE-02f9e1bf7b46** och kontrol lera "auktorisera omfång". Detta ger åtkomst till Azure CLI (Cloud Shell) för att utföra stegen nedan.

5. Öppna Cloud Shell. Det här alternativet är tillgängligt i verktygsfältet i det övre högra hörnet av Azure Portal.

    ![Azure Portal verktygsfält](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Kontrol lera att miljön är inställd på **PowerShell**. Som standard är den inställd på bash.

    ![Inställning för PowerShell-verktygsfält](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Ladda upp filen från steg 1 i Cloud Shell-instansen.

    ![Knappen Ladda upp verktygsfält](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Gå till den katalog där filen laddades upp. Som standard laddas filerna upp till hem katalogen under användar namnet.

9. Kör följande skript. Skriptet frågar efter klient-ID som kan hämtas från översikts sidan för Azure Active Directory->.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. Följ anvisningarna på skärmen för att samla in värdena för **API-slutpunkt**, klient-ID, **klient-ID**, **klient hemlighet**och EventHub **-** **anslutningssträng**.
## <a name="create-device-or-sensor-metadata"></a>Skapa metadata för enhet eller sensor

 Nu när du har de autentiseringsuppgifter som krävs kan du definiera enheten och sensorer. Du gör detta genom att skapa metadata genom att anropa FarmBeats-API: er. Observera att du måste anropa API: erna som den klient app som du skapade i ovanstående avsnitt

 FarmBeats Datahub har följande API: er som möjliggör skapande och hantering av enhets-eller sensor-metadata. Observera att du som partner har åtkomst till att läsa, skapa och uppdatera metadata. **Delete tillåts inte av en partner.**

- /**DeviceModel**: DeviceModel motsvarar enhetens metadata, till exempel tillverkaren och typen av enhet, som är antingen en gateway eller en nod.
- /**enhet**: enheten motsvarar en fysisk enhet som finns i Server gruppen.
- /**SensorModel**: SensorModel motsvarar sensorns metadata, t. ex. tillverkaren, typen av sensor, som är antingen analog eller digital och sensor måttet, till exempel omgivnings temperatur och tryck.
- /**sensor**: sensorn motsvarar en fysisk sensor som registrerar värden. En sensor är vanligt vis ansluten till en enhet med ett enhets-ID.  


|        DeviceModel   |  Förslag   |
| ------- | -------             |
|     Typ (nod, Gateway)        |          Typ av enhet-nod eller gateway      |
|          Tillverkare            |         Tillverkarens namn    |
|  ProductCode                    |  Enhetens produkt kod eller modell namn eller nummer. Till exempel EnviroMonitor # 6800.  |
|            Portar          |     Port namn och-typ, som är digital eller analog.
|     Namn                 |  Namn för att identifiera resursen. Till exempel modell namnet eller produkt namnet.
      Beskrivning     | Ange en meningsfull beskrivning av modellen.
|    Egenskaper          |    Ytterligare egenskaper från tillverkaren.   |
|    **Anordningar**             |                      |
|   DeviceModelId     |     ID för associerad enhets modell.  |
|  HardwareId          | Unikt ID för enheten, till exempel MAC-adressen.
|  ReportingInterval        |   Rapport intervall i sekunder.
|  plats.            |  Enhets-latitud (-90 till + 90), longitud (-180 till 180) och höjning (i meter).   
|ParentDeviceId       |    ID för den överordnade enhet som enheten är ansluten till. Till exempel en nod som är ansluten till en gateway. En nod har parentDeviceId som gateway.  |
|    Namn            | Ett namn för att identifiera resursen. Enhets partner måste skicka ett namn som stämmer överens med enhets namnet på partner sidan. Om partner enhetens namn är användardefinierad, ska samma användardefinierade namn spridas till FarmBeats.|
|     Beskrivning       |      Ange en meningsfull beskrivning. |
|     Egenskaper    |  Ytterligare egenskaper från tillverkaren.
|     **SensorModel**        |          |
|       Typ (analog, digital)          |      Typen av sensor, vare sig det är analogt eller digitalt.       |
|          Tillverkare            |       Sensorns tillverkare.     |
|     ProductCode| Produkt kod eller modell namn eller nummer. Till exempel RS-CO2-N01. |
|       SensorMeasures > namn       | Sensor måttets namn. Endast gemener stöds. Ange djupet för mått från olika djup. Till exempel soil_moisture_15cm. Det här namnet måste vara konsekvent med telemetri-data.  |
|          SensorMeasures > datatype       |Datatyp för telemetri. För närvarande stöds Double.|
|    SensorMeasures > typ    |Typ av mått för sensorer för telemetri. Systemdefinierade typer är AmbientTemperature, CO2, djup, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrat, O2, PH, fosfat, PointInTime, kalium, press, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volym, WindDirection, WindRun, WindSpeed, evapotranspiration, parivärde. Mer information finns i/ExtendedType-API: et.|
|        SensorMeasures > enhet              | Enhet för data för sensor telemetri. De systemdefinierade enheterna är nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kvicksilver, PSI, MilliMeter, CentiMeter, meter, tum, fot, mil, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, examen, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, procent,,,,, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour för att lägga till mer finns i/ExtendedType-API: et.|
|    SensorMeasures > AggregationType    |  Värdena kan vara ingen, genomsnitt, högsta, lägsta eller StandardDeviation.  |
|          Namn            | Namn för att identifiera en resurs. Till exempel modell namnet eller produkt namnet.  |
|    Beskrivning        | Ange en meningsfull beskrivning av modellen.  |
|   Egenskaper       |  Ytterligare egenskaper från tillverkaren.  |
|    **Mäta**      |          |
| HardwareId          |   Unikt ID för sensorn som anges av tillverkaren. |
|  SensorModelId     |    ID för associerad sensor modell.   |
| plats.          |  Sensor Latitude (-90 till + 90), longitud (-180 till 180) och höjning (i meter).|
|   Port > namn        |  Namn och typ för den port som sensorn är ansluten till på enheten. Det måste vara samma namn som det definieras i enhets modellen. |
|    DeviceID  |    ID för den enhet som sensorn är ansluten till.     |
| Namn            |   Namn för att identifiera resursen. Till exempel sensor namn, produkt namn och modell nummer eller produkt kod.|
|    Beskrivning      | Ange en meningsfull beskrivning. |
|    Egenskaper        |Ytterligare egenskaper från tillverkaren. |

Mer information om objekt finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>API-begäran för att skapa metadata

Om du vill göra en API-begäran kombinerar du HTTP-metoden (POST), URL: en till API-tjänsten och URI: n till en resurs att fråga, skicka data till, skapa eller ta bort en begäran. Sedan lägger du till en eller flera huvuden för HTTP-begäran. URL-adressen till API-tjänsten är API-slutpunkten, det vill säga Datahub-URL: en (https://\<yourdatahub >. azurewebsites. net).  

### <a name="authentication"></a>Autentisering

FarmBeats Datahub använder Bearer-autentisering, som behöver följande autentiseringsuppgifter som genererades i föregående avsnitt:

- Klientorganisations-ID
- Klienthemlighet
- Klient-ID:t

Med dessa autentiseringsuppgifter kan anroparen begära en åtkomsttoken. Token måste skickas i efterföljande API-begäranden i avsnittet rubrik enligt följande:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Följande exempel på python-kod ger åtkomst-token som kan användas för efterföljande API-anrop till FarmBeats: 

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

Här är de vanligaste begärandehuvuden som måste anges när du gör ett API-anrop till FarmBeats Datahub:

- **Innehålls typ**: Application/JSON
- **Auktorisering**: innehavare < åtkomst-token >
- **Acceptera**: Application/JSON

### <a name="input-payload-to-create-metadata"></a>Ange nytto last för att skapa metadata

DeviceModel


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Enhet

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Mäta

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Följande exempel förfrågan skapar en enhet. Den här begäran har indataports-JSON som nytto last med begär ande texten.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> API: erna returnerar unika ID: n för varje instans som skapats. Du måste behålla ID: na för att skicka motsvarande telemetri-meddelanden.

### <a name="send-telemetry"></a>Skicka telemetri

Nu när du har skapat enheterna och sensorer i FarmBeats kan du skicka de associerade telemetri-meddelandena.

### <a name="create-a-telemetry-client"></a>Skapa en telemetri-klient

Du måste skicka Telemetrin till Azure-Event Hubs för bearbetning. Azure Event Hubs är en tjänst som gör att du kan mata in real tids data (telemetri) från anslutna enheter och program. Om du vill skicka telemetridata till FarmBeats skapar du en klient som skickar meddelanden till en Event Hub i FarmBeats. Mer information om hur du skickar telemetri finns i [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Skicka ett meddelande om telemetri som klienten

När du har upprättat en anslutning som en Event Hubs-klient kan du skicka meddelanden till Event Hub som JSON.

Här är exempel på python-kod som skickar telemetri som en klient till en angiven Event Hub:

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

Konvertera det historiska sensor data formatet till ett kanoniskt format som Azure-FarmBeats förstår. Det kanoniska meddelande formatet är följande:

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

När du har lagt till motsvarande enheter och sensorer måste du hämta enhets-ID och sensor-ID i telemetri-meddelandet, enligt beskrivningen i föregående avsnitt.

Här är ett exempel på ett telemetri-meddelande:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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

## <a name="troubleshooting"></a>Felsökning

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Det går inte att Visa telemetridata efter att du har matat in historiska/strömmande data från sensorer

**Symptom**: enheter eller sensorer distribueras och du har skapat enheter/sensorer på FarmBeats och inmatad telemetri till EventHub, men du kan inte hämta eller Visa telemetridata på FarmBeats.

**Korrigerande åtgärd**:

1. Se till att du har utfört partner registreringen korrekt – du kan kontrol lera detta genom att gå till Datahub-Swagger, navigera till/partner-API, göra en get-och kontrol lera om partnern är registrerad. Om inte följer du [stegen här](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) för att lägga till partner.
2. Se till att du har skapat metadata (DeviceModel, enhet, SensorModel, sensor) med hjälp av partner klientens autentiseringsuppgifter.
3. Kontrol lera att du har använt rätt format för telemetri (som anges nedan):

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```


## <a name="next-steps"></a>Nästa steg

Mer information om REST API-baserad integrations information finns i [REST API](rest-api-in-azure-farmbeats.md).
