---
title: Importera/exportera för Azure IoT Hub-enhetsidentiteter | Microsoft Docs
description: Hur du använder tjänsten Azure IoT SDK för att utföra massåtgärder mot identitetsregistret för att importera och exportera enhetsidentiteter. Importåtgärder kan du skapa, uppdatera och ta bort enhetsidentiteter gruppvis.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: aedf2d0012f5af8ea2eb8e944f06b20c7f1a6bb8
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "42059212"
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Hantera din IoT Hub-enhetsidentiteter i grupp

Varje IoT-hubb har ett identitetsregister som du kan använda för att skapa resurser för varje enhet i tjänsten. ID-registret kan du styra åtkomsten till enheten-slutpunkter. Den här artikeln beskriver hur du importera och exportera enhetsidentiteter gruppvis till och från ett identitetsregister.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Import och export-åtgärder sker i samband med *jobb* som gör att du kan köra massåtgärder mot en IoT-hubb med tjänsten.

Den **RegistryManager** klassen innehåller den **ExportDevicesAsync** och **ImportDevicesAsync** metoder som använder den **jobbet** framework. Dessa metoder kan du exportera, importera och synkronisera i sin helhet en IoT hub-identitetsregistret.

Det här avsnittet beskrivs med hjälp av den **RegistryManager** klass och **jobbet** system att genomföra bulk import och export av enheter till och från en IoT-hubbens identitetsregister. Du kan också använda Azure IoT Hub Device Provisioning-tjänsten för att aktivera zero-touch, just-in-time-etablering till en eller flera IoT-hubbar utan mänsklig inblandning. Mer information finns i den [provisioning service-dokumentationen](/azure/iot-dps).


## <a name="what-are-jobs"></a>Vad är jobb?

Identitetsregisteråtgärder använder den **jobbet** system när åtgärden:

* Har en lång körningstid jämfört med standard körning-åtgärder.

* Returnerar en stor mängd data för användaren.

I stället för ett enda API-anrop väntar eller spärr på resultatet av åtgärden åtgärden asynkront skapar en **jobbet** för den IoT-hubben. Den åtgärd och sedan omedelbart returnerar en **JobProperties** objekt.

I följande C#-kodavsnitt visar hur du skapar ett exportjobb:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Du använder den **RegistryManager** klassen i C#-kod, lägga till den **Microsoft.Azure.Devices** NuGet-paketet i projektet. Den **RegistryManager** undervisningen har börjat den **Microsoft.Azure.Devices** namnområde.

Du kan använda den **RegistryManager** klassen för att fråga efter tillståndet för den **jobbet** med hjälp av den returnerade **JobProperties** metadata. Skapa en instans av den **RegistryManager** klass, använda den **CreateFromConnectionString** metod.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Att hitta anslutningssträngen för din IoT-hubb i Azure portal:

- Gå till IoT-hubben.

- Välj **principer för delad åtkomst**.

- Välj en princip, med hänsyn till de behörigheter som du behöver.

- Kopiera connectionstring från panelen till höger på skärmen.

I följande C#-kodavsnitt visar hur du avsöker var femte sekund om du vill se om jobbet har slutförts kör:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Exportera enheter

