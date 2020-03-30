---
title: Import/export av Azure IoT Hub-enhetsidentiteter | Microsoft-dokument
description: Så här använder du Azure IoT-tjänsten SDK för att köra massåtgärder mot identitetsregistret för att importera och exportera enhetsidentiteter. Med importåtgärder kan du skapa, uppdatera och ta bort enhetsidentiteter i grupp.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 2a0394e6e7c17e0a4954bbdddb1d5b2811959746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371587"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Massimportera och massexportera IoT Hub-enhetsidentiteter

Varje IoT-hubb har ett identitetsregister som du kan använda för att skapa resurser per enhet i tjänsten. Identitetsregistret gör det också möjligt att styra åtkomsten till de enhetsvända slutpunkterna. I den här artikeln beskrivs hur du importerar och exporterar enhetsidentiteter i bulk till och från ett identitetsregister. Information om hur du visar ett fungerande exempel i C# och lär dig hur du kan använda den här funktionen när du klonar ett nav till en annan region finns i [Klona en IoT Hub](iot-hub-how-to-clone.md).

> [!NOTE]
> IoT Hub har nyligen lagt till stöd för virtuella nätverk i ett begränsat antal regioner. Den här funktionen skyddar import- och exportåtgärder och eliminerar behovet av att skicka nycklar för autentisering.  Inledningsvis är virtuellt nätverksstöd endast tillgängligt i dessa regioner: *WestUS2*, *EastUS*och *SouthCentralUS*. Mer information om stöd för virtuella nätverk och API-anrop för att implementera det finns i [IoT Hub Support för virtuella nätverk](virtual-network-support.md).

Import- och exportåtgärder sker i samband med *Jobb* som gör att du kan utföra masstjänståtgärder mot en IoT-hubb.

Klassen **RegistryManager** innehåller metoderna **ExportDevicesAsync** och **ImportDevicesAsync** som använder **ramverket Jobb.** Med de här metoderna kan du exportera, importera och synkronisera hela ett IoT-navidentitetsregister.

I det här avsnittet beskrivs hur du använder **klassen RegistryManager** och **Job** system för att utföra massimport och export av enheter till och från ett IoT-navs identitetsregister. Du kan också använda Azure IoT Hub Device Provisioning Service för att aktivera zero-touch, just-in-time-etablering till en eller flera IoT-hubbar utan att kräva mänsklig inblandning. Mer information finns i dokumentationen till [etableringstjänsten](/azure/iot-dps).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="what-are-jobs"></a>Vad är jobb?

Identitetsregisteråtgärder använder **jobbsystemet** när åtgärden:

* Har en potentiellt lång körningstid jämfört med vanliga körningsåtgärder.

* Returnerar en stor mängd data till användaren.

I stället för ett enda API-anrop som väntar eller blockerar resultatet av åtgärden skapar åtgärden asynkront ett **jobb** för den IoT-hubben. Åtgärden returnerar sedan omedelbart ett **JobProperties-objekt.**

Följande C#-kodavsnitt visar hur du skapar ett exportjobb:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Om du vill använda **klassen RegistryManager** i C#-koden lägger du till **Paketet Microsoft.Azure.Devices** NuGet i projektet. **Klassen RegistryManager** finns i namnområdet **Microsoft.Azure.Devices.**

Du kan använda klassen **RegistryManager** för att fråga tillståndet för **jobbet** med hjälp av de returnerade **JobProperties-metadata.** Om du vill skapa en instans av klassen **RegistryManager** använder du metoden **CreateFromConnectionString.**

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Så här hittar du anslutningssträngen för din IoT-hubb i Azure-portalen:

- Gå till IoT-hubben.

- Välj **principer för delad åtkomst**.

- Välj en princip med hänsyn till de behörigheter du behöver.

- Kopiera anslutningssträngen från panelen till höger på skärmen.

Följande C#-kodavsnitt visar hur du avsöker var femte sekund för att se om jobbet har körts klart:

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

> [!NOTE]
> Om ditt lagringskonto har brandväggskonfigurationer som begränsar IoT Hub-anslutningen kan du överväga att använda [Microsofts betrodda undantag](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) från första part (tillgängligt i vissa regioner för IoT-hubbar med hanterad tjänstidentitet).


## <a name="device-importexport-job-limits"></a>Jobbgränser för enhetsimport/export

