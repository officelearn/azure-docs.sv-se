---
title: Exportera data till Azure Blob Storage | Microsoft Docs
description: Så här exporterar du data från ditt Azure IoT Central-program till Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 609d16994cf88f1777584243b1031368ddc79724
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849075"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exportera data till Azure Blob Storage

*Det här avsnittet gäller för administratörer.*

Den här artikeln beskriver hur du använder funktionen för kontinuerlig data export i Azure IoT Central för att regelbundet exportera data till ditt **Azure Blob Storage-konto**. Du kan exportera **mått**, **enheter**och **enhetsspecifika mallar** till filer i Apache Avro-format. Exporterade data kan användas för kall Sök vägs analys som utbildnings modeller i Azure Machine Learning eller långsiktig trend analys i Microsoft Power BI.

> [!Note]
> När du sedan aktiverar kontinuerlig data export får du bara data från det tillfället. För närvarande går det inte att hämta data under en tid då kontinuerliga data exporter ATS. Aktivera kontinuerlig data export tidigt om du vill behålla mer historiska data.


## <a name="prerequisites"></a>Förutsättningar

- Du måste vara administratör i IoT Centrals programmet


## <a name="set-up-export-destination"></a>Konfigurera export mål

Om du inte har en befintlig lagrings enhet att exportera till följer du dessa steg:

## <a name="create-storage-account"></a>Skapa lagrings konto

