---
title: Mata in historiska telemetridata
description: Beskriver hur du hämtar historiska telemetridata för inmatningar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6fc70b55b3e672ecc67eb1145bb751de33d998a1
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847442"
---
# <a name="ingest-historical-telemetry-data"></a>Mata in historiska telemetridata

I den här artikeln beskrivs hur du matar in historiska sensor data i Azure FarmBeats.

Att mata in historiska data från Sakernas Internet (IoT) för resurser som enheter och sensorer är ett vanligt scenario i FarmBeats. Du skapar metadata för enheter och sensorer och matar sedan in historiska data till FarmBeats i kanoniskt format.

## <a name="before-you-begin"></a>Innan du börjar

Innan du fortsätter med den här artikeln måste du kontrol lera att du har installerat FarmBeats och samlat in historiska data från IoT.

## <a name="enable-partner-access"></a>Aktivera partner åtkomst

Du måste aktivera partner integrering till din Azure FarmBeats-instans. Det här steget skapar en klient som har åtkomst till din Azure-FarmBeats som din enhets partner och innehåller följande värden som krävs i följande steg.

- API-slutpunkt – det här är data hubbens URL, till exempel https://<datahub>. azurewebsites.net
- Klient-ID:t
- Klientorganisations-ID
- Klient hemlighet
- EventHub-anslutningssträng

Generera följande genom att följa stegen nedan:

>[!NOTE]
> Du måste vara administratör för att utföra följande steg.