Endast ett aktivt enhetsimport- eller exportjobb tillåts åt gången för alla IoT Hub-nivåer. IoT Hub har också gränser för jobboperationer. Mer information finns i [Referens - IoT Hub-kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Exportera enheter

Använd metoden **ExportDevicesAsync** för att exportera hela ett IoT-hubbidentitetsregister till en Azure Storage-blob-behållare med hjälp av en SIGNATUR för delad åtkomst (SAS). Mer information om signaturer för delad åtkomst finns i [Bevilja begränsad åtkomst till Azure Storage-resurser med hjälp av SAS (Shared Access Signatures).](../storage/common/storage-sas-overview.md)

Med den här metoden kan du skapa tillförlitliga säkerhetskopior av enhetsinformationen i en blob-behållare som du styr.

**Metoden ExportDevicesAsync** kräver två parametrar:

* En *sträng* som innehåller en URI-behållare för en blob-behållare. Den här URI:n måste innehålla en SAS-token som ger skrivåtkomst till behållaren. Jobbet skapar en blockblob i den här behållaren för att lagra serialiserade exportenhetsdata. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* En *boolesk* som anger om du vill utesluta autentiseringsnycklar från exportdata. Om **det är falskt**inkluderas autentiseringsnycklar i exportutdata. Annars exporteras nycklar som **null**.

Följande C#-kodavsnitt visar hur du initierar ett exportjobb som innehåller enhetsautentiseringsnycklar i exportdata och sedan avsöker för slutförande:

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

Jobbet lagrar utdata i den angivna blob-behållaren som en blockblob med namnet **devices.txt**. Utdata består av JSON serialiserade enhetsdata, med en enhet per linje.

I följande exempel visas utdata:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Om en enhet har dubbla data exporteras även tvillingdata tillsammans med enhetsdata. I följande exempel visas det här formatet. Alla data från raden "twinETag" tills slutet är dubbla data.

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

Om du behöver åtkomst till dessa data i kod kan du enkelt deserialisera dessa data med klassen **ExportImportDevice.** Följande C#-kodavsnitt visar hur du läser enhetsinformation som tidigare exporterades till en blockblob:

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

Med metoden **ImportDevicesAsync** i klassen **RegistryManager** kan du utföra massimport- och synkroniseringsåtgärder i ett IoT-hubbidentitetsregister. Precis som metoden **ExportDevicesAsync** använder metoden **ImportDevicesAsync** **ramverket Jobb.**

Var försiktig med metoden **ImportDevicesAsync** eftersom det förutom att etablera nya enheter i identitetsregistret också kan uppdatera och ta bort befintliga enheter.

> [!WARNING]
> Det går inte att ångra en importåtgärd. Säkerhetskopiera alltid befintliga data med metoden **ExportDevicesAsync** till en annan blob-behållare innan du gör massändringar i identitetsregistret.

**Metoden ImportDevicesAsync** tar två parametrar:

* En *sträng* som innehåller en URI för en Azure Storage-blob-behållare som ska användas som *indata* till jobbet. [Azure Storage](../storage/index.yml) Den här URI:n måste innehålla en SAS-token som ger läsbehörighet till behållaren. Den här behållaren måste innehålla en blob med namnet **devices.txt** som innehåller serialiserade enhetsdata som ska importeras till identitetsregistret. Importdata måste innehålla enhetsinformation i samma JSON-format som **jobbet ExportImportDevice** använder när en **devices.txt-blob** skapas. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* En *sträng* som innehåller en URI för en Azure Storage-blob-behållare som ska användas som *utdata* från jobbet. [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) Jobbet skapar en blockblob i den här behållaren för att lagra eventuell felinformation från det slutförda **importjobbet**. SAS-token måste innehålla följande behörigheter:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> De två parametrarna kan peka på samma blob-behållare. De separata parametrarna ger helt enkelt mer kontroll över dina data eftersom utdatabehållaren kräver ytterligare behörigheter.

Följande C#-kodavsnitt visar hur du initierar ett importjobb:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Den här metoden kan också användas för att importera data för enhetstvillingen. Formatet för datainmatningen är detsamma som formatet som visas i avsnittet **ExportDevicesAsync.** På så sätt kan du importera de exporterade data igen. Det **$metadata** är valfritt.

## <a name="import-behavior"></a>Importbeteende

Du kan använda metoden **ImportDevicesAsync** för att utföra följande massåtgärder i identitetsregistret:

* Massregistrering av nya enheter
* Massborttagningar av befintliga enheter
* Massstatusändringar (aktivera eller inaktivera enheter)
* Masstilldelning av nya enhetsautentiseringsnycklar
* Massförnyelse av enhetsautentiseringsnycklar
* Massuppdatering av tvillingdata

Du kan utföra valfri kombination av föregående åtgärder inom ett enda **ImportDevicesAsync-anrop.** Du kan till exempel registrera nya enheter och ta bort eller uppdatera befintliga enheter samtidigt. När du används tillsammans med **metoden ExportDevicesAsync** kan du helt migrera alla dina enheter från en IoT-hubb till en annan.

Om importfilen innehåller dubbla metadata skriver metadata över de befintliga tvillingmetadata. Om importfilen inte innehåller dubbla metadata `lastUpdateTime` uppdateras bara metadata med den aktuella tiden.

Använd egenskapen valfri **importMode** i import serialiseringsdata för varje enhet för att styra importprocessen per enhet. Egenskapen **importMode** har följande alternativ:

| importMode | Beskrivning |
| --- | --- |
| **createOrUpdate** |Om det inte finns en enhet med det angivna **ID:t**registreras den nyligen. <br/>Om enheten redan finns skrivs befintlig information över med de angivna indata utan hänsyn till **ETag-värdet.** <br> Användaren kan eventuellt ange dubbla data tillsammans med enhetsdata. Tvillingens etag, om det anges, bearbetas oberoende av enhetens etag. Om det finns en obalans med den befintliga tvillingens etag skrivs ett fel till loggfilen. |
| **skapa** |Om det inte finns en enhet med det angivna **ID:t**registreras den nyligen. <br/>Om enheten redan finns skrivs ett fel till loggfilen. <br> Användaren kan eventuellt ange dubbla data tillsammans med enhetsdata. Tvillingens etag, om det anges, bearbetas oberoende av enhetens etag. Om det finns en obalans med den befintliga tvillingens etag skrivs ett fel till loggfilen. |
| **Uppdatering** |Om det redan finns en enhet med det angivna **ID:t**skrivs befintlig information över med de angivna indata utan hänsyn till **ETag-värdet.** <br/>Om enheten inte finns skrivs ett fel till loggfilen. |
| **updateIfMatchETag** |Om det redan finns en enhet med det angivna **ID:t**skrivs befintlig information över med de angivna indata endast om det finns en **ETag-matchning.** <br/>Om enheten inte finns skrivs ett fel till loggfilen. <br/>Om det **ETag** finns en ETag-felmatchning skrivs ett fel till loggfilen. |
| **createOrUpdateIfMatchETag** |Om det inte finns en enhet med det angivna **ID:t**registreras den nyligen. <br/>Om enheten redan finns skrivs befintlig information över med de **ETag** angivna indata endast om det finns en ETag-matchning. <br/>Om det **ETag** finns en ETag-felmatchning skrivs ett fel till loggfilen. <br> Användaren kan eventuellt ange dubbla data tillsammans med enhetsdata. Tvillingens etag, om det anges, bearbetas oberoende av enhetens etag. Om det finns en obalans med den befintliga tvillingens etag skrivs ett fel till loggfilen. |
| **delete** |Om det redan finns en enhet med det angivna **ID:t**tas den bort utan hänsyn till **ETag-värdet.** <br/>Om enheten inte finns skrivs ett fel till loggfilen. |
| **deleteIfMatchETag** |Om det redan finns en enhet med det angivna **ID:t**tas den bara bort om det finns en **ETag-matchning.** Om enheten inte finns skrivs ett fel till loggfilen. <br/>Om det finns en ETag-felmatchning skrivs ett fel till loggfilen. |

> [!NOTE]
> Om serialiseringsdata inte uttryckligen definierar en **importMode-flagga** för en enhet, **skapasorupdate** som standard under importåtgärden.

## <a name="import-devices-example--bulk-device-provisioning"></a>Exempel på importenheter – etablering av bulkenheter

Följande C#-kodexempel illustrerar hur du genererar flera enhetsidentiteter som:

* Inkludera autentiseringsnycklar.
* Skriv enhetsinformationen till en blockblob.
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

## <a name="import-devices-example--bulk-deletion"></a>Exempel på importenheter – massborttagning

I följande kodexempel visas hur du tar bort de enheter som du har lagt till med det föregående kodexemplet:

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

## <a name="get-the-container-sas-uri"></a>Hämta behållaren SAS URI

Följande kodexempel visar hur du genererar en [SAS URI](../storage/common/storage-dotnet-shared-access-signature-part-1.md) med läs-, skriv- och borttagningsbehörigheter för en blob-behållare:

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

I den här artikeln har du lärt dig hur du utför massåtgärder mot identitetsregistret i en IoT-hubb. Många av dessa åtgärder, inklusive hur du flyttar enheter från en hubb till ett annat, används i [avsnittet Hantera enheter som är registrerade i ioT-hubben i Hur man klonar en IoT Hub](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

Kloningsartikeln har ett fungerande exempel kopplat till den, som finns i IoT C#-exemplen på den här sidan: [Azure IoT Samples for C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), med projektet ImportExportDevicesSample. Du kan ladda ner exemplet och prova det; Det finns instruktioner i hur [man klonar en IoT](iot-hub-how-to-clone.md) Hub-artikel.

Mer information om hur du hanterar Azure IoT Hub finns i följande artiklar:

* [IoT Hub-mått](iot-hub-metrics.md)
* [IoT Hub loggar](iot-hub-monitor-resource-health.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Information om hur du använder etableringstjänsten för IoT Hub Device För att aktivera nollpunkts-etablering, just-in-time-etablering finns i: 

* [Azure IoT Hub Device Provisioning-tjänst](/azure/iot-dps)
