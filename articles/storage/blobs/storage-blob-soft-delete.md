---
title: Mjuk borttagning för Azure Storage-blobbar | Microsoft Docs
description: Azure Storage erbjuder nu mjuk borttagning för blob-objekt så att du kan enkelt återställa dina data när den är felaktigt ändras eller tas bort av ett program eller en annan användare för storage-konto.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 8c23e429966cf9a1e93ac46ea3ecd11744761872
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148626"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Mjuk borttagning för Azure Storage-blobbar
Azure Storage erbjuder nu mjuk borttagning för blob-objekt så att du kan enkelt återställa dina data när den är felaktigt ändras eller tas bort av ett program eller en annan användare för storage-konto.

## <a name="how-does-it-work"></a>Hur fungerar det?
När den aktiveras, kan mjuk borttagning du spara och återställa dina data när blobar eller blobögonblicksbilder har tagits bort. Det här skyddet täcker även blob-data som tas bort till följd av en överskrivning.

När data tas bort, övergår det till ett ej permanent Borttaget tillstånd i stället för att tas bort permanent. När mjuk borttagning är på och du skriver över data, genereras en ej permanent borttagen ögonblicksbild för att spara tillståndet för över data. Ej permanent borttagna objekt är osynliga såvida inte uttryckligen anges. Du kan konfigurera tidsperiod som mjukt borttagna data kan återställas innan det förfaller permanent.

