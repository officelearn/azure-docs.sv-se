---
title: Exportera dina data i Azure IoT Central | Microsoft Docs
description: Exportera data från Azure IoT Central-programmet
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 6d35e3cfefcefef0b4ff40364cbdab92d486b769
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008813"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportera dina data i Azure IoT Central

Använda löpande Export av Data för att regelbundet exportera data till Azure Blob Storage-kontot. Välja att exportera **mätningar av**, **enheter**, och **enheten mallar** i filer av [Apache AVRO](https://avro.apache.org/docs/current/index.html) format. Använd exporterade data för analys av kalla sökvägen, till exempel utbildning modeller i Azure Machine Learning eller långsiktig trendanalys i Power BI.

> [!Note]
> När du aktiverar löpande Export av Data kan hämta du bara de data som kommer in från det ögonblick då och senare. Det finns för närvarande inget sätt att hämta data från när löpande Export av Data har inaktiverats. Aktivera löpande dataexport tidigt att behålla fler historiska data!

## <a name="prerequisites"></a>Förutsättningar

- En utökad 30-dagars utvärderingsversion app eller en betald app
- Azure-konto med Azure-prenumeration
- Samma Azure-konto är en administratör i din IoT Central-app
- Samma Azure-konto har behörighet att skapa ett lagringskonto eller öppna ett befintligt lagringskonto i samma Azure-prenumeration

## <a name="types-of-data-to-export"></a>Typer av data som ska exporteras

### <a name="measurements"></a>Mått

De mått som enheterna skickar exporteras till ditt lagringskonto. Mätning av faktisk användning data exporteras ungefär en gång i minuten, som innehåller alla nya meddelanden som har tagits emot av IoT Central från alla enheter inom den tidsperioden. De exporterade AVRO-filerna finns i samma format som meddelanden-filer som exporteras av [IoT Hub meddelanderoutning](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) till blob storage.

> [!NOTE]
> De enheter som skickas mått som representeras av enhets-ID (se nedan). Om du vill hämta namnen på enheterna som du behöver exportera enheter ögonblicksbilder för. Du kan jämföra varje meddelande-post med hjälp av connectionDeviceId som matchar ID: t för enheten.

Det här är ett exempel på en post i avkodade AVRO-filen.

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Enheter

När du först aktivera löpande Export av Data exporteras en enda ögonblicksbild som innehåller alla enheter. Det här omfattar:
- Enhets-ID
- Enhetsnamn
- Enheten mall-ID: N
- Egenskapsvärden
- Inställningsvärden

Ungefär en gång i minuten, skrivs en ny ögonblicksbild som innehåller:

- De nya enheterna som har lagts till sedan den senaste ögonblicksbilden
- Enheter som har värden för egenskaper och inställningar som ändrats sedan den senaste ögonblicksbilden

> [!NOTE]
> Enheter som har tagits bort sedan den senaste ögonblicksbilden exporteras inte. Det finns ingen indikator i ögonblicksbilder för enheter som har tagits bort just nu.

> [!NOTE]
> Mallen enhet som varje enhet som tillhör representeras av en enhet mall-ID. För att hämta namnet på mallen enheten, måste du exportera enheten mall ögonblicksbilder för.

Varje post i filen avkodade AVRO ser ut så här:

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Enheten mallar

När du först aktivera löpande Export av Data exporteras en enda ögonblicksbild som innehåller alla mallar för enheten. Det här omfattar: 
- Enheten mall-ID: N
- Datatyper för mätning och min/max-värden
- Egenskaper för-datatyper och standardvärden
- Typer av inställningar och standardvärden

Ungefär en gång i minuten, skrivs en ny ögonblicksbild som innehåller:

- De nya mallarna för enheter som har lagts till sedan den senaste ögonblicksbilden
- Enhet-mallar som hade mått, egenskaper och inställningar definitioner som ändrats sedan den senaste ögonblicksbilden

> [!NOTE]
> Enhet-mallar som har tagits bort sedan den senaste ögonblicksbilden exporteras inte. Det finns inte indikatorn i ögonblicksbilder för enhet-mallar som har tagits bort just nu.

Varje post i filen avkodade AVRO ser ut så här:

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="how-to-set-up-data-export"></a>Hur du ställer in export av data

1. Om du inte redan har en, skapa ett Azure Storage-konto **i Azure-prenumeration som din app i**. [Klicka här](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) att hoppa till Azure portal för att skapa ett nytt Azure Storage-konto.

    - Välj *generella* eller *Blob-lagring* konto typer
    - Välj den prenumeration som IoT Central appen är i. Om du inte ser prenumerationen, kan du behöva logga in på en annan Azure-konto eller be om åtkomst till prenumerationen.
    - Du kan välja en befintlig resursgrupp eller skapa en ny. Lär dig mer om [så här skapar du ett nytt lagringskonto.](https://aka.ms/blobdocscreatestorageaccount)

2. Skapa en behållare i ditt Storage-konto för att exportera data till IoT Central. Gå till ditt lagringskonto -> Bläddra efter Blobar och skapa en ny behållare. ![Skapa en behållaravbildning](media/howto-export-data/createcontainer.png)

3. Logga in på ditt IoT Central-program med samma Azure-konto.
1. Gå till Administration -> löpande dataexport.
![IoT Central CDE](media/howto-export-data/continuousdataexport.PNG)
1. Med hjälp av listrutorna, Välj ditt lagringskonto och en behållare. Sedan använder du knapparna för att aktivera eller inaktivera de olika typerna av data som ska exporteras.
1. Slutligen aktivera kontinuerlig Data exportera med hjälp av växlingsknappen och tryck på ”Spara”.
1. Vänta några minuter och du bör se dina data som visas i ditt Storage-konto. Du kan navigera till ditt Storage-konto, Välj Bläddra blobar, din behållare och ser du tre mappar. Standardsökvägarna för AVRO-filer som innehåller olika typer av data är:
- Meddelanden: **{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Enheter: **{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Enhet-mallar: **{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>Läsa exporterade AVRO-filer

AVRO är ett binärt format, så inte går att läsa filerna i raw-tillståndet. De kan att avkoda till JSON-format. I följande exempel visas hur du Parsar den mått, enheter och enheten mallar AVRO-filer med hjälp av exemplen ovan.

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Installera Pandas och PandaAvro paketet:

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>Parsa AVRO-filen för mätning av faktisk användning

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>Parsa AVRO-filen för enheter

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>Parsa AVRO enhetsfil-mallar

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Installera Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>Parsa AVRO-filen för mätning av faktisk användning

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

### <a name="parse-devices-avro-file"></a>Parsa AVRO-filen för enheter

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```
### <a name="parse-device-templates-avro-file"></a>Parsa AVRO enhetsfil-mallar

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

## <a name="javascript"></a>Javascript

### <a name="install-avsc"></a>Installera avsc

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>Parsa AVRO-filen för mätning av faktisk användning

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>Parsa AVRO-filen för enheter

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>Parsa AVRO enhetsfil-mallar

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hantera dina enheter](howto-manage-devices.md) i enhetsutforskaren. 
