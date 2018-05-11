---
title: Mjuka ta bort för Azure Storage BLOB (förhandsversion) | Microsoft Docs
description: Azure Storage erbjuder mjuk borttagning (förhandsversion) för blob-objekt så att du lättare kan återställa data när det är felaktigt ändras eller tas bort av ett program eller en annan användare för storage-konto.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 03/21/2018
ms.author: mihauss
ms.openlocfilehash: 0e728f9f9754d76d893b12309bb52201d772efbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="soft-delete-for-azure-storage-blobs-preview"></a>Mjuk borttagning för Azure Storage BLOB (förhandsgranskning)

## <a name="overview"></a>Översikt

Azure Storage erbjuder mjuk borttagning (förhandsversion) för blob-objekt så att du lättare kan återställa data när det är felaktigt ändras eller tas bort av ett program eller en annan användare för storage-konto.

## <a name="how-does-it-work"></a>Hur fungerar det?

När aktiverat kan mjuk borttagning du spara och återställa dina data när blobbar eller blob-ögonblicksbilder tas bort. Det här skyddet sträcker sig till blob-data som tas bort till följd av en överskrivning.

När data tas bort, övergår det till ett mjukt Borttaget tillstånd i stället för att tas bort permanent. När mjuk borttagning är på och du skriver över data, skapas en ej permanent borttagen ögonblicksbild för att spara tillståndet för överskrivna data. Ej permanent borttagna objekt är osynliga såvida inte uttryckligen anges.
Du kan konfigurera tidsperiod som ej permanent borttagna data kan återställas innan det förfaller permanent.

