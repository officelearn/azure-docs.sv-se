---
title: Mata in historiska telemetridata
description: I den här artikeln beskrivs hur du intar historiska telemetridata.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e0a5e89f256b562ce5f702e9ff1388cb4d021bf5
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437698"
---
# <a name="ingest-historical-telemetry-data"></a>Mata in historiska telemetridata

I den här artikeln beskrivs hur du intar historiska sensordata i Azure FarmBeats.

Intag av historiska data från Sakernas Internet (IoT) resurser som enheter och sensorer är ett vanligt scenario i FarmBeats. Du skapar metadata för enheter och sensorer och intar sedan historiska data till FarmBeats i ett kanoniskt format.

## <a name="before-you-begin"></a>Innan du börjar

Innan du fortsätter med den här artikeln bör du se till att du har installerat FarmBeats och samlat in historiska data från dina IoT-enheter. Du måste också aktivera partneråtkomst som nämns i följande steg.

## <a name="enable-partner-access"></a>Aktivera partneråtkomst

Du måste aktivera partnerintegrering till din Azure FarmBeats-instans. Det här steget skapar en klient som har åtkomst till din Azure FarmBeats-instans som din enhetspartner och ger dig följande värden som krävs i de följande stegen:

- API-slutpunkt: Detta är Datahub-URL:en,\<till exempel https:// datahub>.azurewebsites.net
- Klient-ID:t
- Klientorganisations-ID
- Klienthemlighet
- Anslutningssträng för EventHub

Följ de här stegen:

> [!NOTE]
> Du måste vara administratör för att kunna göra följande.

1. Logga in på https://portal.azure.com/.

2. **Om du är på FarmBeats version 1.2.7 eller senare, hoppa över steg a, b och c, och gå till steg 3.** Du kan kontrollera FarmBeats-versionen genom att välja **ikonen Inställningar** längst upp till höger i FarmBeats-användargränssnittet.

      a.  Gå till Azure Active > **Directory-appregistreringar** **Azure Active Directory**

      b. Välj den **appregistrering** som skapades som en del av distributionen av FarmBeats. Det kommer att ha samma namn som din FarmBeats datahub.

      c. Välj **Exponera ett API** > väljer Lägg till ett **klientprogram** och ange **04b07795-8ddb-461a-bbee-02f9e1bf7b46** och kontrollera **Auktorisera omfattning**. Detta ger åtkomst till Azure CLI (Cloud Shell) för att utföra stegen nedan:

