---
title: Exportera data till Azure Blob Storage | Microsoft Docs
description: Exportera data från Azure IoT Central programmet till Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: f81ca34931e2ee4bce35fa06195fb64c47ef9a7b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682036"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exportera data till Azure Blob Storage

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen löpande export i Azure IoT Central regelbundet exportera data till din **Azure Blob storage-konto**. Du kan exportera **mätningar av**, **enheter**, och **enheten mallar** till filer i Apache Avro-format. Exporterade data kan användas för analys av kalla sökvägen som utbildning modeller i Azure Machine Learning eller långsiktig trendanalys i Microsoft Power BI.

> [!Note]
> Igen när du aktiverar löpande dataexport, får du endast data från det ögonblick då och uppåt. För närvarande går inte att hämta data under en tid när löpande dataexport var inaktiverat. Om du vill behålla fler historiska data, aktivera löpande dataexport tidigt.


## <a name="prerequisites"></a>Nödvändiga komponenter

- Du måste vara administratör i din IoT Central-App


## <a name="set-up-export-destination"></a>Konfigurera exportera

Följ dessa steg om du inte har ett befintligt lagringsutrymme för att exportera till:

## <a name="create-storage-account"></a>Skapa lagringskonto

1. Skapa en [nytt lagringskonto i Azure-portalen](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan läsa mer i [Azure Storage-docs](https://aka.ms/blobdocscreatestorageaccount).
2. Kontotyp, Välj **generella** eller **Blob-lagring**.
3. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som är **inte samma** som programmets användningsbaserad IoT Central. Du ansluter med en anslutningssträng i det här fallet.

4. Skapa en behållare i ditt storage-konto. Gå till ditt lagringskonto. Under **Blobtjänsten**väljer **Bläddra efter Blobar**. Välj **+ behållare** överst för att skapa en ny behållare


## <a name="set-up-continuous-data-export"></a>Konfigurera löpande dataexport

Nu när du har en lagringsplats för att exportera data till följer du dessa steg för att konfigurera löpande dataexport. 

1. Logga in på ditt IoT Central-program.

2. I den vänstra menyn väljer du **löpande Export av Data**.

    > [!Note]
    > Om du inte ser löpande Export av Data på den vänstra menyn kan är du inte administratör i din app. Kontakta en administratör att ställa in export av data.

    ![Skapa ny cde Event Hub](media/howto-export-data/export_menu.PNG)

3. Välj den **+ ny** knappen uppe till höger. Välj **Azure Blob Storage** som mål för exporten. 

    > [!NOTE] 
    > Det maximala antalet exporter per app är fem. 

    ![Skapa ny löpande dataexport](media/howto-export-data/export_new.PNG)

4. I den nedrullningsbara listrutan väljer du din **Lagringskonto namnområde**. Du kan också välja alternativet sist i listan som **ange en anslutningssträng**. 

    > [!NOTE] 
    > Du ser bara Lagringskonton namnområden i den **samma prenumeration som din IoT Central-app**. Om du vill exportera till ett mål utanför den här prenumerationen kan du välja **ange en anslutningssträng** och finns i steg 5.

    > [!NOTE] 
    > 7 dagars utvärderingsversion appar, det enda sättet att konfigurera kontinuerlig data exportera är i via en anslutningssträng. Det beror på att 7 dagars utvärderingsversion appar inte har en associerad Azure-prenumeration.

    ![Skapa ny cde Event Hub](media/howto-export-data/export-create-blob.png)

5. (Valfritt) Om du har valt **ange en anslutningssträng**, en ny ruta visas där du kan klistra in anslutningssträngen. Att hämta anslutningssträngen för din:
    - Storage-konto, gå till lagringskontot i Azure-portalen.
        - Under **inställningar**väljer **åtkomstnycklar**
        - Kopiera anslutningssträngen key1 och key2-anslutningssträng
 
6. Välj en behållare i nedrullningsbara listrutan.

7. Under **Data som ska exporteras**, ange varje typ av data som ska exporteras genom att ställa in typen **på**.

6. Se till att aktivera löpande dataexport genom **dataexport** är **på**. Välj **Spara**.

  ![Konfigurera löpande dataexport](media/howto-export-data/export-list-blob.png)

7. Efter ett par minuter visas dina data i ditt valda mål.


## <a name="export-to-azure-blob-storage"></a>Exportera till Azure Blob Storage

Mått, enheter och mallar enhetsdata exporteras till ditt storage-konto en gång per minut, med varje fil som innehåller batch med ändringar sedan senast exporterade filen. Exporterade data är i [Apache Avro](https://avro.apache.org/docs/current/index.html) formatera och kommer att exporteras i till tre mappar. Standardsökvägarna i ditt storage-konto är:
- Meddelanden: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Enheter: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Mallar för enheten: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

### <a name="measurements"></a>Mått

Exporterade mätning av faktisk användning har alla nya meddelanden som tas emot av IoT Central från alla enheter under den tiden. De exporterade filerna använder samma format som meddelandefiler som exporteras av [IoT Hub meddelanderoutning](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) till Blob storage.

> [!NOTE]
> De enheter som skickar mått som representeras av enhets-ID (se nedan). Exportera ögonblicksbilder för enheten för att hämta namnen på enheterna. Korrelera varje meddelande-post med hjälp av den **connectionDeviceId** som matchar den **deviceId** av enheten.

I följande exempel visas en post i en avkodade Avro-fil:

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

En post i filen avkodade Avro kan se ut:

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

En post i filen avkodade Avro kan se ut så här:

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

## <a name="read-exported-avro-files"></a>Läs exporterade Avro-filer

Avro är ett binärt format, så inte går att läsa filerna i raw-tillståndet. Filerna kan avkodas till JSON-format. I följande exempel visas hur du Parsar den mått, enheter och enheten mallar Avro-filer. Exemplen motsvarar de exempel som beskrivs i föregående avsnitt.

### <a name="read-avro-files-by-using-python"></a>Läsa Avro-filer med hjälp av Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Installera pandas och pandavro-paketet

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Tolka en mätningar av Avro-fil

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
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

#### <a name="parse-a-devices-avro-file"></a>Tolka en enheter Avro-fil

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
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

#### <a name="parse-a-device-templates-avro-file"></a>Tolka en enhet mallar Avro-fil

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
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

### <a name="read-avro-files-by-using-c"></a>Läs Avro-filer med hjälp avC#

#### <a name="install-the-microsofthadoopavro-package"></a>Installera paketet Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Tolka en mätningar av Avro-fil

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
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>Tolka en enheter Avro-fil

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

#### <a name="parse-a-device-templates-avro-file"></a>Tolka en enhet mallar Avro-fil

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

### <a name="read-avro-files-by-using-javascript"></a>Läsa Avro-filer med hjälp av Javascript

#### <a name="install-the-avsc-package"></a>Installera paketet avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Tolka en mätningar av Avro-fil

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
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

#### <a name="parse-a-devices-avro-file"></a>Tolka en enheter Avro-fil

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
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

#### <a name="parse-a-device-templates-avro-file"></a>Tolka en enhet mallar Avro-fil

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
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
