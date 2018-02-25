---
title: "Importera export av identiteter för Azure IoT Hub-enhet | Microsoft Docs"
description: "Hur du använder tjänsten Azure IoT SDK för att utföra massåtgärder mot identitetsregistret att importera och exportera enheten identiteter. Importera åtgärder kan du skapa, uppdatera och ta bort enheten identiteter gruppvis."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2ade1494-45ea-46a7-ade7-cf6e11ce62da
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 699237c68258243b5f654f5dc57e616e3a22177a
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Hantera din IoT-hubb enheten identiteter i grupp

Varje IoT-hubben har en identitetsregistret som du kan använda för att skapa per enhet resurser i tjänsten. Identitetsregistret kan du styra åtkomsten till enheter riktade slutpunkter. Den här artikeln beskriver hur du importerar och exporterar enheten identiteter gruppvis till och från en identitetsregistret.

Import och export-åtgärder sker i samband med *jobb* som gör att du kan köra tjänsten massåtgärder mot en IoT-hubb.

Den **RegistryManager** klassen innehåller den **ExportDevicesAsync** och **ImportDevicesAsync** metoder som använder den **jobbet** framework. Dessa metoder kan du exportera, importera och synkronisera identitetsregistret en IoT-hubb i sin helhet.

Det här avsnittet beskrivs med hjälp av den **RegistryManager** klass och **jobbet** systemet att utföra bulk import och export av enheter till och från en IoT-hubb identitetsregistret. Du kan också använda Azure IoT-hubb Device etablering Service för att aktivera zero touch, just-in-time etablering till en eller flera IoT-hubbar utan mänsklig inblandning. Mer information finns i [etablering dokumentation för tjänsten][lnk-dps].

## <a name="what-are-jobs"></a>Vad är jobb?

Identitet registret åtgärderna använder den **jobbet** system när åtgärden:

* Har en lång körningstid jämfört med standardåtgärder för körning.
* Returnerar en stor mängd data för användaren.

I stället för ett enda API-anrop som väntar på eller blockerar på resultatet av åtgärden igen asynkront skapar en **jobbet** för att IoT-hubb. Åtgärden sedan omedelbart returnerar en **JobProperties** objekt.

Följande C# kodfragment visar hur du skapar ett exportjobb:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Att använda den **RegistryManager** klassen i C#-kod, lägga till den **Microsoft.Azure.Devices** NuGet-paket i projektet. Den **RegistryManager** klassen är i den **Microsoft.Azure.Devices** namnområde.

Du kan använda den **RegistryManager** klassen för att fråga efter tillståndet för den **jobbet** med hjälp av den returnerade **JobProperties** metadata. Att skapa en instans av den **RegistryManager** klassen använder den **CreateFromConnectionString** metod:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Att hitta anslutningssträngen för din IoT-hubb i Azure-portalen:

- Gå till din IoT-hubb.
- Välj **principer för delad åtkomst**.
- Välj en princip med hänsyn till de behörigheter som du behöver.
- Kopiera connectionstring från panelen på höger sida av skärmen.

Följande C# kodfragmentet visas hur du avsöker var femte sekund för att se om jobbet har slutförts körs:

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