1. Skapa ett [nytt lagrings konto i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan läsa mer i [Azure Storage dokument](https://aka.ms/blobdocscreatestorageaccount).
2. För konto typen väljer du **generell användning** eller **blob-lagring**.
3. Välj en prenumeration. 

    > [!Note] 
    > Nu kan du exportera data till andra prenumerationer som **inte är samma** som den som du betalar per användning för IoT Central programmet. Du kommer att ansluta med hjälp av en anslutnings sträng i det här fallet.

4. Skapa en behållare i ditt lagrings konto. Gå till ditt lagringskonto. Under **BLOB service**väljer du **Bläddra i blobbar**. Välj **+ behållare** överst för att skapa en ny behållare


## <a name="set-up-continuous-data-export"></a>Konfigurera kontinuerlig data export

Nu när du har ett lagrings mål att exportera data till, följer du dessa steg för att konfigurera kontinuerlig data export. 

1. Logga in på ditt IoT Central-program.

2. På den vänstra menyn väljer du **kontinuerlig data export**.

    > [!Note]
    > Om du inte ser kontinuerliga data export på den vänstra menyn är du inte administratör i din app. Prata med en administratör för att konfigurera data export.

    ![Skapa ny CDE-Händelsehubben](media/howto-export-data/export_menu1.png)

3. Välj knappen **+ ny** längst upp till höger. Välj **Azure Blob Storage** som mål för exporten. 

    > [!NOTE] 
    > Det maximala antalet exporter per app är fem. 

    ![Skapa ny kontinuerlig data export](media/howto-export-data/export_new1.png)

4. I list rutan väljer du ditt **lagrings konto namn område**. Du kan också välja det sista alternativet i listan som **anger en anslutnings sträng**. 

    > [!NOTE] 
    > Du ser bara lagrings konto namn områden i **samma prenumeration som din IoT Central-app**. Om du vill exportera till ett mål utanför den här prenumerationen väljer du **Ange en anslutnings sträng** och se steg 5.

    > [!NOTE] 
    > För 7 dagars test av appar är det enda sättet att konfigurera kontinuerlig data export genom en anslutnings sträng. Detta beror på att sju dagars prov versioner inte har någon tillhör ande Azure-prenumeration.

    ![Skapa ny CDE-Händelsehubben](media/howto-export-data/export-create-blob.png)

5. Valfritt Om du väljer **Ange en anslutnings sträng**visas en ny ruta där du kan klistra in anslutnings strängen. Så här hämtar du anslutnings strängen för din:
    - Lagrings konto går du till lagrings kontot i Azure Portal.
        - Under **Inställningar**väljer du **åtkomst nycklar**
        - Kopiera antingen anslutnings strängen KEY1 eller key2-anslutningssträngen
 
6. Välj en behållare i list rutan.

7. Under **data som ska exporteras**anger du varje typ av data som ska exporteras genom att ange typen till **på**.

6. Om du vill aktivera kontinuerlig data export kontrollerar du att **data exporten** är **på**. Välj **Spara**.

   ![Konfigurera kontinuerlig data export](media/howto-export-data/export-list-blob.png)

7. Efter några minuter visas dina data i det valda målet.


## <a name="export-to-azure-blob-storage"></a>Exportera till Azure Blob Storage

Data för mått, enheter och enhets information exporteras till ditt lagrings konto en gång per minut, med varje fil som innehåller grupp ändringar sedan den senaste exporterade filen. Exporterade data är i [Apache Avro](https://avro.apache.org/docs/current/index.html) -format och kommer att exporteras i till tre mappar. Standard Sök vägarna i ditt lagrings konto är:
- Meddelanden: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Enheter: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Enhets mallar: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

### <a name="measurements"></a>Mått

Exporterade mätnings data har alla nya meddelanden som tagits emot av IoT Central från alla enheter under den tiden. De exporterade filerna använder samma format som de meddelandefiler som exporteras av [IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) meddelanderoutning till Blob Storage.

> [!NOTE]
> De enheter som skickar måtten representeras av enhets-ID: n (se följande avsnitt). Exportera enhetens ögonblicks bilder för att hämta namnen på enheterna. Korrelera varje meddelande post med hjälp av **connectionDeviceId** som matchar enhets Postens **deviceId** .

I följande exempel visas en post i en avkodad avro-fil:

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

När kontinuerlig data export först aktive ras, exporteras en enda ögonblicks bild med alla enheter. Varje enhet innehåller:
- `id`av enheten i IoT Central
- `name`av enheten
- `deviceId`från [enhets etablerings tjänst](https://aka.ms/iotcentraldocsdps)
- Information om enhets mal len
- Egenskapsvärden
- Inställnings värden

En ny ögonblicks bild skrivs en gång per minut. Ögonblicks bilden innehåller:

- Nya enheter har lagts till sedan den senaste ögonblicks bilden.
- Enheter med ändrad egenskap och inställnings värden sedan den senaste ögonblicks bilden.

> [!NOTE]
> Enheter som har tagits bort sedan den senaste ögonblicks bilden exporteras inte. Ögonblicks bilderna har för närvarande inga indikatorer för borttagna enheter.
>
> Enhets mal len som varje enhet tillhör representeras av ett mall-ID för enhet. Exportera enhets mal len ögonblicks bilder för att hämta namnet på enhets mal len.

En post i den avkodade Avro-filen kan se ut så här:

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

### <a name="device-templates"></a>Enhets mallar

När kontinuerlig data export har Aktiver ATS, exporteras en enda ögonblicks bild med alla enhets mallar. Varje enhets mall innehåller:
- `id`av enhets mal len
- `name`av enhets mal len
- `version`av enhets mal len
- Mät data typer och minsta/högsta värden.
- Egenskaps data typer och standardvärden.
- Ange data typer och standardvärden.

En ny ögonblicks bild skrivs en gång per minut. Ögonblicks bilden innehåller:

- Nya enhetsspecifika mallar har lagts till sedan den senaste ögonblicks bilden.
- Enhets mallar med ändrade mått, egenskaper och inställnings definitioner sedan den senaste ögonblicks bilden.

> [!NOTE]
> Enhetsspecifika har tagits bort sedan den senaste ögonblicks bilden inte har exporter ATS. Ögonblicks bilderna har för närvarande inga indikatorer för borttagna enhetsspecifika mallar.

En post i den avkodade Avro-filen kan se ut så här:

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

## <a name="read-exported-avro-files"></a>Läsa exporterade Avro-filer

Avro är ett binärt format, så filerna kan inte läsas i sitt RAW-tillstånd. Filerna kan avkodas till JSON-format. I följande exempel visas hur du kan parsa Avro-filer för mått, enheter och Device templates. Exemplen motsvarar exemplen som beskrivs i föregående avsnitt.

### <a name="read-avro-files-by-using-python"></a>Läsa Avro-filer med hjälp av python

#### <a name="install-pandas-and-the-pandavro-package"></a>Installera Pandas och pandavro-paketet

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Parsa en mått avro-fil

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

#### <a name="parse-a-devices-avro-file"></a>Parsa en enhet avro-fil

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

#### <a name="parse-a-device-templates-avro-file"></a>Parsa en Avro-fil för Device templates

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

### <a name="read-avro-files-by-using-c"></a>Läsa Avro-filer med hjälp avC#

#### <a name="install-the-microsofthadoopavro-package"></a>Installera paketet Microsoft. Hadoop. avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Parsa en mått avro-fil

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

#### <a name="parse-a-devices-avro-file"></a>Parsa en enhet avro-fil

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

#### <a name="parse-a-device-templates-avro-file"></a>Parsa en Avro-fil för Device templates

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

### <a name="read-avro-files-by-using-javascript"></a>Läsa Avro-filer med hjälp av Java Script

#### <a name="install-the-avsc-package"></a>Installera AVSC-paketet

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Parsa en mått avro-fil

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

#### <a name="parse-a-devices-avro-file"></a>Parsa en enhet avro-fil

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

#### <a name="parse-a-device-templates-avro-file"></a>Parsa en Avro-fil för Device templates

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
> [Visualisera dina data i Power BI](howto-connect-powerbi.md)
