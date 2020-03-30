---
title: Mjuk borttagning för Azure Storage-blobbar | Microsoft-dokument
description: Azure Storage erbjuder nu mjuk borttagning för blob-objekt så att du lättare kan återställa dina data när de felaktigt ändras eller tas bort av ett program eller annan användare av lagringskontot.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 4deae235ed15d02874ab5cb3470c62e934324364
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234299"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Mjuk borttagning för Azure Storage-blobar

Azure Storage erbjuder nu mjuk borttagning för blob-objekt så att du lättare kan återställa dina data när de felaktigt ändras eller tas bort av ett program eller annan användare av lagringskontot.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-soft-delete-works"></a>Så här fungerar mjuk borttagning

När det är aktiverat kan du spara och återställa data när blobbar eller blob-ögonblicksbilder tas bort när du är aktiverad. Det här skyddet omfattar blob-data som raderas som ett resultat av en överskrivning.

När data tas bort övergår den till ett mjukt borttaget tillstånd i stället för att raderas permanent. När mjuk borttagning är aktiverat och du skriver över data genereras en mjuk borttagen ögonblicksbild för att spara tillståndet för de överskrivna data. Mjuka borttagna objekt är osynliga om de inte uttryckligen anges. Du kan ange hur länge mjukt borttagna data ska kunna återställas innan de tas bort permanent.

