---
title: Exportera dina data i Azure IoT Central | Microsoft Docs
description: Exportera data från Azure IoT Central-programmet
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/18/2018
ms.topic: article
ms.service: azure-iot-central
manager: peterpr
ms.openlocfilehash: a1a7e6a62a88057cc8bc512a0c46de79a55ccd53
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368145"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportera dina data i Azure IoT Central

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen löpande export i Azure IoT Central att regelbundet exporterar data till Azure Blob storage-kontot. Du kan exportera **mätningar av**, **enheter**, och **enheten mallar** med den [Apache AVRO](https://avro.apache.org/docs/current/index.html) format. Exporterade data kan användas för analys av kalla sökvägen som utbildning modeller i Azure Machine Learning eller långsiktig trendanalys i Microsoft Power BI.

> [!Note]
> När du aktiverar löpande dataexport, Hämta endast data från det ögonblick då och uppåt. För närvarande går inte att hämta data under en tid när löpande dataexport var inaktiverat. Om du vill behålla fler historiska data, aktivera löpande dataexport tidigt.

## <a name="prerequisites"></a>Förutsättningar

- En utökad 30-dagars utvärderingsversion IoT Central eller en betald program.
- Ett Azure-konto med en Azure-prenumeration.
- Samma Azure-konto är en administratör i din IoT Central-App.
- Samma Azure-konto har behörighet att skapa ett lagringskonto eller få åtkomst till ett befintligt lagringskonto i samma Azure-prenumeration.

## <a name="types-of-data-to-export"></a>Typer av data som ska exporteras

### <a name="measurements"></a>Mått

De mått som enheterna skickar exporteras till ditt storage-konto en gång per minut. Data har alla nya meddelanden som tas emot av IoT Central från alla enheter under den tiden. De exporterade AVRO-filerna använder samma format som meddelandefiler som exporteras av [IoT Hub meddelanderoutning](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) till Blob storage.

> [!NOTE]
> De enheter som skickar mått som representeras av enhets-ID (se nedan). Exportera ögonblicksbilder för enheten för att hämta namnen på enheterna. Korrelera varje meddelande-post med hjälp av den **connectionDeviceId** som matchar den **deviceId** av enheten.

I följande exempel visas en post i en avkodade AVRO-fil:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Enheter

När löpande dataexport aktiveras först, exporteras en enda ögonblicksbild med alla enheter. Varje enhet innehåller:
- `id` för enheten i IoT Central
- `name` enhetens
- `deviceId` från [Device Provisioning-tjänst](https://aka.ms/iotcentraldocsdps)
- Mallen enhetsinformation
- Egenskapsvärden
- Inställningsvärden

En ny ögonblicksbild skrivs en gång per minut. Ögonblicksbilden innehåller:

- Nya enheter som har lagts till sedan den senaste ögonblicksbilden.
- Enheter med ändrade egenskapen och ange värden för sedan den senaste ögonblicksbilden.

> [!NOTE]
> Enheter som har tagits bort sedan den senaste ögonblicksbilden inte exporteras. Ögonblicksbilder har för närvarande inte indikatorer för enheter som har tagits bort.
>
> Mallen enhet som varje enhet som tillhör representeras av en enhet mall-ID. Exportera mall-ögonblicksbilder enheten för att hämta namnet på mallen för enheten.

En post i filen avkodade AVRO kan se ut:

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
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

När löpande dataexport aktiveras först, exporteras en enda ögonblicksbild med alla mallar för enheten. Varje enhet-mall innehåller:
- `id` för mall för enhet
- `name` för mall för enhet
- `version` för mall för enhet
- Datatyper för mätning och min/max-värden.
- Egenskapen datatyper och standardvärden.
- Ställa in datatyper och standardvärden.

En ny ögonblicksbild skrivs en gång per minut. Ögonblicksbilden innehåller:

- Ny enhet mallar har lagts till sedan den senaste ögonblicksbilden.
- Enheten mallar med ändrade mått, egenskap och inställningen definitioner eftersom den senaste ögonblicksbilden.

> [!NOTE]
> Enhet-mallar som har tagits bort sedan den senaste ögonblicksbilden exporteras inte. Ögonblicksbilder har för närvarande inte indikatorer för borttagna mallar.

En post i filen avkodade AVRO kan se ut så här:

```json
{
    "id": "<id>",
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

## <a name="set-up-continuous-data-export"></a>Konfigurera löpande dataexport

1. Om du inte har ett Azure storage-konto [skapa ett nytt lagringskonto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) i Azure-portalen. Skapa lagringskontot **i Azure-prenumeration som har programmet IoT Central**.
    - Kontotyp, Välj **generella** eller **Blob-lagring**.
    - Välj den prenumeration som har programmet IoT Central. Om du inte ser prenumerationen, kan du behöva logga in på en annan Azure-konto eller begär åtkomst till prenumerationen.
    - Välj en befintlig resursgrupp eller skapa en ny. Lär dig mer om [så här skapar du ett nytt lagringskonto](https://aka.ms/blobdocscreatestorageaccount).

2. Skapa en behållare i ditt storage-konto för att exportera dina IoT Central-data. Gå till ditt lagringskonto. Under **Blobtjänsten**väljer **Bläddra efter Blobar**. Välj **behållare** att skapa en ny behållare.

   ![Skapa en container](media/howto-export-data/createcontainer.png)

3. Logga in på ditt IoT Central-program med hjälp av samma Azure-konto.

4. Under **Administration**väljer **dataexport**.

5. I den **lagringskonto** listrutan väljer du ditt lagringskonto. I den **behållare** listrutan väljer du din behållare. Under **Data som ska exporteras**, ange varje typ av data som ska exporteras genom att ställa in typen **på**.

6. Om du vill aktivera löpande dataexport ange **dataexport** till **på**. Välj **Spara**.

  ![Konfigurera löpande dataexport](media/howto-export-data/continuousdataexport.PNG)

7. Efter ett par minuter visas dina data i ditt storage-konto. Bläddra till ditt lagringskonto. Välj **Bläddra efter blobar** > din behållare. Du kan se tre mappar för exporterade data. Standardsökvägarna för AVRO-filerna med exporterade data är:
    - Meddelanden: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
    - Enheter: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
    - Mallar för enheten: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

## <a name="read-exported-avro-files"></a>Läs exporterade AVRO-filer

AVRO är ett binärt format, så inte går att läsa filerna i raw-tillståndet. Filerna kan avkodas till JSON-format. I följande exempel visas hur du Parsar den mått, enheter och enheten mallar AVRO-filer. Exemplen motsvarar de exempel som beskrivs i föregående avsnitt.

### <a name="read-avro-files-by-using-python"></a>Läsa AVRO-filer med hjälp av Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Installera pandas och pandavro-paketet

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Tolka en mätningar av AVRO-fil

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>Tolka en enheter AVRO-fil

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>Tolka en enhet mallar AVRO-fil

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Läs AVRO-filer med hjälp av C#

#### <a name="install-the-microsofthadoopavro-package"></a>Installera paketet Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Tolka en mätningar av AVRO-fil

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
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>Tolka en enheter AVRO-fil

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
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

#### <a name="parse-a-device-templates-avro-file"></a>Tolka en enhet mallar AVRO-fil

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
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

### <a name="read-avro-files-by-using-javascript"></a>Läsa AVRO-filer med hjälp av Javascript

#### <a name="install-the-avsc-package"></a>Installera paketet avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Tolka en mätningar av AVRO-fil

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Tolka en enheter AVRO-fil

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Tolka en enhet mallar AVRO-fil

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du exporterar dina data kan du fortsätta till nästa steg:

> [!div class="nextstepaction"]
> [Så här att visualisera dina data i Power BI](howto-connect-powerbi.md)