1. Hämta det här [skriptet](https://aka.ms/farmbeatspartnerscript) och extrahera det i på den lokala enheten. Du hittar två filer i ZIP-filen.
2. Logga in på [Azure Portal](https://portal.azure.com/) och öppna Cloud Shell (det här alternativet finns i det övre högra fältet i portalen)  

    ![Taktslag i projekt grupp](./media/for-tutorials/navigation-bar-1.png)

3. Se till att miljön är inställd på **PowerShell**.

    ![Taktslag i projekt grupp](./media/for-tutorials/power-shell-new-1.png)

4. Ladda upp de två filerna som du laddade ned (från steg 1 ovan) i din Cloud Shell.  

    ![Taktslag i projekt grupp](./media/for-tutorials/power-shell-two-1.png)

5. Gå till den katalog där filerna laddades upp (som standard överförs den till arbets katalogen/Home/username/.
6. Kör skriptet med hjälp av kommandot:  

    ```azurepowershell-interactive
    PS> ./generateCredentials.ps1
    ```

7. Följ anvisningarna på skärmen för att slutföra proceduren.

## <a name="create-devicesensor-metadata"></a>Skapa metadata för enhet/sensor

 Nu när du har de autentiseringsuppgifter som krävs kan du definiera enheten och sensorer genom att skapa metadata med FarmBeats-API: er.

 FarmBeats-datahubben har följande API: er som möjliggör skapande och hantering av enhet/sensor-metadata.   

- /**DeviceModel** -enhets modell motsvarar enhetens metadata, till exempel tillverkare, typ av enhet, antingen gateway eller nod.  
- /**enhet** – enheten motsvarar en fysisk enhet som finns i Server gruppen.  
- /**SensorModel** -sensor modell motsvarar metadata för sensorn, till exempel tillverkare, typ av sensor, antingen analog eller digital, sensor mått, till exempel omgivnings temperatur eller tryck.
- /**sensor** -sensor motsvarar en fysisk sensor som registrerar värden. En sensor är vanligt vis ansluten till en enhet med ett enhets-ID.  


|        Enhets läge   |  Förslag   |
| ------- | -------             |
|     Typ (nod, Gateway)        |          1 stjärna      |
|          Tillverkare            |         2 stjärnor     |
|  ProductCode                    |  Enhetens produkt kod eller modell namn/nummer. Till exempel EnviroMonitor # 6800.  |
|            Portar          |     Port namn och typ (digital/analog)
|     Namn                 |  Namn för att identifiera resursen. Till exempel modell namn/produkt namn.
      Beskrivning     | Ange en meningsfull beskrivning av modellen
|    Egenskaper          |    Ytterligare egenskaper från tillverkaren   |
|    **Anordningar**             |                      |
|   DeviceModelId     |     ID för associerad enhets modell  |
|  HardwareId          | Unikt ID för enheten, till exempel MAC-adress osv.
|  ReportingInterval        |   Rapport intervall i sekunder
|  Plats            |  Enhets-latitud (-90 till + 90)/Longitude (-180 till 180)/Elevation (i meter)   
|ParentDeviceId       |    ID för den överordnade enhet som enheten är ansluten till. Till exempel en nod som är ansluten till en gateway. En nod kommer att ha parentDeviceId som gateway.  |
|    Namn            | Ett namn för att identifiera resursen. Enhets partner måste skicka ett namn som stämmer överens med enhets namnet på partner sidan. Om partner enhetens namn är användardefinierat, ska samma användardefinierade namn spridas till FarmBeats.|
|     Beskrivning       |      Ange en meningsfull beskrivning  |
|     Egenskaper    |  Ytterligare egenskaper från tillverkaren
|     **Sensor modell**        |          |
|       Typ (analog, digital)          |      typ av sensor, vare sig analog eller digital       |
|          Tillverkare            |       sensorns tillverkare     |
|     ProductCode| Produkt kod eller modell namn/nummer. Till exempel RS-CO2-N01. |
|       SensorMeasures > namn       | Sensor måttets namn. Endast gemener stöds. Ange djupet för mått från olika djup. Till exempel soil_moisture_15cm. Det här namnet måste vara konsekvent med telemetri-data  |
|          SensorMeasures > datatype       |Datatyp för telemetri. För närvarande stöds Double|
|    sensorMeasures > typ    |Typ av mått för sensorer för telemetri. Följande är systemdefinierade typer: AmbientTemperature, CO2, djup, ElectricalConductivity, LeafWetness, length, LiquidLevel, nitrat, O2, PH, fosfat, PointInTime, kalium, press, RainGauge, RelativeHumidity, salinity, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, volym, WindDirection, WindRun, WindSpeed, evapotranspiration, parivärde. Mer information finns i/ExtendedType-API: et.|
|        SensorMeasures > enhet              | Enhet för data för sensor telemetri. Följande är systemdefinierade enheter: nounit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, kvicksilver, PSI, MilliMeter, CentiMeter, meter, tum, fötter, mil, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, examen, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, procent, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, sekunder, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour för att lägga till mer, se/ ExtendedType-API.|
|    SensorMeasures > aggregationType    |  Värdena kan vara ingen, genomsnitt, högsta, lägsta eller StandardDeviation  |
|          Namn            | Namn för att identifiera resursen. Till exempel modell namn/produkt namn.  |
|    Beskrivning        | Ange en meningsfull beskrivning av modellen  |
|   Egenskaper       |  Ytterligare egenskaper från tillverkaren  |
|    **Mäta**      |          |
| HardwareId          |   Unikt ID för sensorn som angetts per tillverkare |
|  SensorModelId     |    ID för associerad sensor modell   |
| location          |  Sensor latitud (-90 till + 90)/Longitude (-180 till 180)/Elevation (i meter)|
|   Port > namn        |  Namn och typ för den port som sensorn är ansluten till på enheten. Detta måste vara samma namn som det definieras i enhets modellen. |
|    DeviceID  |    ID för den enhet som sensorn är ansluten till     |
| Namn            |   Namn för att identifiera resursen. Till exempel sensor namn/produkt namn och modell nummer/produkt kod.|
|    Beskrivning      | Ange en meningsfull beskrivning |
|    Egenskaper        |Ytterligare egenskaper från tillverkaren |

Mer information om objekt finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**API-begäran för att skapa metadata**

Om du vill göra en API-begäran kombinerar du HTTP-metoden (POST), URL: en till API-tjänsten, URI: n till en resurs att fråga, skickar data för att skapa eller ta bort en begäran och lägger till en eller flera HTTP-begärandehuvuden. URL: en till API-tjänsten är API-slutpunkten, dvs. URL: en för data hubben (https://<yourdatahub>. azurewebsites.net)  

**Autentisering**:

FarmBeats-datahubben använder Bearer-autentisering, som behöver följande autentiseringsuppgifter som vi skapade i avsnittet ovan.

- Klientorganisations-ID
- Klient hemlighet
- Klient-ID:t  

Med hjälp av ovanstående autentiseringsuppgifter kan anroparen begära en åtkomsttoken som måste skickas i efterföljande API-begäranden i rubrik avsnittet enligt följande:

headers = *{"auktorisering": "Bearer" + access_token,...}*

**Rubriker för http-begäran**:

Här är de vanligaste begärandehuvuden som måste anges när du gör ett API-anrop till FarmBeats-data hubben:

- Innehålls typ: Application/JSON
- Auktorisering: innehavare < åtkomst-token >
- Acceptera: Application/JSON

**Ange nytto last för att skapa metadata**:

**DeviceModel**


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

Device
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
Nedanstående exempel förfrågan är att skapa en enhet (detta har en indataport som nytto last med begär ande texten).  

```
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "
{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\",}"*
```

> [!NOTE]
> API: erna returnerar unika ID: n för varje instans som skapats. Du måste behålla ID: na för att skicka motsvarande telemetri meddelanden.

**Skicka telemetri**

Nu när du har skapat enheterna och sensorer i FarmBeats kan du skicka de associerade telemetri-meddelandena.  

**Skapa telemetri-klient**

Du måste skicka telemetri till Azure Event Hub för bearbetning. Azure EventHub är en tjänst som gör det möjligt att använda real tids data (telemetri) från anslutna enheter och program. Om du vill skicka telemetridata till FarmBeats måste du skapa en klient som skickar meddelanden till en Event Hub i FarmBeats. Mer information om hur du skickar telemetri finns i [Azure Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

**Skicka telemetri-meddelande som klienten**

När du har upprättat en anslutning som en EventHub-klient kan du skicka meddelanden till EventHub som en JSON.  
Konvertera det historiska sensor data formatet till ett kanoniskt format som Azure-FarmBeats förstår. Det kanoniska meddelande formatet är som följer:  


 ```
  {   
      “deviceid”: “<id of the Device created>”,   
      "timestamp": "<timestamp in ISO 8601 format>",     
      "version" : "1",   
      "sensors":
      [     
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


När du har lagt till motsvarande enheter och sensorer hämtar du DeviceID och sensorid i telemetri-meddelandet, enligt beskrivningen i föregående avsnitt

Exempel på telemetri:


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


## <a name="next-steps"></a>Nästa steg

Mer information om REST API-baserad integrerings information finns [REST API](references-for-farmbeats.md#rest-api).