Mjuk borttagning är bakåtkompatibel; Du behöver göra några ändringar i dina program att kunna utnyttja de skydd som ger den här funktionen. Dock [dataåterställning](#recovery) introducerar en ny **ångra borttagning av Blob** API.

### <a name="configuration-settings"></a>Konfigurationsinställningar
När du skapar ett nytt konto, är Mjuk borttagning inaktiverat som standard. Mjuk borttagning är också inaktiverat som standard för befintliga lagringskonton. Du kan växla och inaktivera funktionen när som helst under din arbetsgrens livstid ett storage-konto.

Du kommer fortfarande att kunna komma åt och återställa mjukt borttagna data när funktionen är avstängd, förutsatt att mjukt borttagna data sparades när funktionen tidigare har slagits på. När du aktiverar mjuk borttagning, måste du också konfigurera kvarhållningsperioden.

Kvarhållningsperioden anger hur lång tid som mjukt borttagna data är lagrade och tillgänglig för återställning. För blobbar och blob-ögonblicksbilder som uttryckligen har tagits bort, systemklockan kvarhållningsperioden startar när data tas bort. För ej permanent borttagna ögonblicksbilder som genereras av funktionen när data skrivs över, startar klockan när ögonblicksbilden har genererats. För närvarande kan du behålla mjukt borttagna data i mellan 1 och 365 dagar.

Du kan ändra kvarhållningsperioden för mjuk borttagning när som helst. En uppdaterad kvarhållningsperiod gäller enbart för nyligen borttagna data. Tidigare borttagna data går ut baserat på tiden som konfigurerades när dessa data har tagits bort. Försök att ta bort ett ej permanent borttaget objekt påverkas inte dess förfallotid.

### <a name="saving-deleted-data"></a>Sparar borttagna data
Mjuk borttagning bevarar dina data i många fall där blobar eller blobögonblicksbilder har tagits bort eller skrivs över.

När en blob skrivs över med hjälp av **placera Blob**, **placera Block**, **placera Blockeringslista** eller **kopiering av Blob** en ögonblicksbild av blobens tillståndet före den skriva åtgärden genereras automatiskt. Den här ögonblicksbilden är en ej permanent borttagen ögonblicksbild; Det är osynligt, såvida inte uttryckligen visas ej permanent borttagna objekt. Se den [Recovery](#recovery) att ta reda på Visa en lista över ej permanent borttagna objekt.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Mjukt borttagna data är grå, medan aktiva data är blå. Mer data som nyligen har skrivits visas under äldre data. När B0 skrivs över med B1, genereras en ej permanent borttagen ögonblicksbild av B0. När B1 skrivs över med B2, genereras en ej permanent borttagen ögonblicksbild av B1.*

> [!NOTE]  
> Mjuk borttagning ger endast skriva över skydd för kopieringsåtgärder när det är aktiverat för mål-blob-konto.

> [!NOTE]  
> Mjuk borttagning som inte ger skriva över skydd för BLOB i arkivlagring. Om en blob i arkivlagring skrivs över med en ny blob i valfri nivå, skrevs över blob är permanent upphört att gälla.

När **ta bort Blob** anropas på en ögonblicksbild som ögonblicksbilden har markerats som ej permanent bort. En ny ögonblicksbild genereras inte.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Mjukt borttagna data är grå, medan aktiva data är blå. Mer data som nyligen har skrivits visas under äldre data. När **ta ögonblicksbild av Blob** anropas B0 blir en ögonblicksbild och B1 är aktiv på blob. När ögonblicksbilden B0 raderas den markeras som ej permanent bort.*

När **ta bort Blob** kallas för en grundläggande blob (någon blob som är inte själva en ögonblicksbild) som har markerats som ej permanent bort. Konsekvent med tidigare beteendet Anropa **ta bort Blob** returnerar ett fel på en blob som har aktiva ögonblicksbilder. Anropa **ta bort Blob** för en blob med ej permanent borttagna ögonblicksbilder inte returnerar ett fel. Du kan fortfarande ta bort en blob och alla dess ögonblicksbilder på gång när mjuk borttagning är påslagen. Detta markerar de grundläggande blobben och ögonblicksbilder som mjuk tas bort.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Mjukt borttagna data är grå, medan aktiva data är blå. Mer data som nyligen har skrivits visas under äldre data. Här är en **ta bort Blob** anrop görs för att ta bort B2 och alla associerade ögonblicksbilder. Den aktiva blob, B2 och alla associerade ögonblicksbilder är markerade som ej permanent bort.*

> [!NOTE]  
> När en ej permanent borttagna blob skrivs över, skapas automatiskt en ej permanent borttagna ögonblicksbild av blobens tillstånd innan åtgärden. Nivå för skrevs över blob ärver den nya bloben.

Mjuk borttagning inte spara dina data i fall av behållare eller tar bort kontot och inte heller när blobbmetadata och egenskaper för blob skrivs över. Du kan konfigurera ett lås som med Azure Resource Manager för att skydda ett lagringskonto från felaktiga borttagning. Finns i Azure Resource Manager-artikeln [låsresurser för att förhindra oväntade ändringar](../../azure-resource-manager/resource-group-lock-resources.md) vill veta mer.

I följande tabell beskrivs förväntat beteende när mjuk borttagning är aktiverat:

| REST API-åtgärd | Resurstyp | Beskrivning | Ändra i beteende |
|--------------------|---------------|-------------|--------------------|
| [Ta bort](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Tar bort lagringskontot, inklusive alla behållare och blobbar som den innehåller.                           | Ingen ändring. Behållare och blobbar i Borttaget konto går inte att återställa. |
| [Ta bort behållare](/rest/api/storageservices/delete-container) | Container | Tar bort behållaren, inklusive alla blobbar som den innehåller. | Ingen ändring. Det går inte att återställa blobar i behållaren har tagits bort. |
| [Placera Blob](/rest/api/storageservices/put-blob) | Block, lägga till och Sidblob-objekt | Skapar en ny blob eller ersätter en befintlig blob i en behållare | Om används för att ersätta en befintlig blob, skapas automatiskt en ögonblicksbild av blobens tillstånd innan anropet. Detta gäller även för en tidigare ej permanent borttagna blob endast om den har ersatts av en blob-av samma typ (Block, Lägg till eller sida). Om den har ersatts av en blob-av en annan typ kan förfaller alla befintliga mjukt borttagna data permanent. |
| [Ta bort Blob](/rest/api/storageservices/delete-blob) | Block, lägga till och Sidblob-objekt | Markerar en blob eller blobögonblicksbild för borttagning. Blob eller ögonblicksbild tas senare bort under skräpinsamling | Om används för att bort ta bort en blob-ögonblicksbild, den ögonblicksbilden har markerats som ej permanent. Om du vill ta bort en blob, som har markerats som ej permanent bort. |
| [Kopiera Blob](/rest/api/storageservices/copy-blob) | Block, lägga till och Sidblob-objekt | Kopierar en källblob till en mål-blob i samma lagringskonto eller i ett annat lagringskonto. | Om används för att ersätta en befintlig blob, skapas automatiskt en ögonblicksbild av blobens tillstånd innan anropet. Detta gäller även för en tidigare ej permanent borttagna blob endast om den har ersatts av en blob-av samma typ (Block, Lägg till eller sida). Om den har ersatts av en blob-av en annan typ kan förfaller alla befintliga mjukt borttagna data permanent. |
| [Placera Block](/rest/api/storageservices/put-block) | Blockblob-objekt | Skapar ett nytt block för att bekräftas som en del av en blockblob. | Om används för att genomföra ett block till en blob som är aktiv har inte ändrats. Om används för att genomföra ett block till en blob som är ej permanent borttagen, skapas en ny blob och en ögonblicksbild skapas automatiskt för att spara tillståndet för ej permanent borttagna blob. |
| [Placera Blockeringslista](/rest/api/storageservices/put-block-list) | Blockblob-objekt | Genomför en blob genom att ange uppsättningen block-ID som utgör blockbloben. | Om används för att ersätta en befintlig blob, skapas automatiskt en ögonblicksbild av blobens tillstånd innan anropet. Detta gäller även för en tidigare ej permanent borttagna blob endast om det är en Blockblob. Om den har ersatts av en blob-av en annan typ kan förfaller alla befintliga mjukt borttagna data permanent. |
| [Placera sidan](/rest/api/storageservices/put-page) | Sidblob-objekt | Skriver ett sidintervall till en Sidblobb. | Ingen ändring. Page Blob-data som skrivs eller avmarkerad med hjälp av den här åtgärden sparas inte och kan inte återställas. |
| [Lägga till Block](/rest/api/storageservices/append-block) | Tilläggsblobbar | Skriver ett datablock i slutet av en bifoga Blob | Ingen ändring. |
| [Ange Blob-egenskaper](/rest/api/storageservices/set-blob-properties) | Block, lägga till och Sidblob-objekt | Anger värden för Systemegenskaper som definierats för en blob. | Ingen ändring. Det går inte att återställa skrevs över blobegenskaper. |
| [Ange Blob-Metadata](/rest/api/storageservices/set-blob-metadata) | Block, lägga till och Sidblob-objekt | Ställer in användardefinierade metadata för den angivna blobben som en eller flera namn / värde-par. | Ingen ändring. Skrevs över blob-metadata kan inte återställas. |

Det är viktigt att Observera att anropa ”placera Page” om du vill skriva över eller rensa intervallen för en Sidblob inte genererar automatiskt ögonblicksbilder. Virtuella diskar som backas upp av Sidblobar och använda **placera sidan** att skriva data.

### <a name="recovery"></a>Återställning
Om du vill göra det enklare att återställa borttagna data, har vi lagt till ett nytt ”ångra borttagning av Blob”-API. Anropa API: et ångra borttagning av på en ej permanent borttagna grundläggande blob återställer det och alla tillhörande ej permanent borttagna ögonblicksbilder som aktiv. Anropa API ångra borttagning av på en aktiv grundläggande blob återställer alla associerade ej permanent borttagna ögonblicksbilder som aktiv. När ögonblicksbilder har återställts som aktiv, ser det ut användargenererade ögonblicksbilder de Skriv inte över den grundläggande blobben.

Att återställa en blob till en specifik mjukt borttagna ögonblicksbild som du kan anropa **ångra borttagning av Blob** på grundläggande blob. Du kan sedan kopiera ögonblicksbilden över nu-aktiv-blob. Du kan också kopiera ögonblicksbilden till en ny blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Mjukt borttagna data är grå, medan aktiva data är blå. Mer data som nyligen har skrivits visas under äldre data. Här kan **ångra borttagning av Blob** anropas på blob B, vilket återställer grundläggande blob, B1 och alla associerade ögonblicksbilder, här bara B0 som aktiv. I det andra steget kopieras B0 över grundläggande blob. Den här kopieringsåtgärden genererar en ej permanent borttagen ögonblicksbild av B1.*

Om du vill visa ej permanent borttagna blobar och blobögonblicksbilder, kan du välja att inkludera borttagna data i **lista Blobar**. Du kan välja att visa endast ej permanent borttagna grundläggande blobar eller att inkludera ej permanent borttagen blobögonblicksbilder. Du kan visa den tid när data skedde samt antalet dagar innan data upphör att gälla permanent för mjukt borttagna data.

### <a name="example"></a>Exempel
Följande är konsolens utdata av ett .NET-skript som laddar upp, skriver över, ögonblicksbilder, borttagningar och återställningar som en blob med namnet ”HelloWorld” när det ej permanent ta bort är aktiverat:

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

Se den [nästa steg](#next-steps) för en pekare till programmet som producerade det här resultatet.

## <a name="pricing-and-billing"></a>Priser och fakturering
Alla mjukt borttagna data debiteras enligt samma taxa som aktiva data. Du debiteras inte för data som tas bort permanent efter konfigurerade kvarhållningsperioden. En djupdykning i ögonblicksbilder och hur de påföras kostnader finns i [förstå hur ögonblicksbilder påföras kostnader](storage-blob-snapshots.md).

Du debiteras inte för transaktioner som rör automatisk generering av ögonblicksbilder. Du kommer att debiteras för **ångra borttagning av Blob** transaktioner priset som ”skrivåtgärder”.

Mer information om priser för Azure Blob Storage i allmänhet finns det [Prissättningssidan för Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

När du först aktivera mjuk borttagning, bör du använda en liten kvarhållningsperiod för att bättre förstå hur funktionen påverkar din faktura.

## <a name="quickstart"></a>Snabbstart
### <a name="azure-portal"></a>Azure Portal
Om du vill aktivera mjuk borttagning, navigera till den **mjuk borttagning** alternativet **Blobtjänsten**. Klicka sedan på **aktiverad** och ange antalet dagar som du vill behålla mjukt borttagna data.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Om du vill visa ej permanent borttagna blobar, Välj den **Visa borttagna blobar** kryssrutan.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Om du vill visa ej permanent borttagna ögonblicksbilder för en viss blob, Välj blobben som och klicka sedan på **Visa ögonblicksbilder**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Kontrollera att den **Visa borttagna ögonblicksbilder** kryssrutan är markerad.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

När du klickar på en ej permanent borttagna blob eller en ögonblicksbild, Lägg märke till de nya blobegenskaperna. De anger när objektet har tagits bort och hur många dagar som återstår före blob eller blobögonblicksbilden har gått ut permanent. Om det ej permanent borttagna objektet inte är en ögonblicksbild har också alternativet att ångra borttagning av den.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Kom ihåg att återställa en blob kommer också ångra borttagning av alla associerade ögonblicksbilder. Om du vill ångra borttagning av ej permanent borttagna ögonblicksbilder för en aktiv blob, klickar du på den och välja **ångra borttagning av alla ögonblicksbilder**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

När du ångra borttagning av en blob-ögonblicksbilder, kan du klicka på **befordra** att kopiera en ögonblicksbild över rot-blob, varigenom blob till ögonblicksbilden.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Uppdatera egenskaper för en blob-klient för att aktivera mjuk borttagning. I följande exempel aktiveras mjuk borttagning för en delmängd av konton i en prenumeration:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Du kan verifiera den mjuk borttagning har varit påslagen med hjälp av följande kommando:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Om du vill återställa blobbar som tagits bort av misstag, anropar du ångrar borttagningen av dessa blobar. Kom ihåg att anropa **ångra borttagning av Blob**, både på aktiva och ej permanent borttagna blobar, återställs alla associerade ej permanent borttagna ögonblicksbilder som aktiv. I följande exempel anropar ångra borttagning på alla ej permanent borttagna och aktiva blobarna i en behållare:
```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Om du vill hitta aktuella bevarandeprincipen för mjuk borttagning, använder du följande kommando:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

### <a name="azure-cli"></a>Azure CLI 
Uppdatera egenskaper för en blob-klient för att aktivera mjuk borttagning:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

För att verifiera mjuk borttagning är påslagen, använder du följande kommando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

### <a name="python-client-library"></a>Python-klientbiblioteket
Uppdatera egenskaper för en blob-klient för att aktivera mjuk borttagning:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(delete_retention_policy = DeleteRetentionPolicy(enabled = True, days = 7))
```

### <a name="net-client-library"></a>Klientbiblioteket för .NET
Uppdatera egenskaper för en blob-klient för att aktivera mjuk borttagning:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Om du vill återställa blobbar som tagits bort av misstag, anropar du ångrar borttagningen av dessa blobar. Kom ihåg att anropa **ångra borttagning av Blob**, både på aktiva och ej permanent borttagna blobar, återställs alla associerade ej permanent borttagna ögonblicksbilder som aktiv. I följande exempel anropar ångra borttagning på alla ej permanent borttagna och aktiva blobarna i en behållare:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Om du vill återställa till en specifik blob-version, först anropa ångrar borttagningen för en blob och kopiera önskade ögonblicksbilden via bloben. I följande exempel återställer en blockblob till den nyligen skapad ögonblicksbilden:

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

## <a name="should-i-use-soft-delete"></a>Ska jag använda mjuk borttagning?
Om det finns en risk att dina data råkar ändrats eller tagits bort av ett program eller en annan användare för storage-konto, rekommenderar vi att aktivera mjuk borttagning. Mjuk borttagning är en del av en strategi för att skydda data och kan hjälpa dig att förhindra oavsiktlig dataförlust.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
**Finns det ingen särskild hänsyn för att använda mjuk borttagning?**  
Aktivera mjuk borttagning för ofta överskrivna data kan resultera i ökade lagringskostnader för kapacitet och ökad latens när blobbar. Du kan komma runt detta genom att lagra ofta skrevs över data i ett separat lagringskonto med mjuk borttagning har inaktiverats. 

**Vilka typer av lagring kan jag använda mjuk borttagning?**  
Mjuk borttagning är för närvarande endast tillgänglig för bloblagring (objekt).

**Är Mjuk borttagning tillgänglig för alla typer av konton?**  
Ja, mjuk borttagning är tillgänglig för blob storage-konton samt som blobar i allmänna lagringskonton (både GPv1- och GPv2). Detta gäller för både standard och premium-konton. Mjuk borttagning är inte tillgänglig för hanterade diskar.

**Är Mjuk borttagning tillgänglig för alla nivåer?**  
Ja, mjuk borttagning är tillgängliga för alla nivåer, inklusive frekvent, lågfrekvent och Arkiv. Dock mjuk borttagning som inte ger skriva över skydd för BLOB i arkivlagring.

**Kan jag använda Ange Blob-nivå API till tier blobar med ej permanent borttagna ögonblicksbilder?**  
Ja. Ej permanent borttagna ögonblicksbilder finns kvar i den ursprungliga nivån, men de grundläggande blobben flyttas till den nya nivån. 

**Premium storage-konton har en per blob snapshot gränsen på 100. Ej permanent borttagna ögonblicksbilder tillgodoräknas vid den här gränsen?**  
Nej, mjuk bort ögonblicksbilder inte räknas in i den här gränsen.

**Kan jag aktivera mjuk borttagning för befintliga storage-konton?**  
Ja, mjuk borttagning kan konfigureras för både befintliga och nya storage-konton.

**Om jag tar bort en hela kontot eller en behållare med mjuk borttagning aktiveras alla associerade BLOB sparas?**  
Nej, om du tar bort en hela kontot eller behållare kommer alla associerade BLOB tas bort permanent. Om du vill lära dig att skydda ett lagringskonto från oavsiktliga borttagningar, finns i Azure Resource Manager-artikeln [låsresurser för att förhindra oväntade ändringar](../../azure-resource-manager/resource-group-lock-resources.md).

**Kan jag visa kapacitetsdata för borttagna data?**  
Mjukt borttagna data ingår som en del av din totala lagringskapaciteten för kontot. Mer information om spårning och övervakning av lagringskapacitet finns i den [Lagringsanalys](../common/storage-analytics.md) artikeln.

**Om jag har stängt av mjuk borttagning kommer jag fortfarande att kunna komma åt mjukt borttagna data?**  
Ja, du kan fortfarande kan komma åt och återställa läggs mjukt borttagna data när mjuk borttagning är avstängd.

**Kan jag läsa och kopiera ej permanent borttagna ögonblicksbilder för mitt blob?**  
Ja, men du måste anropa ångra borttagning på blobben som först.

**Är Mjuk borttagning tillgänglig för alla blob-typer?**  
Ja, mjuk borttagning är tillgängligt för Blockblobbar, Tilläggsblobbar och Sidblobbar.

**Är Mjuk borttagning för virtuella diskar?**  
Mjuk borttagning är tillgänglig för både premium och standard ohanterade diskar. Mjuk borttagning endast hjälper dig att återställa data som tas bort av **ta bort Blob**, **placera Blob**, **placera Blockeringslista**, **placera Block** och **Kopiera Blob**. Data skrivs över av ett anrop till **placera sidan** kan inte återställas.

**Måste jag ändra Mina befintliga appar du använder mjuk borttagning?**  
Det är möjligt att utnyttja fördelarna med mjuk borttagning oavsett vilken API-version som du använder. Men för att lista och återställa mjukt borttagna blobar och blob-ögonblicksbilder, du måste använda version 2017-07-29 av den [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) eller större. I allmänhet rekommenderar vi alltid använder den senaste versionen oavsett om du använder den här funktionen.

## <a name="next-steps"></a>Nästa steg
* [.NET-exempelkod](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [REST API för BLOB Service](/rest/api/storageservices/blob-service-rest-api)
* [Azure Storage Replication](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Utforma högtillgängliga program med hjälp av RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Disaster recovery och storage-konto redundans (förhandsversion) i Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