3. Öppna Cloud Shell. Det här alternativet är tillgängligt i verktygsfältet i det övre högra hörnet av Azure-portalen.

    ![Verktygsfältet Azure Portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. Se till att miljön är inställd på **PowerShell**. Som standard är den inställd på Bash.

    ![Verktygsfältsinställningen i PowerShell](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. Gå till din hemkatalog.

    ```azurepowershell-interactive 
    cd  
    ```

6. Kör följande kommando. Detta kommer att ladda ner ett skript till din hemkatalog.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. Kör följande skript. Skriptet frågar efter klient-ID, som kan erhållas från **Azure Active Directory** > **Overview** sida.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. Följ instruktionerna på skärmen för att samla in värdena för **API-slutpunkt,** **klient-ID,** **klient-ID,** **Klienthemlighet**och **EventHub-anslutningssträng**.


## <a name="create-device-or-sensor-metadata"></a>Skapa enhets- eller sensormetadata

 Nu när du har de autentiseringsuppgifter som krävs kan du definiera enheten och sensorerna. Det gör du genom att skapa metadata genom att anropa FarmBeats API:er. Se till att anropa API:erna som klientapp som du har skapat i avsnittet ovan.

 FarmBeats Datahub har följande API:er som möjliggör skapande och hantering av enhets- eller sensormetadata.

 > [!NOTE]
 > Som partner har du bara åtkomst till att läsa, skapa och uppdatera metadata. **delete-alternativet är begränsat till partnern.**

- /**DeviceModel**: DeviceModel motsvarar enhetens metadata, till exempel tillverkaren och typen av enhet, som antingen är en gateway eller en nod.
- /**Enhet**: Enheten motsvarar en fysisk enhet som finns på servergruppen.
- /**SensorModel**: SensorModel motsvarar metadata för sensorn, såsom tillverkaren, vilken typ av sensor, som är antingen analog eller digital, och sensormätningen, såsom omgivningstemperatur och tryck.
- /**Sensor**: Sensorn motsvarar en fysisk sensor som registrerar värden. En sensor är vanligtvis ansluten till en enhet med ett enhets-ID.  


|        DeviceModel   |  Förslag   |
| ------- | -------             |
|     Typ (nod, gateway)        |          Typ av enhet - nod eller gateway      |
|          Tillverkare            |         Tillverkarens namn    |
|  Produktkod                    |  Enhetens produktkod eller modellnamn eller modellnummer. Till exempel EnviroMonitor #6800.  |
|            Portar          |     Portnamn och typ, som är digitalt eller analogt.
|     Namn                 |  Namn för att identifiera resursen. Till exempel modellnamnet eller produktnamnet.
      Beskrivning     | Ge en meningsfull beskrivning av modellen.
|    Egenskaper          |    Ytterligare egenskaper från tillverkaren.   |
|    **Enhet**             |                      |
|   DeviceModelId     |     ID för den associerade enhetsmodellen.  |
|  HardwareId (HardwareId)          | Unikt ID för enheten, till exempel MAC-adressen.
|  RapporteringInval        |   Rapporteringsintervallet i sekunder.
|  Location            |  Enhets latitud (-90 till +90), longitud (-180 till 180) och höjd (i meter).   
|ParentDeviceId       |    ID för den överordnade enhet som enheten är ansluten till. Till exempel en nod som är ansluten till en gateway. En nod har parentDeviceId som gateway.  |
|    Namn            | Ett namn för att identifiera resursen. Enhetspartner måste skicka ett namn som överensstämmer med enhetsnamnet på partnersidan. Om partnerenhetens namn är användardefinierat ska samma användardefinierade namn spridas till FarmBeats.|
|     Beskrivning       |      Ge en meningsfull beskrivning. |
|     Egenskaper    |  Ytterligare egenskaper från tillverkaren.
|     **SensorModel (SensorModel)**        |          |
|       Typ (analog, digital)          |      Typ av sensor, oavsett om det är analogt eller digitalt.       |
|          Tillverkare            |       Tillverkaren av sensorn.     |
|     Produktkod| Produktkod eller modellnamn eller nummer. Till exempel RS-CO2-N01. |
|       SensorÅtgärder > namn       | Sensormåttets namn. Endast gemener stöds. För mätningar från olika djup anger du djupet. Till exempel soil_moisture_15cm. Det här namnet måste vara förenligt med telemetridata.  |
|          SensorÅtgärder > DataType       |Telemetridatatyp. För närvarande stöds dubbel.|
|    SensorÅtgärder > typ    |Mättyp för sensortelemetridata. De systemdefinierade typerna är AmbientTemperature, CO2, Djup, ElektriskKonduktivitet, LeafWetness, Längd, LiquidLevel, Nitrat, O2, PH, Fosfat, PointInTime, Kalium, Tryck, RainGauge, RelativeHumidity, Salthalt, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volym, WindDirection, WindRun, WindSpeed, Evaranpotspiration, PAR. Mer information finns i API:et /ExtendedType.|
|        SensorÅtgärder > enhet              | Enhet av sensortelemetridata. De systemdefinierade enheterna är NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, MilliMeter, CentiMeter, Meter, Inch, Feet, Mile, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KilowattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Procent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, MilliLit Liter, MilliLit Liter, MilliLit Liter, MilliLit Liter, MilliLit liter, MilliLit liter, MilliLit liter, Liter liter, MilliLit liter, Liter liter, MilliLit liter, Liter liter, MilliLit liter, Liter liter, Liter liter, liter, liter, liter, VolymtricIonContent, MilliLiter Liter, MilliLiter Liter, Liter Liter, Liter Liter, Liter, Liter, Sekunder, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour Om du vill lägga till fler läser du API:et /ExtendedType.|
|    SensorÅtgärder > AggregationType    |  Värden kan vara ingen, medelvärde, maximum, minimum eller StandardDeviation.  |
|          Namn            | Namn för att identifiera en resurs. Till exempel modellnamnet eller produktnamnet.  |
|    Beskrivning        | Ge en meningsfull beskrivning av modellen.|
|   Egenskaper       |  Ytterligare egenskaper från tillverkaren.|
|    **Sensor**      |          |
| HardwareId (HardwareId)          |   Unikt ID för sensorn som ställs in av tillverkaren.|
|  SensorModelId     |    ID för den associerade sensormodellen.|
| Location          |  Sensor latitud (-90 till +90), longitud (-180 till 180), och höjd (i meter).|
|   Namn på > port        |  Namn och typ på den port som sensorn är ansluten till på enheten. Detta måste vara samma namn som definieras i enhetsmodellen.|
|    Deviceid  |    ID för den enhet som sensorn är ansluten till. |
| Namn            |   Namn för att identifiera resurs. Till exempel sensornamn eller produktnamn och modellnummer eller produktkod.|
|    Beskrivning      | Ge en meningsfull beskrivning.|
|    Egenskaper        |Ytterligare egenskaper från tillverkaren.|

Mer information om objekt finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>API-begäran om att skapa metadata

Om du vill göra en API-begäran kombinerar du HTTP-metoden (POST), URL:en till API-tjänsten och URI till en resurs för att fråga, skicka data till, skapa eller ta bort en begäran. Sedan lägger du till ett eller flera HTTP-begäranden. URL:en till API-tjänsten är API-slutpunkten, det vill https://\<Datahub-URL:en (https:// dindatahub>.azurewebsites.net).  

### <a name="authentication"></a>Autentisering

FarmBeats Datahub använder bärareautentisering, som behöver följande autentiseringsuppgifter som genererades i föregående avsnitt:

- Klientorganisations-ID
- Klienthemlighet
- Klient-ID:t

Med hjälp av dessa autentiseringsuppgifter kan anroparen begära en åtkomsttoken. Token måste skickas i efterföljande API-begäranden, i rubrikavsnittet, enligt följande:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Följande exempel python-kod ger åtkomsttoken, som kan användas för efterföljande API-anrop till FarmBeats: 

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

Här är de vanligaste begäranderubrikerna som måste anges när du ringer ett API-anrop till FarmBeats Datahub:

- **Innehåll-Typ**: ansökan / json
- **Auktorisering**: Bärare <Access-Token>
- **Acceptera**: ansökan/json

### <a name="input-payload-to-create-metadata"></a>Nyttolast för indata för att skapa metadata

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

SensorModel (SensorModel)

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
Sensor

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

Följande exempelbegäran skapar en enhet. Denna begäran har matat in JSON som nyttolast med begäran kroppen.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

Nedan finns en exempelkod i Python. Åtkomsttoken som används i det här exemplet är samma som tas emot under autentiseringen.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> API:erna returnerar unika ID:er för varje instans som skapas. Du måste behålla ID:t för att kunna skicka motsvarande telemetrimeddelanden.

### <a name="send-telemetry"></a>Skicka telemetri

Nu när du har skapat enheterna och sensorerna i FarmBeats kan du skicka tillhörande telemetrimeddelanden.

### <a name="create-a-telemetry-client"></a>Skapa en telemetriklient

Du måste skicka telemetrin till Azure Event Hubs för bearbetning. Azure Event Hubs är en tjänst som möjliggör inmatning av realtidsdata (telemetri) från anslutna enheter och program. Om du vill skicka telemetridata till FarmBeats skapar du en klient som skickar meddelanden till en händelsehubb i FarmBeats. Mer information om hur du skickar telemetri finns i [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Skicka ett telemetrimeddelande som klient

När du har upprättat en anslutning som en Event Hubs-klient kan du skicka meddelanden till händelsehubben som JSON.

Här är exempel på Python-kod som skickar telemetri som klient till en angiven händelsehubb:

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

Konvertera det historiska sensordataformatet till ett kanoniskt format som Azure FarmBeats förstår. Det kanoniska meddelandeformatet är följande:

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

När du har lagt till motsvarande enheter och sensorer hämtar du enhets-ID:t och sensor-ID:t i telemetrimeddelandet, enligt beskrivningen i föregående avsnitt.

Här är ett exempel på ett telemetrimeddelande:


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

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Det går inte att visa telemetridata efter intag av historiska/strömmande data från sensorerna

**Symptom:** Enheter eller sensorer distribueras och du har skapat enheter/sensorer på FarmBeats och inmatad telemetri till EventHub, men du kan inte hämta eller visa telemetridata på FarmBeats.

**Korrigerande åtgärder:**

1. Se till att du har gjort rätt partnerregistrering - du kan kontrollera detta genom att gå till din datahub swagger, navigera till / Partner API, Gör en Get och kontrollera om partnern är registrerad. Om inte, följ [stegen här](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) för att lägga till partner.

2. Kontrollera att du har skapat metadata (DeviceModel, Device, SensorModel, Sensor) med hjälp av partnerklientautentiseringsuppgifterna.

3. Kontrollera att du har använt rätt telemetrimeddelandeformat (enligt nedan):

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

Mer information om REST API-baserad integrationsinformation finns i [REST API](rest-api-in-azure-farmbeats.md).