Använd den **ExportDevicesAsync** metod för att exportera i sin helhet en IoT-hubb identitetsregistret till en [Azure Storage](../storage/index.yml) blob-behållare med en [signatur för delad åtkomst](../storage/common/storage-security-guide.md#data-plane-security).

Den här metoden kan du skapa pålitliga säkerhetskopieringar av din enhetsinformation i en blobbbehållare som du bestämmer.

Den **ExportDevicesAsync** kräver två parametrar:

* En *sträng* som innehåller en URI för en blobbbehållare. Den här URI måste innehålla en SAS-token som ger åtkomst till behållaren. Jobbet skapar en blockblobb i behållaren för lagring av enhetsdata serialiserade export. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

* En *booleskt* som anger om du vill undanta autentiseringsnycklar från exportera data. Om **FALSKT**, autentiseringsnycklar ingår i utdata för export. I annat fall exporteras nycklar som **null**.

Följande C# kodfragment visar hur du initierar ett exportjobb som innehåller enheten autentiseringsnycklar i exporterade data och sedan söka efter slutförande:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

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

Jobbet lagrar utdata i angivna blob-behållaren som en blockblobb med namnet **devices.txt**. Utdata består av JSON serialiserade data på enheten, med en enhet per rad.

I följande exempel visar utdata:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Om en enhet har identiska data, exporteras också identiska data tillsammans med enhetsdata. I följande exempel visas det här formatet. Alla data från raden ”twinETag” ända tills den är identiska data.

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

Om du behöver åtkomst till dessa data i kod kan du enkelt deserialisera data med den **ExportImportDevice** klass. Följande C# kodfragment visar hur de ska läsa enhetsinformation som tidigare har exporterats till en blockblobb:

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

> [!NOTE]
> Du kan också använda den **GetDevicesAsync** metod för den **RegistryManager** klassen för att hämta en lista över dina enheter. Den här metoden har dock en hård fjärrskrivbordsanslutning 1000 på antalet enhetsobjekt som ska returneras. Det förväntade användningsfallet för den **GetDevicesAsync** metoden är för utvecklingsscenarier att underlätta felsökning och rekommenderas inte för produktionsarbetsbelastningar.

## <a name="import-devices"></a>Importera enheter

Den **ImportDevicesAsync** metod i den **RegistryManager** klassen kan du utföra massåtgärder för import och synkronisering i en IoT-hubb identitetsregistret. Som det **ExportDevicesAsync** -metoden i **ImportDevicesAsync** metoden använder den **jobbet** framework.

Var noga med hjälp av den **ImportDevicesAsync** metoden eftersom förutom att etablera nya enheter i din identitetsregistret den även uppdatera och ta bort befintliga enheter.

> [!WARNING]
> En importen kan inte ångras. Säkerhetskopiera alltid dina befintliga data med hjälp av den **ExportDevicesAsync** metod till en annan blob-behållare innan du gör bulk ändringar i identitetsregistret.

Den **ImportDevicesAsync** metoden har två parametrar:

* En *sträng* som innehåller en URI för en [Azure Storage](../storage/index.yml) blob-behållaren ska användas som *inkommande* i jobbet. Den här URI måste innehålla en SAS-token som ger läsbehörighet till behållaren. Den här behållaren måste innehålla en blob med namnet **devices.txt** som innehåller serialiserade enhetsdata importera till din identitetsregistret. Importera data måste innehålla enhetsinformation i samma JSON-format som den **ExportImportDevice** jobbet används när den skapar en **devices.txt** blob. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```
* En *sträng* som innehåller en URI för en [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) blob-behållaren ska användas som *utdata* från jobbet. Jobbet skapar en blockblobb i behållaren att spara felinformation om från den slutförda importen **jobbet**. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> De två parametrarna peka på samma blob-behållaren. Separata parametrar kan bara mer kontroll över dina data som kräver ytterligare behörigheter för behållaren utdata.

Följande C# kodfragmentet visas hur du initierar ett importjobb:

```csharp
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Den här metoden kan även användas för import av data för dubbla för enheten. Formatet för indata är detsamma som visas i den **ExportDevicesAsync** avsnitt. På så sätt kan importera du den exporterade data. Den **$metadata** är valfritt.

## <a name="import-behavior"></a>Importera beteende

Du kan använda den **ImportDevicesAsync** metod för att utföra följande massåtgärder i identitetsregistret:

* Massinläsning registrering av nya enheter
* Massinläsning borttagningar av befintliga enheter
* Massredigera statusändringar (aktivera eller inaktivera enheter)
* Massinläsning tilldelning av nya nycklar för autentisering av enheten
* Massinläsning auto-omskapande av enheten autentiseringsnycklar
* Massuppdatering av identiska data

Du kan utföra en kombination av föregående åtgärder i en enskild **ImportDevicesAsync** anropa. Du kan till exempel registrera nya enheter och ta bort eller uppdatera befintliga enheter samtidigt. När den används tillsammans med den **ExportDevicesAsync** metod, du kan migrera alla dina enheter från en IoT-hubb helt till en annan.

Om importfilen innehåller dubbla metadata, sedan dessa metadata skriver över befintliga dubbla metadata. Om importfilen inte innehåller dubbla metadata, sedan bara den `lastUpdateTime` metadata har uppdaterats med den aktuella tiden.

Använd det valfria **ImportMode %** egenskap i serialiseringsdata import för varje enhet för att styra den importera processen per enheten. Den **ImportMode %** -egenskap har följande alternativ:

| ImportMode % | Beskrivning |
| --- | --- |
| **createOrUpdate** |Om en enhet inte finns med det angivna **id**, den nyligen har registrerats. <br/>Om enheten redan befintlig information skrivs över med den angivna indata utan avser den **ETag** värde. <br> Användaren kan ange identiska data tillsammans med enhetsdata. Den dubbla etag bearbetas om anges separat från enhetens etag. Om det finns en felaktig matchning med befintliga dubbla etag, skrivs ett fel till loggfilen. |
| **skapa** |Om en enhet inte finns med det angivna **id**, den nyligen har registrerats. <br/>Om enheten redan finns skrivs ett fel till loggfilen. <br> Användaren kan ange identiska data tillsammans med enhetsdata. Den dubbla etag bearbetas om anges separat från enhetens etag. Om det finns en felaktig matchning med befintliga dubbla etag, skrivs ett fel till loggfilen. |
| **update** |Om det finns redan en enhet med det angivna **id**, befintlig information skrivs över med den angivna indata utan avser den **ETag** värde. <br/>Om enheten inte finns skrivs ett fel till loggfilen. |
| **updateIfMatchETag** |Om det finns redan en enhet med det angivna **id**, befintlig information skrivs över med den angivna indata om det finns en **ETag** matchar. <br/>Om enheten inte finns skrivs ett fel till loggfilen. <br/>Om det finns en **ETag** matchningsfel, ett fel skrivs till loggfilen. |
| **createOrUpdateIfMatchETag** |Om en enhet inte finns med det angivna **id**, den nyligen har registrerats. <br/>Om enheten redan befintlig information skrivs över med den angivna indata om det finns en **ETag** matchar. <br/>Om det finns en **ETag** matchningsfel, ett fel skrivs till loggfilen. <br> Användaren kan ange identiska data tillsammans med enhetsdata. Den dubbla etag bearbetas om anges separat från enhetens etag. Om det finns en felaktig matchning med befintliga dubbla etag, skrivs ett fel till loggfilen. |
| **ta bort** |Om det finns redan en enhet med det angivna **id**, tas den bort utan avser den **ETag** värde. <br/>Om enheten inte finns skrivs ett fel till loggfilen. |
| **deleteIfMatchETag** |Om det finns redan en enhet med det angivna **id**, raderas om det finns en **ETag** matchar. Om enheten inte finns skrivs ett fel till loggfilen. <br/>Om det finns en ETag-Typfel, skrivs ett fel till loggfilen. |

> [!NOTE]
> Om serialiseringsdata inte explicit definiera en **ImportMode %** flaggan för en enhet, används som standard **createOrUpdate** under importen.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importera enheter exempel – masskopiera enhetsetableringen

Följande C# kodexempel illustrerar hur du skapar flera identiteter i enheten som:

* Inkludera autentiseringsnycklar.
* Skriva enhetsinformationen till en blockblobb.
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
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

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

Följande kodexempel visar hur du tar bort de enheter som du lade till i kodexemplet:

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

## <a name="get-the-container-sas-uri"></a>Hämta behållaren SAS-URI

Följande kodexempel visar hur du skapar en [SAS URI](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) med läsa, skriva och ta bort behörigheter för en blob-behållare:

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

I den här artikeln har du lärt dig hur du utför massåtgärder mot identitetsregistret i en IoT-hubb. Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT-hubb:

* [IoT-hubb mått][lnk-metrics]
* [Åtgärder som övervakning][lnk-monitor]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till gränsenheter med Azure IoT Edge][lnk-iotedge]

Om du vill utforska använda Etableringstjänsten IoT-hubb enheten för att aktivera zero touch se just-in-time-etablering: 

* [Azure IoT-hubb enheten etablering av tjänst][lnk-dps]


[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