Använd den **ExportDevicesAsync** metod för att exportera hela en IoT hub-identitetsregistret till en [Azure Storage](../storage/index.yml) blob behållaren med hjälp av en [signatur för delad åtkomst](../storage/common/storage-security-guide.md#data-plane-security).

Den här metoden kan du skapa pålitliga säkerhetskopieringar av din enhetsinformation i en blob-behållare som du bestämmer.

Den **ExportDevicesAsync** kräver två parametrar:

* En *sträng* som innehåller en URI för en blob-behållare. URI: N måste innehålla en SAS-token som ger skrivåtkomst till behållaren. Jobbet skapar en blockblob i den här behållaren att lagra enhetsdata serialiserade export. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* En *booleskt* -värde som anger om du vill undanta autentiseringsnycklar från dina data för export. Om **FALSKT**, autentiseringsnycklar ingår i exportutdata. I annat fall nycklar exporteras som **null**.

I följande C#-kodavsnitt visar hur du startar ett exportjobb som innehåller enheten autentiseringsnycklar i exporterade data och sedan söka efter slutförande:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Jobbet lagrar sina utdata i angivna blob-behållaren som en blockblob med namnet **devices.txt**. Utdata består av JSON-serialiserat enhetsdata, med en enhet per rad.

I följande exempel visar utdata:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Om en enhet har twin data, exporteras twin data också tillsammans med enhetsdata. I följande exempel visas det här formatet. Alla data från raden ”twinETag” tills slutet är twin data.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Om du behöver åtkomst till dessa data i koden kan du enkelt kan deserialiseras data med den **ExportImportDevice** klass. I följande C#-kodavsnitt visar hur du kan läsa enhetsinformation som tidigare har exporterats till en blockblob:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Importera enheter

Den **ImportDevicesAsync** -metod i den **RegistryManager** klassen kan du utföra massåtgärder för import och synkronisering i en IoT hub-identitetsregistret. Som den **ExportDevicesAsync** metoden den **ImportDevicesAsync** metoden används den **jobbet** framework.

Var noga med hjälp av den **ImportDevicesAsync** metoden eftersom förutom att etablera nya enheter i din identitetsregister, kan den också uppdatera och ta bort befintliga enheter.

> [!WARNING]
> En importåtgärden kan inte ångras. Säkerhetskopiera alltid dina befintliga data med hjälp av den **ExportDevicesAsync** metod till en annan blob-behållare innan du gör bulk ändras till din identitetsregistret.

Den **ImportDevicesAsync** metoden har två parametrar:

* En *sträng* som innehåller en URI för en [Azure Storage](../storage/index.yml) blobbehållare som ska användas som *inkommande* för jobbet. URI: N måste innehålla en SAS-token som ger läsbehörighet till behållaren. Den här behållaren måste innehålla en blob med namnet **devices.txt** som innehåller serialiserade enhetsdata för att importera till din identitetsregistret. Importera data måste innehålla enhetsinformation i samma JSON-format som den **ExportImportDevice** jobbet använder när den skapar en **devices.txt** blob. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* En *sträng* som innehåller en URI för en [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) blobbehållare som ska användas som *utdata* från jobbet. Jobbet skapar en blockblob i den här behållaren lagrar några fel från den slutförda importen **jobbet**. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> De två parametrarna kan peka på samma blob-behållaren. Parametrarna separat kan du bara aktivera mer kontroll över dina data eftersom utdatabehållaren kräver ytterligare behörighet.

I följande C#-kodavsnitt visar hur du startar ett importjobb:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Den här metoden kan också användas för import av data för enhetstvillingen. Formatet för indata är detsamma som visas i den **ExportDevicesAsync** avsnittet. På så sätt kan importera du den exporterade data. Den **$metadata** är valfritt.

## <a name="import-behavior"></a>Importera beteende

Du kan använda den **ImportDevicesAsync** metod för att utföra följande massåtgärder i identity-registry:

* Massinläsning registrering av nya enheter
* Massinläsning borttagningar av befintliga enheter
* Massregistrera statusändringar (aktivera eller inaktivera enheter)
* Massinläsning tilldelning av nya nycklar för autentisering av enheten
* Massinläsning automatisk-omgenerering av enheten autentiseringsnycklar
* Massuppdatering av twin data

Du kan utföra olika kombinationer av föregående åtgärder i en enda **ImportDevicesAsync** anropa. Du kan till exempel registrera nya enheter och ta bort eller uppdatera befintliga enheter på samma gång. När de används tillsammans med den **ExportDevicesAsync** metod, du kan migrera alla dina enheter från en IoT hub helt till en annan.

Om importfilen innehåller twin metadata, sedan dessa metadata skriver över befintliga twin metadata. Om importfilen inte innehåller twin metadata, sedan endast de `lastUpdateTime` metadata har uppdaterats med den aktuella tiden.

Använd det valfria **ImportMode %** -egenskapen i serialiseringsdata importera för varje enhet att styra import processen per enhet. Den **ImportMode %** egenskapen har följande alternativ:

| ImportMode % | Beskrivning |
| --- | --- |
| **createOrUpdate** |Om en enhet inte finns med det angivna **id**, den nyligen har registrerats. <br/>Om enheten redan befintlig information skrivs över med den angivna indatan utan gäller till den **ETag** värde. <br> Användaren kan du ange twin data tillsammans med enhetsdata. Den läsningen etag bearbetas om anges separat från enhetens etag. Om det finns ett matchningsfel med den befintliga läsningen etag, skrivs ett fel till loggfilen. |
| **skapa** |Om en enhet inte finns med det angivna **id**, den nyligen har registrerats. <br/>Om enheten redan finns skrivs ett fel till loggfilen. <br> Användaren kan du ange twin data tillsammans med enhetsdata. Den läsningen etag bearbetas om anges separat från enhetens etag. Om det finns ett matchningsfel med den befintliga läsningen etag, skrivs ett fel till loggfilen. |
| **Uppdatera** |Om det finns redan en enhet med det angivna **id**, befintlig information skrivs över med den angivna indatan utan gäller till den **ETag** värde. <br/>Om enheten inte finns skrivs ett fel till loggfilen. |
| **updateIfMatchETag** |Om det finns redan en enhet med det angivna **id**, befintlig information skrivs över med de angivna indata endast om det finns en **ETag** matchar. <br/>Om enheten inte finns skrivs ett fel till loggfilen. <br/>Om det finns en **ETag** matchningsfel, ett fel skrivs till loggfilen. |
| **createOrUpdateIfMatchETag** |Om en enhet inte finns med det angivna **id**, den nyligen har registrerats. <br/>Om enheten redan befintlig information skrivs över med de angivna indata endast om det finns en **ETag** matchar. <br/>Om det finns en **ETag** matchningsfel, ett fel skrivs till loggfilen. <br> Användaren kan du ange twin data tillsammans med enhetsdata. Den läsningen etag bearbetas om anges separat från enhetens etag. Om det finns ett matchningsfel med den befintliga läsningen etag, skrivs ett fel till loggfilen. |
| **ta bort** |Om det finns redan en enhet med det angivna **id**, tas den bort utan avser den **ETag** värde. <br/>Om enheten inte finns skrivs ett fel till loggfilen. |
| **deleteIfMatchETag** |Om det finns redan en enhet med det angivna **id**, raderas bara om det finns en **ETag** matchar. Om enheten inte finns skrivs ett fel till loggfilen. <br/>Om det finns ett ETag-Typfel, skrivs ett fel till loggfilen. |

> [!NOTE]
> Om serialiseringsdata inte explicit definiera en **ImportMode %** flaggan för en enhet, den standard **createOrUpdate** under importen.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importera enheter-exempel – bulk enhetsetablering

I följande C#-kodexempel illustrerar hur du skapar flera enhetsidentiteter som:

* Inkludera autentiseringsnycklar.
* Skriva den enhetsinformationen till en blockblob.
* Importera enheterna till identitetsregistret.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Importera enheter exempel – massborttagning

Följande kodexempel visar hur du tar bort de enheter som du lade till med ovanstående kodexempel:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Hämta SAS-URI

Följande kodexempel visar hur du skapar en [SAS-URI](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) med läsa, skriva och ta bort behörigheter för en blob-behållare:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att utföra massåtgärder mot identitetsregistret i IoT-hubb. Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Utforska använder IoT Hub Device Provisioning-tjänsten för att aktivera zero-touch och just-in-time-etablering, se: 

* [Azure IoT Hub Device Provisioning-tjänst](/azure/iot-dps)