Mjuk borttagning är bakåtkompatibel, så du behöver inte göra några ändringar i dina program för att dra nytta av det skydd som den här funktionen ger. [Dataåterställning](#recovery) introducerar dock ett nytt **Undelete Blob** API.

### <a name="configuration-settings"></a>Konfigurationsinställningar

När du skapar ett nytt konto inaktiveras den mjuka borttagningen som standard. Mjuk borttagning är också inaktiverat som standard för befintliga lagringskonton. Du kan aktivera och inaktivera funktionen när som helst under ett lagringskontos livstid.

Du kommer fortfarande att kunna komma åt och återställa mjuka borttagna data när funktionen är avstängd, förutsatt att mjuka borttagna data sparades när funktionen tidigare var aktiverad. När du aktiverar mjuk borttagning måste du också konfigurera kvarhållningsperioden.

Lagringsperioden anger hur lång tid som mjuka borttagna data lagras och är tillgängliga för återställning. För blobbar och blob-ögonblicksbilder som uttryckligen tas bort startar kvarhållningsperiodens klocka när data tas bort. För mjuka borttagna ögonblicksbilder som genereras av funktionen för mjuk borttagning när data skrivs över startar klockan när ögonblicksbilden genereras. För närvarande kan du behålla mjuka borttagna data i mellan 1 och 365 dagar.

Du kan ändra kvarhållningsperioden för mjuk borttagning när som helst. En uppdaterad lagringsperiod gäller endast för nyligen borttagna data. Tidigare borttagna data upphör att gälla baserat på den kvarhållningsperiod som konfigurerades när dessa data togs bort. Om du försöker ta bort ett mjukt borttaget objekt påverkas inte dess utgångstid.

### <a name="saving-deleted-data"></a>Spara borttagna data

Mjuk borttagning bevarar dina data i många fall där blobbar eller blob-ögonblicksbilder tas bort eller skrivs över.

När en blob skrivs över med **Put Blob,** **Put Block**, Put **Block List**eller **Kopiera Blob** genereras en ögonblicksbild av blobens tillstånd innan skrivåtgärden genereras automatiskt. Den här ögonblicksbilden är en mjuk borttagen ögonblicksbild. Det är osynligt om inte mjuka borttagna objekt uttryckligen visas. I avsnittet [Återställning](#recovery) finns information om hur du listar borttagna objekt med mjuka borttagna objekt.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Mjuka borttagna data är grå, medan aktiva data är blå. Senast skrivna data visas under äldre data. När B0 skrivs över med B1 genereras en mjuk borttagen ögonblicksbild av B0. När B1 skrivs över med B2 genereras en mjuk borttagen ögonblicksbild av B1.*

> [!NOTE]  
> Mjuk borttagning ger bara överskrivningsskydd för kopieringsåtgärder när den är aktiverad för målblobbens konto.

> [!NOTE]  
> Mjuk borttagning ger inte överskrivningsskydd för blobbar på arkivnivån. Om en blob i arkivet skrivs över med en ny blob på valfri nivå, har den överskrivna bloben permanent upphört att gälla.

När **Ta bort blob** anropas på en ögonblicksbild markeras ögonblicksbilden som mjuk borttagen. En ny ögonblicksbild genereras inte.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Mjuka borttagna data är grå, medan aktiva data är blå. Senast skrivna data visas under äldre data. När **Snapshot Blob** anropas blir B0 en ögonblicksbild och B1 är det aktiva tillståndet för bloben. När ögonblicksbilden B0 tas bort markeras den som mjuk borttagen.*

När **Ta bort Blob** anropas på en basblob (en blob som inte i sig är en ögonblicksbild) markeras den bloben som mjuk borttagen. I enlighet med tidigare beteende returnerar **anropar ta bort blob** på en blob som har aktiva ögonblicksbilder ett fel. Anropa **borttagningsblob** på en blob med mjuka borttagna ögonblicksbilder returnerar inte ett fel. Du kan fortfarande ta bort en blob och alla dess ögonblicksbilder i en enda åtgärd när mjuk borttagning är aktiverat. Om du gör det markeras basbloben och ögonblicksbilderna som mjuka borttagna.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Mjuka borttagna data är grå, medan aktiva data är blå. Senast skrivna data visas under äldre data. Här görs ett **ta bort Blob-anrop** för att ta bort B2 och alla associerade ögonblicksbilder. Den aktiva blob, B2 och alla associerade ögonblicksbilder markeras som mjuka borttagna.*

> [!NOTE]  
> När en mjuk borttagen blob skrivs över genereras automatiskt en mjuk borttagen ögonblicksbild av blobens tillstånd före skrivåtgärden. Den nya bloben ärver nivån för den överskrivna blobben.

Mjuk borttagning sparar inte dina data i fall av behållare eller kontoborttagningar, inte heller när blobmetadata och blob-egenskaper skrivs över. Om du vill skydda ett lagringskonto från felaktig borttagning kan du konfigurera ett lås med Hjälp av Azure Resource Manager. Se azure Resource Manager-artikeln [Lås resurser för att förhindra oväntade ändringar](../../azure-resource-manager/management/lock-resources.md) om du vill veta mer.

Följande tabellinformation förväntade beteende när mjuk borttagning är aktiverat:

| REST API-åtgärd | Resurstyp | Beskrivning | Förändring i beteende |
|--------------------|---------------|-------------|--------------------|
| [Ta bort](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Tar bort lagringskontot, inklusive alla behållare och blobbar som det innehåller.                           | Ingen förändring. Behållare och blobbar i det borttagna kontot kan inte återställas. |
| [Ta bort container](/rest/api/storageservices/delete-container) | Container | Tar bort behållaren, inklusive alla blobbar som den innehåller. | Ingen förändring. Blobbar i den borttagna behållaren kan inte återställas. |
| [Placera blob](/rest/api/storageservices/put-blob) | Blockera, lägga till och sidblobar | Skapar en ny blob eller ersätter en befintlig blob i en behållare | Om den används för att ersätta en befintlig blob genereras automatiskt en ögonblicksbild av blobens tillstånd före anropet. Detta gäller även för en tidigare mjuk borttagen blob om och endast om den ersätts med en blob av samma typ (Block, Lägg till eller Sida). Om den ersätts av en blob av en annan typ upphör alla befintliga borttagna data permanent. |
| [Ta bort blob](/rest/api/storageservices/delete-blob) | Blockera, lägga till och sidblobar | Markerar en blob- eller blob-ögonblicksbild för borttagning. Blob eller ögonblicksbilden tas senare bort under skräpinsamlingen | Om den används för att ta bort en blob-ögonblicksbild markeras ögonblicksbilden som mjuk borttagen. Om den används för att ta bort en blob markeras den bloben som mjuk borttagen. |
| [Kopiera blob](/rest/api/storageservices/copy-blob) | Blockera, lägga till och sidblobar | Kopierar en källblob till en målblob i samma lagringskonto eller i ett annat lagringskonto. | Om den används för att ersätta en befintlig blob genereras automatiskt en ögonblicksbild av blobens tillstånd före anropet. Detta gäller även för en tidigare mjuk borttagen blob om och endast om den ersätts med en blob av samma typ (Block, Lägg till eller Sida). Om den ersätts av en blob av en annan typ upphör alla befintliga borttagna data permanent. |
| [Sätt block](/rest/api/storageservices/put-block) | Blockblobar | Skapar ett nytt block som ska bekräftas som en del av en blockblob. | Om det används för att binda ett block till en blob som är aktiv, finns det ingen ändring. Om det används för att binda ett block till en blob som tas bort mjukt skapas en ny blob och en ögonblicksbild genereras automatiskt för att fånga tillståndet för den mjuka borttagna bloben. |
| [Placera blocklista](/rest/api/storageservices/put-block-list) | Blockblobar | Genomför en blob genom att ange den uppsättning block-ID:er som utgör blockbloben. | Om den används för att ersätta en befintlig blob genereras automatiskt en ögonblicksbild av blobens tillstånd före anropet. Detta gäller även för en tidigare mjuk borttagen blob om och endast om det är en Block Blob. Om den ersätts av en blob av en annan typ upphör alla befintliga borttagna data permanent. |
| [Placera sida](/rest/api/storageservices/put-page) | Sidblobar | Skriver ett sidintervall till en sidblobar. | Ingen förändring. Sidblobbdata som skrivs över eller rensas med den här åtgärden sparas inte och kan inte återställas. |
| [Lägg till block](/rest/api/storageservices/append-block) | Tilläggsblobar | Skriver ett datablock till slutet av en Tilläggsblob | Ingen förändring. |
| [Ange blob-egenskaper](/rest/api/storageservices/set-blob-properties) | Blockera, lägga till och sidblobar | Anger värden för systemegenskaper som definierats för en blob. | Ingen förändring. Överskrivna blob-egenskaper kan inte återställas. |
| [Ange Blob-metadata](/rest/api/storageservices/set-blob-metadata) | Blockera, lägga till och sidblobar | Anger användardefinierade metadata för den angivna blobben som ett eller flera namnvärdespar. | Ingen förändring. Överskrivna blobmetadata kan inte återställas. |

Det är viktigt att notera att det inte automatiskt genererar ögonblicksbilder genom att anropa "Placera sida" för att skriva över eller rensa intervall för en sidblob inte automatiskt. Virtuella datordiskar backas upp av sidblobar och använder **Put Page** för att skriva data.

### <a name="recovery"></a>Återställning

Anropa [åtgärden Undelete Blob](/rest/api/storageservices/undelete-blob) på en mjuk borttagen basblob återställer den och alla associerade mjuka borttagna ögonblicksbilder som aktiva. När `Undelete Blob` du anropar åtgärden på en aktiv basblob återställer alla associerade mjuka borttagna ögonblicksbilder som aktiva. När ögonblicksbilder återställs som aktiva ser de ut som användargenererade ögonblicksbilder. De skriver inte över basbloben.

Om du vill återställa en blob till `Undelete Blob` en viss mjuk borttagen ögonblicksbild kan du anropa basblobben. Sedan kan du kopiera ögonblicksbilden över den nu aktiva blobben. Du kan också kopiera ögonblicksbilden till en ny blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Mjuka borttagna data är grå, medan aktiva data är blå. Senast skrivna data visas under äldre data. Här **anropas Undelete Blob** blob B och återställer därmed basblobben, B1 och alla associerade ögonblicksbilder, här bara B0, som aktiva. I det andra steget kopieras B0 över basbloben. Den här kopieringen genererar en mjuk borttagen ögonblicksbild av B1.*

Om du vill visa mjuka borttagna blobbar och blob-ögonblicksbilder kan du välja att inkludera borttagna data i **listblobar**. Du kan välja att bara visa mjuka borttagna basblobar eller inkludera ögonblicksbilder av mjuk borttagna blob också. För alla mjuka borttagna data kan du visa den tid då data togs bort samt antalet dagar innan data upphör att gälla permanent.

### <a name="example"></a>Exempel

Följande är konsolutdata för ett .NET-skript som laddar upp, skriver över, ögonblicksbilder, tar bort och återställer en blob med namnet *HelloWorld* när mjuk borttagning är aktiverat:

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

Se avsnittet [Nästa steg](#next-steps) för en pekare till programmet som producerade utdata.

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla mjuka borttagna data faktureras i samma takt som aktiva data. Du debiteras inte för data som tas bort permanent efter den konfigurerade kvarhållningsperioden. För en djupare inblick i ögonblicksbilder och hur de samlar avgifter, se [Förstå hur ögonblicksbilder samla avgifter](storage-blob-snapshots.md).

Du debiteras inte för transaktioner som är relaterade till automatisk generering av ögonblicksbilder. Du debiteras för **Undelete Blob-transaktioner** med kursen för skrivåtgärder.

Mer information om priser för Azure Blob Storage i allmänhet finns på [prissidan för Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

När du först aktiverar mjuk borttagning rekommenderar vi att du använder en liten kvarhållningsperiod för att bättre förstå hur funktionen kommer att påverka din faktura.

## <a name="get-started"></a>Komma igång

Följande steg visar hur du kommer igång med mjuk borttagning.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aktivera mjuk borttagning för blobbar i ditt lagringskonto med hjälp av Azure Portal:

1. Välj ditt lagringskonto i [Azure-portalen.](https://portal.azure.com/) 

2. Navigera till alternativet **Dataskydd** under **Blob-tjänst**.

3. Klicka på **Aktiverad** under **Mjuk borttagning av blobb**

4. Ange hur många dagar du vill *behålla under* **Bevarandeprinciper**

5. Välj knappen **Spara** för att bekräfta dina dataskyddsinställningar

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Om du vill visa mjuka borttagna blobbar markerar du kryssrutan **Visa borttagna blobbar.**

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Om du vill visa mjuka borttagna ögonblicksbilder för en viss blob markerar du blobben och klickar sedan på **Visa ögonblicksbilder**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Kontrollera att kryssrutan **Visa borttagna ögonblicksbilder** är markerad.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

När du klickar på en mjuk borttagen blob eller ögonblicksbild, märker de nya blob egenskaper. De anger när objektet togs bort och hur många dagar som återstår tills blob- eller blob-ögonblicksbilden har upphört att gälla permanent. Om det mjuka borttagna objektet inte är en ögonblicksbild har du också möjlighet att ta bort det.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Kom ihåg att om du tar bort en blob tar du bort alla associerade ögonblicksbilder. Om du vill ta bort mjuka borttagna ögonblicksbilder för en aktiv blob klickar du på blobben och väljer **Ta bort alla ögonblicksbilder**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

När du har ångrat ögonblicksbilder av en blob kan du klicka på **Befordra** för att kopiera en ögonblicksbild över rotbloben och därmed återställa blobben till ögonblicksbilden.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill aktivera mjuk borttagning uppdaterar du en blob-klients tjänstegenskaper. I följande exempel kan du ta bort en delmängd konton i en prenumeration:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Du kan kontrollera att mjuk borttagning har aktiverats med hjälp av följande kommando:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Om du vill återställa blobbar som tagits bort av misstag anropar du Undelete på dessa blobbar. Kom ihåg att anropa **Undelete Blob**, både på aktiva och mjuka borttagna blobbar, återställer alla associerade mjuka borttagna ögonblicksbilder som aktiva. I följande exempel anropas Undelete på alla mjuka borttagna och aktiva blobbar i en behållare:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Om du vill söka efter den aktuella principen för mjuk borttagning använder du följande kommando:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Om du vill aktivera mjuk borttagning uppdaterar du en blob-klients tjänstegenskaper:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Om du vill kontrollera att mjuk borttagning är aktiverat använder du följande kommando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Om du vill aktivera mjuk borttagning uppdaterar du en blob-klients tjänstegenskaper:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net"></a>[.NET](#tab/net)

Om du vill aktivera mjuk borttagning uppdaterar du en blob-klients tjänstegenskaper:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Om du vill återställa blobbar som tagits bort av misstag anropar du Undelete på dessa blobbar. Kom ihåg att anropa **Undelete Blob**, både på aktiva och mjuka borttagna blobbar, återställer alla associerade mjuka borttagna ögonblicksbilder som aktiva. I följande exempel anropas Undelete på alla mjuka borttagna och aktiva blobbar i en behållare:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Om du vill återställa till en viss blob-version anropar du först Undelete på en blob och kopierar sedan önskad ögonblicksbild över blobben. I följande exempel återställs en blockblob till den senast genererade ögonblicksbilden:

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

---

## <a name="special-considerations"></a>Särskilda överväganden

Om det finns en risk att dina data av misstag ändras eller tas bort av ett program eller en annan användare av lagringskontot, rekommenderas att aktivera mjuk borttagning. Om du aktiverar mjuk borttagning för ofta överskrivna data kan det leda till ökade avgifter för lagringskapacitet och ökad svarstid när blobbar listas. Du kan minska den här extra kostnaden och svarstiden genom att lagra de ofta överskrivna data i ett separat lagringskonto där mjuk borttagning är inaktiverad. 

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>För vilka lagringstjänster kan jag använda mjuk borttagning?

För närvarande är mjuk borttagning endast tillgänglig för blob (objekt) lagring.

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Är mjuk borttagning tillgänglig för alla lagringskontotyper?

Ja, mjuk borttagning är tillgängligt för Blob-lagringskonton samt för blobbar i allmänna lagringskonton (både GPv1 och GPv2). Både standard- och premiumkontotyper stöds. Mjuk borttagning är tillgänglig för ohanterade diskar, som är sidblobar under skalen. Mjuk borttagning är inte tillgängligt för hanterade diskar.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Är mjuk borttagning tillgänglig för alla lagringsnivåer?

Ja, mjuk borttagning är tillgänglig för alla lagringsnivåer, inklusive hot, cool och archive. Mjuk borttagning ger dock inte överskrivningsskydd för blobbar på arkivnivån.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Kan jag använda API:et ange Blob-nivå för att nivålabba med mjuka borttagna ögonblicksbilder?

Ja. De mjuka borttagna ögonblicksbilderna finns kvar på den ursprungliga nivån, men basbloloben flyttas till den nya nivån. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium lagringskonton har en per blob ögonblicksbild gräns på 100. Räknas mjuka borttagna ögonblicksbilder mot den här gränsen?

Nej, mjuka borttagna ögonblicksbilder räknas inte mot den här gränsen.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Kan jag aktivera mjuk borttagning för befintliga lagringskonton?

Ja, mjuk borttagning kan konfigureras för både befintliga och nya lagringskonton.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Om jag tar bort ett helt konto eller en hel behållare med mjuk borttagning aktiverad sparas alla associerade blobbar?

Nej, om du tar bort ett helt konto eller en hel behållare tas alla associerade blobbar bort permanent. Mer information om hur du skyddar ett lagringskonto från oavsiktliga borttagningar finns i [Lås resurser för att förhindra oväntade ändringar](../../azure-resource-manager/management/lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Kan jag visa kapacitetsmått för borttagna data?

Mjuka borttagna data ingår som en del av din totala lagringskontokapacitet. Mer information om spårning och övervakning av lagringskapacitet finns i [Storage Analytics](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Om jag inaktiverar mjuk borttagning, kommer jag fortfarande att kunna komma åt mjuka borttagna data?

Ja, du kommer fortfarande att kunna komma åt och återställa oexpirerade mjuka borttagna data när mjuk borttagning är inaktiverad.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Kan jag läsa och kopiera mjuka borttagna ögonblicksbilder av min blob?  

Ja, men du måste ringa Undelete på blob först.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Är mjuk borttagning tillgänglig för alla blobtyper?

Ja, mjuk borttagning är tillgänglig för blockblobar, tilläggsblobar och sidblobar.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Är mjuk borttagning tillgänglig för virtuella datordiskar?  

Mjuk borttagning är tillgänglig för både premium- och standardhanterade diskar, som är sidblobar under skalen. Mjuk borttagning hjälper dig bara att återställa data som tagits bort av **Ta bort Blob,** **Put Blob**, **Put Block List,** **Put Block** och **Copy Blob-åtgärder.** Data som skrivs över av ett anrop till **Placera sida** kan inte återställas.

En virtuell Azure-dator skriver till en ohanterade disk med anrop till **Put Page**, så att använda mjuk borttagning för att ångra skrivningar till en ohanterade disk från en Azure VM är inte ett scenario som stöds.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Måste jag ändra mina befintliga program för att använda mjuk borttagning?

Det är möjligt att dra nytta av mjuk borttagning oavsett vilken API-version du använder. Om du vill lista och återställa mjuka borttagna blobbar och blob-ögonblicksbilder måste du dock använda version 2017-07-29 av [REST-API:et för lagringstjänster](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) eller senare. Microsoft rekommenderar att du alltid använder den senaste versionen av Azure Storage API.

## <a name="next-steps"></a>Nästa steg

* [Exempelkod för .NET](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob-tjänstens REST-API](/rest/api/storageservices/blob-service-rest-api)
* [Azure Storage Replication](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Designa program med högtillgängliga program med RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Redundans- och lagringskonto för katastrofberedskap (förhandsversion) i Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