Mjuk borttagning är bakåtkompatibel; Du behöver inte göra ändringar i dina program kan dra nytta av skydd som den här funktionen ger. Dock [dataåterställning](#recovery) introducerar en ny **ångra borttagning Blob** API.

> [!NOTE]
> Under Public Preview anropa ange Blob-nivå på en blob med ögonblicksbilder är inte tillåtet.
Mjuk borttagning genererar ögonblicksbilder för att skydda dina data när den är över. Vi arbetar aktivt på en lösning för att aktivera skiktning av blobbar med ögonblicksbilder.

### <a name="configuration-settings"></a>Konfigurationsinställningar

När du skapar ett nytt konto, är Mjuk borttagning inaktiverat som standard. Mjuk borttagning är också inaktiverat som standard för befintliga lagringskonton. Du kan växla och inaktivera funktionen när som helst under ett lagringskonto.

Du kommer fortfarande att kunna komma åt och återställa ej permanent borttagna data när funktionen är avstängd, förutsatt att ej permanent borttagna data sparades när funktionen tidigare var aktiverad. När du aktiverar mjuk borttagning, måste du också konfigurera kvarhållningsperioden.

Kvarhållningsperioden anger hur lång tid som ej permanent borttagna data är lagrade och tillgänglig för återställning. För blobbar och blob-ögonblicksbilder som uttryckligen tas bort klockfrekvensen kvarhållningsperioden startar när data tas bort. För ej permanent borttagna ögonblicksbilder som genereras av funktionen Mjuk borttagning när data skrivs över, påbörjas klocka när ögonblicksbilden har genererats. För närvarande kan du behålla ej permanent borttagna data för mellan 1 och 365 dagar.

Du kan ändra kvarhållningsperioden för mjuk borttagning när som helst. En uppdaterad Bevarandeperiod gäller enbart för nyligen borttagna data. Tidigare borttagna data upphör att gälla baserat på kvarhållningsperioden som konfigurerades när informationen har tagits bort.

### <a name="saving-deleted-data"></a>Spara borttagna data

Mjuk borttagning bevarar dina data i många fall där blobbar eller blob-ögonblicksbilder tas bort eller skrivs över.

När en blob skrivs över med **placera Blob**, **placera Block**, **placera Blockeringslista** eller **kopiera Blob** en ögonblicksbild av den blob tillstånd före den skriva åtgärden genereras automatiskt. Den här ögonblicksbilden är en ej permanent borttagen ögonblicksbild; Det är osynliga om ej permanent borttagna objekt uttryckligen anges. Finns det [Recovery](#recovery) avsnitt för att lära dig hur du listar ej permanent borttagna objekt.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Ej permanent borttagna data är grå, medan aktiva data är blå. Fler nyskrivna data visas under äldre data. När B0 skrivs över med B1 genereras en ej permanent borttagen ögonblicksbild av B0. När B1 skrivs över med B2 genereras en ej permanent borttagen ögonblicksbild av B1.*

> [!NOTE]
> Mjuk borttagning ger endast över skydd för kopieringsåtgärd när den är aktiverad för mål-blob-konto.

> [!NOTE]
> Mjuk borttagning inte ger över skydd för blobbar i arkivet nivån. Om en blobb i arkivet skrivs över med en ny blob i alla skikt över blob är permanent upphört att gälla.

När **ta bort Blob** anropas på en ögonblicksbild den ögonblicksbilden har markerats som ej permanent borttagna. En ny ögonblicksbild genereras inte.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Ej permanent borttagna data är grå, medan aktiva data är blå. Fler nyskrivna data visas under äldre data. När **ögonblicksbild Blob** anropas B0 blir en ögonblicksbild och B1 är aktiv på blob. När ögonblicksbilden B0 tas bort, den har markerats som ej permanent borttagna.*

När **ta bort Blob** anropas på en grundläggande blob (någon blob som inte själv en ögonblicksbild), blobben har markerats som ej permanent borttagna. Konsekvent med tidigare beteende, anropar **ta bort Blob** returnerar ett fel på en blob med aktiva ögonblicksbilder. Anropar **ta bort Blob** på en blob med ej permanent borttagna ögonblicksbilder inte returnerar ett fel. Du kan ändå ta bort en blob och alla dess ögonblicksbilder på gång när mjuk borttagning är aktiverat. Detta markerar de grundläggande blobben och ögonblicksbilder som mjuka tas bort.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Ej permanent borttagna data är grå, medan aktiva data är blå. Fler nyskrivna data visas under äldre data. Här är en **ta bort Blob** anrop görs för att ta bort B2 och alla associerade ögonblicksbilder. Aktiva blob, B2 och alla associerade ögonblicksbilder är markerade som ej permanent borttagna.*

> [!NOTE]
> En ej permanent borttagna ögonblicksbild av blobens tillstånd innan Skrivåtgärden genereras automatiskt när en ej permanent borttagna blob skrivs över. Ny blob ärver nivån på över blob.

Mjuk borttagning inte spara dina data i fall av behållare eller tar bort kontot, eller när blob-metadata och blob skrivs över. Du kan konfigurera ett lås med Azure Resource Manager för att skydda ett lagringskonto från felaktiga borttagning. Finns i Azure Resource Manager-artikeln [låsresurser för att förhindra oväntade ändringar](../../azure-resource-manager/resource-group-lock-resources.md) vill veta mer.

I följande tabell beskrivs förväntat beteende när mjuk borttagning är aktiverat:

| REST API-åtgärd                                                                                                | Resurstyp                 | Beskrivning                                                                                                 | Ändra i beteende                                                                                                                                                                                                                                                                                                                                                   |
|-------------------------------------------------------------------------------------------------------------------|-------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Ta bort](/rest/api/storagerp/StorageAccounts/Delete)              | Konto                       | Tar bort lagringskontot, inklusive alla behållare och blobbar som den innehåller.                           | Ingen ändring. Behållare och blobbar i kontot går inte att återställa.                                                                                                                                                                                                                                                                                          |
| [Ta bort behållaren](/rest/api/storageservices/fileservices/delete-container)       | Behållare                     | Tar bort den behållaren, inklusive alla blobbar som den innehåller.                                                | Ingen ändring. Det går inte att återställa blobbar i behållaren borttagna.                                                                                                                                                                                                                                                                                                       |
| [Placera Blob](/rest/api/storageservices/fileservices/put-blob)                       | Block, lägga till och Sidblobbar | Skapar en ny blob eller ersätter en befintlig blob i en behållare                                          | Om används för att ersätta en befintlig blob, skapas automatiskt en ögonblicksbild av den blob tillstånd innan anropet. Detta gäller även för en tidigare ej permanent borttagna blob endast om den har ersatts av en blob av samma typ (Block, Lägg till eller sidan). Om den har ersatts av en blob av en annan typ, ut alla befintliga ej permanent borttagna data permanent. |
| [Ta bort blobben](/rest/api/storageservices/fileservices/delete-blob)                 | Block, lägga till och Sidblobbar | Markerar en blob eller blob ögonblicksbild för borttagning. Blob eller ögonblicksbild senare tas bort under skräpinsamling | Om används för att bort ta bort en blob-ögonblicksbild, den ögonblicksbilden har markerats som ej permanent. Om används för att ta bort en blobb, blobben markeras som ej permanent borttagna.                                                                                                                                                                                                                           |
| [Kopiera Blob](/rest/api/storageservices/fileservices/copy-blob)                     | Block, lägga till och Sidblobbar | Kopierar en käll-blob till en mål-blob i samma lagringskonto eller i ett annat lagringskonto.       | Om används för att ersätta en befintlig blob, skapas automatiskt en ögonblicksbild av den blob tillstånd innan anropet. Detta gäller även för en tidigare ej permanent borttagna blob endast om den har ersatts av en blob av samma typ (Block, Lägg till eller sidan). Om den har ersatts av en blob av en annan typ, ut alla befintliga ej permanent borttagna data permanent. |
| [Placera Block](/rest/api/storageservices/put-block)                                  | Blockblob-objekt                   | Skapar ett nytt block för att genomföras som en del av en blockblob.                                                | Om används för att utföra ett block i en blob som är aktiv påverkas inte. Om för att utföra ett block i en blob som är ej permanent borttagen, skapas en ny blob och en ögonblicksbild har genererats automatiskt för att spara tillståndet för ej permanent borttagna blob.                                                                                                                      |
| [Placera Blockeringslista](/rest/api/storageservices/fileservices/put-block-list)           | Blockblob-objekt                   | Genomför en blob genom att ange en uppsättning block-ID som utgör blockblob.                             | Om används för att ersätta en befintlig blob, skapas automatiskt en ögonblicksbild av den blob tillstånd innan anropet. Detta gäller även för en tidigare ej permanent borttagna blob endast om det är en Blockblob. Om den har ersatts av en blob av en annan typ, ut alla befintliga ej permanent borttagna data permanent.                                                |
| [Placera sida](/rest/api/storageservices/fileservices/put-page)                       | Sidblob-objekt                    | Skriver ett sidintervall till en Sidblobb.                                                                     | Ingen ändring. Sidan Blob-data som skrivs över eller rensas med hjälp av den här åtgärden sparas inte och kan inte återställas.                                                                                                                                                                                                                                                   |
| [Lägga till Block](/rest/api/storageservices/fileservices/append-block)               | Tilläggsblobbar                  | Skriver ett datablock i slutet av en Append-Blob                                                         | Ingen ändring.                                                                                                                                                                                                                                                                                                                                                           |
| [Ange Blob-egenskaper](/rest/api/storageservices/fileservices/set-blob-properties) | Block, lägga till och Sidblobbar | Anger värden för egenskaper har definierats för en blob.                                                       | Ingen ändring. Det går inte att återställa över blob-egenskaper.                                                                                                                                                                                                                                                                                                          |
| [Ange Blobbmetadata](/rest/api/storageservices/fileservices/set-blob-metadata)     | Block, lägga till och Sidblobbar | Anger användardefinierade metadata för den angivna blobben som en eller flera namn / värde-par.                          | Ingen ändring. Över blobbmetadata kan inte återställas.                                                                                                                                                                                                                                                                                                             |

Det är viktigt att Observera att anropa ”placera sidan” om du vill åsidosätta eller rensa intervall med en Sidblob inte kommer att automatiskt skapa ögonblicksbilder. Virtuella diskar som backas upp av Sidblobar och använda **placera sidan** att skriva data.

### <a name="recovery"></a>Återställning

Om du vill göra det lättare att återställa borttagna data, har vi in ett nytt ”återställa Blob”-API. Anropa API: et ångra borttagning på en ej permanent borttagna grundläggande blob återställer det och alla tillhörande ej permanent borttagna ögonblicksbilder som aktiv. Anropa API: et ångra borttagning på en aktiv grundläggande blob återställer alla associerade ej permanent borttagna ögonblicksbilder som aktiv. Ögonblicksbilder återställs som aktiv ser ut som skapats av användare ögonblicksbilder; de skriver inte över grundläggande blob.

Återställa en blobb till en specifik mjukt borttagna ögonblicksbild som du kan anropa **ångra borttagning Blob** på grundläggande blob. Sedan kan du kopiera ögonblicksbilden över nu active-blob. Du kan också kopiera ögonblicksbilden till en ny blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Ej permanent borttagna data är grå, medan aktiva data är blå. Fler nyskrivna data visas under äldre data. Här, **ångra borttagning Blob** anropas på blob B, vilket återställer grundläggande blob, B1 och alla associerade ögonblicksbilder här bara B0 som aktiv. I det andra steget kopieras B0 över grundläggande blob. Den här kopieringsåtgärden genererar en ej permanent borttagen ögonblicksbild av B1.*

Om du vill visa ej permanent borttagna blobbar och blob ögonblicksbilder, kan du välja att inkludera borttagna data i **lista Blobbar**. Du kan välja att visa endast ej permanent borttagna grundläggande blobbar eller att inkludera ej permanent borttagna blob-ögonblicksbilder. Du kan visa den tid när data har tagits bort samt antalet dagar innan data upphör gälla permanent för ej permanent borttagna data.

### <a name="example"></a>Exempel

Följande är konsolens utdata för ett .NET-skript som överför, skriver över ögonblicksbilder, tar bort och återställer en blob med namnet ”HelloWorld” när ej permanent ta bort är aktiverat:

```bash
Upload:
- HelloWorld (is soft deleted: False, is snapshot: False)

Overwrite:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Snapshot:
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Delete (including snapshots):
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: False)

Undelete:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)

Copy a snapshot over the base blob:
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: True)
- HelloWorld (is soft deleted: True, is snapshot: True)
- HelloWorld (is soft deleted: False, is snapshot: False)
```

Finns det [nästa steg](#Next steps) avsnittet för en pekare till programmet som producerade det här utdata.

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla ej permanent borttagna data debiteras med samma hastighet som aktiva data. Du debiteras inte för data som tas bort permanent efter den konfigurerade bevarandeperioden. En mer grundlig genomgång i ögonblicksbilder och hur de påförs kostnader finns [förstå hur ögonblicksbilder påförs kostnader](storage-blob-snapshots.md).

Du kommer inte att debiteras för de transaktioner som rör automatisk generering av ögonblicksbilder. Du kommer att debiteras för **ångra borttagning Blob** transaktioner med hastighet för ”skriva Operations”.

Mer information om priser för Azure Blob Storage i allmänhet ta en titt på [Azure Blob Storage sidan prisnivåer](https://azure.microsoft.com/pricing/details/storage/blobs/).

När du först aktivera mjuk borttagning bör du använda en liten kvarhållningsperiod för att bättre förstå hur funktionen påverkar din faktura.

## <a name="quickstart"></a>Snabbstart

### <a name="azure-portal"></a>Azure Portal
Om du vill aktivera mjuk borttagning, navigera till den **mjuk borttagning** alternativ **Blob-tjänsten**. Klicka på **aktiverad** och ange antalet dagar som du vill behålla ej permanent borttagna data.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Om du vill visa ej permanent borttagna blobbar, Välj den **Visa borttagna blobbar** kryssrutan.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Om du vill visa ej permanent borttagna ögonblicksbilder för en given blob, Välj blob och klicka sedan **Visa ögonblicksbilder**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Kontrollera att den **Visa borttagna ögonblicksbilder** kryssrutan är markerad.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

När du klickar på en ej permanent borttagna blob eller ögonblicksbild Lägg märke till de nya blob-egenskaperna. De anger när objektet har tagits bort och hur många dagar kvar tills blob eller blob ögonblicksbild permanent upphör att gälla. Om det ej permanent borttagna objektet inte är en ögonblicksbild, måste du även ha alternativet att återställa den.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Kom ihåg att återställa en blob kommer även återställa alla associerade ögonblicksbilder. Klicka på blob och välj om du vill återställa ej permanent borttagna ögonblicksbilder för en aktiv blob **ångra ta bort alla ögonblicksbilder**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

När du återställa en blob ögonblicksbilder kan du klicka på **befordra** att kopiera en ögonblicksbild över rot-blob, vilket återställer blob till ögonblicksbilden.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell
Uppdatera egenskaper för en blob-klient om du vill aktivera mjuk borttagning. I följande exempel aktiveras mjuk borttagning för en delmängd av konton i en prenumeration:

```powershell
Set-AzureRmContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzureRMStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzureStorageDeleteRetentionPolicy -RetentionDays 7
```

Om du vill återställa blobbar som tagits bort av misstag anropa ångra ta bort för de blobbar. Kom ihåg att anropa **ångra borttagning Blob**, både på aktiva och ej permanent borttagna blobbar, återställs alla associerade ej permanent borttagna ögonblicksbilder som aktiv. I följande exempel anropar Ångra radering på alla ej permanent borttagna och aktiva blobbar i en behållare:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzureStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
### <a name="python-client-library"></a>Klientbibliotek för Python

Uppdatera egenskaper för en blob-klient om du vill aktivera mjuk borttagning:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>.NET-klientbibliotek

Uppdatera egenskaper för en blob-klient om du vill aktivera mjuk borttagning:

```csharp
// Get the blob client’s service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client’s service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Om du vill återställa blobbar som tagits bort av misstag anropa ångra ta bort för de blobbar.
Kom ihåg att anropa **ångra borttagning Blob**, både på aktiva och ej permanent borttagna blobbar, återställs alla associerade ej permanent borttagna ögonblicksbilder som aktiv. I följande exempel anropar Ångra radering på alla ej permanent borttagna och aktiva blobbar i en behållare:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Om du vill återställa till en specifik blob-version, först anropa ångra ta bort i en blob och kopiera önskade ögonblicksbilden över blob. I följande exempel återställer en blockblobb till nyligen skapade ögonblicksbilden:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

## <a name="should-i-use-soft-delete"></a>Bör jag använda mjuk borttagning?

Om det finns en risk att dina data av misstag har ändrats eller tagits bort av ett program eller en annan användare för storage-konto, rekommenderar vi aktivera mjuk borttagning.
Mjuk borttagning är en del av strategin för skydd av data och kan hjälpa dig att förhindra oavsiktlig dataförlust.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vilka typer av lagring kan du använda mjuk borttagning?**

Mjuk borttagning är för närvarande endast tillgängligt för bloblagring (objekt).

**Finns mjuk borttagning för alla typer av lagring?**

Ja, mjuk borttagning är tillgänglig för blob storage-konton samt som blobar i allmänna lagringskonton. Detta gäller både standard- och premium-konton. Mjuk borttagning är inte tillgänglig för hanterade diskar.

**Finns mjuk borttagning för alla lagringsnivåer?**

Ja, mjuk borttagning är tillgänglig för alla lagringsnivåer, inklusive hot, kall och Arkiv. Dock mjuk borttagning inte ger över skydd för blobbar i arkivet nivån.

**Premium-lagringskonton har en per blob ögonblicksbild högst 100. Ej permanent borttagna ögonblicksbilder räknas in i den här gränsen?**

Nej, mjuk bort ögonblicksbilder inte räknas in i den här gränsen.

**Kan jag aktivera mjuk borttagning för befintliga lagringskonton?**

Ja, mjuk borttagning kan konfigureras för befintliga och nya storage-konton.

**Om jag tar bort ett hela konto eller behållaren med mjuk borttagning aktiverat alla associerade BLOB sparas?**

Nej, om du tar bort ett hela konto eller behållaren alla associerade blobbar tas bort permanent. Om du vill veta hur du skyddar ett lagringskonto från oavsiktliga borttagningar finns i Azure Resource Manager-artikeln [låsresurser för att förhindra oväntade ändringar](../../azure-resource-manager/resource-group-lock-resources.md).

**Kan jag visa kapacitetsdata för borttagna data?**

Ej permanent borttagna data ingår som en del av din totala lagringskapaciteten för kontot. Mer information om spårning och övervakning av lagringskapacitet, finns det [Storage Analytics](../common/storage-analytics.md) artikel.

**Om du inaktiverar mjuk borttagning kommer det fortfarande att kunna komma åt ej permanent borttagna data?**

Ja, du kan fortfarande komma åt och återställa återstående ej permanent borttagna data när mjuk borttagning är avstängd.

**Kan jag läsa och kopiera ej permanent borttagna ögonblicksbilder av min blob?**

Ja, men du måste anropa ångra ta bort för blob först.

**Är Mjuk borttagning för alla blob-typer?**

Ja, mjuk borttagning är tillgängligt för Blockblobbar, Tilläggsblobbar och Sidblobbar.

**Finns mjuk borttagning för virtuella diskar?**

Mjuk borttagning är tillgänglig för både premium och standard ohanterade diskar. Mjuk borttagning hjälper dig att återställa data som tas bort av endast **ta bort Blob**, **placera Blob**, **placera Blockeringslista**, **placera Block** och **Kopiera Blob**. Data skrivs över av ett anrop till **placera sidan** kan inte återställas.

**Behöver jag ändra Mina befintliga program att använda mjuk borttagning?**

Det är möjligt att dra nytta av mjuk borttagning oavsett vilken API-version som du använder. Men för att lista och återställa ej permanent borttagna blobbar och blob ögonblicksbilder, behöver du använda versionen 2017-07-29 av den [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) eller större. I allmänhet rekommenderar vi alltid använder den senaste versionen oavsett om du använder den här funktionen.

## <a name="next-steps"></a>Nästa steg

* [Exempelkod för .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)

* [BLOB-tjänst REST-API](/rest/api/storageservices/fileservices/blob-service-rest-api)

* [Azure Storage-replikering](../common/storage-redundancy.md)

* [Designa hög tillgängliga program med hjälp av RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md)

* [Vad du ska göra om ett Azure Storage-avbrott inträffar](../common/storage-disaster-recovery-guidance.md)
