---
title: Mjuk borttagning för Azure Storage blobbar | Microsoft Docs
description: Azure Storage erbjuder nu mjuk borttagning för BLOB-objekt så att du enkelt kan återställa dina data när de har ändrats felaktigt eller tagits bort av ett program eller någon annan lagrings konto användare.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 15db96824336c92611b9e1113c42c621f6508744
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978125"
---
# <a name="soft-delete-for-azure-storage-blobs"></a>Mjuk borttagning för Azure Storage blobbar

Azure Storage erbjuder nu mjuk borttagning för BLOB-objekt så att du enkelt kan återställa dina data när de har ändrats felaktigt eller tagits bort av ett program eller någon annan lagrings konto användare.

## <a name="how-soft-delete-works"></a>Så här fungerar mjuk borttagning

När du aktiverar mjuk borttagning kan du spara och återställa data när blobbar eller BLOB-ögonblicksbilder tas bort. Det här skyddet utökar till BLOB-data som raderas som ett resultat av en överskrivning.

När data tas bort övergår de till ett mjukt borttaget tillstånd i stället för att raderas permanent. När mjuk borttagning är aktiverat och du skriver över data, genereras en mjuk borttagen ögonblicks bild för att spara statusen för de överskrivna data. Mjuka borttagna objekt är osynliga om de inte uttryckligen anges. Du kan ange hur länge mjukt borttagna data ska kunna återställas innan de tas bort permanent.

Mjuk borttagning är bakåtkompatibelt, så du behöver inte göra några ändringar i dina program för att dra nytta av de skydd som den här funktionen erbjuder. Men [Data återställning](#recovery) introducerar en ny **Undelete-BLOB** -API.

### <a name="configuration-settings"></a>Konfigurationsinställningar

När du skapar ett nytt konto är mjuk borttagning inaktiverat som standard. Mjuk borttagning är också inaktiverat som standard för befintliga lagrings konton. Du kan aktivera och inaktivera funktionen när som helst under ett lagrings kontos livs längd.

Du kommer fortfarande att kunna komma åt och återställa Soft Deleted data när funktionen är inaktive rad, förutsatt att mjuka borttagna data sparades när funktionen tidigare var aktive rad. När du aktiverar mjuk borttagning måste du också konfigurera kvarhållningsperioden.

Kvarhållningsperioden anger hur lång tid som Soft Deleted data lagras och är tillgänglig för återställning. För blobbar och blob-ögonblicksbilder som tas bort explicit, startar kvarhållningsperioden för kvarhållningsperioden när data tas bort. För mjuka borttagna ögonblicks bilder som genereras av funktionen för mjuk borttagning när data skrivs över, startar klockan när ögonblicks bilden genereras. För närvarande kan du behålla mjuk borttagning av data i mellan 1 och 365 dagar.

Du kan när som helst ändra lagrings perioden för mjuk borttagning. En uppdaterad kvarhållningsperiod gäller endast nyligen borttagna data. Tidigare borttagna data går ut baserat på den kvarhållningsperiod som konfigurerades när dessa data togs bort. Försök att ta bort ett Soft borttaget objekt påverkar inte förfallo tiden.

### <a name="saving-deleted-data"></a>Sparar borttagna data

Mjuk borttagning bevarar dina data i många fall där blobbar eller BLOB-ögonblicksbilder tas bort eller skrivs över.

När en BLOB skrivs över med hjälp av kommandot **reblob**, list **block**, **list block**eller **copy BLOB** skapas en ögonblicks bild av blobens tillstånd innan Skriv åtgärden genereras automatiskt. Den här ögonblicks bilden är en mjuk borttagen ögonblicks bild. Det är osynligt om inte mjuka borttagna objekt anges explicit. I [återställnings](#recovery) avsnittet finns information om hur du visar mjuka borttagna objekt.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-overwrite.png)

*Mjuk borttagning av data är grått, medan aktiva data är blå. Mer nyligen skrivna data visas under äldre data. När B0 skrivs över med B1 genereras en mjuk borttagen ögonblicks bild av B0. När B1 skrivs över med B2 genereras en mjuk raderad ögonblicks bild av B1.*

> [!NOTE]  
> Mjuk borttagning ger endast överskrivning av skydd för kopierings åtgärder när den är aktive rad för mål-blobens konto.

> [!NOTE]  
> Mjuk borttagning ger inte överskrivning av blobar på Arkiv nivån. Om en BLOB i arkivet skrivs över med en ny BLOB på vilken nivå som helst upphör den överskrivna blobben att gälla permanent.

När **Delete BLOB** anropas för en ögonblicks bild markeras den ögonblicks bilden som mjuk borttagen. En ny ögonblicks bild skapas inte.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-delete-snapshot.png)

*Mjuk borttagning av data är grått, medan aktiva data är blå. Mer nyligen skrivna data visas under äldre data. När en **ögonblicks bilds-BLOB** anropas blir B0 en ögonblicks bild och B1 är det aktiva läget för blobben. När B0-ögonblicksbilden tas bort markeras den som mjuk borttagen.*

När **Delete BLOB** anropas på en bas-BLOB (en blob som inte är en ögonblicks bild) markeras denna blob som mjuk borttagen. I överensstämmelse med föregående beteende, anropar **ta bort BLOB** på en blob som har aktiva ögonblicks bilder som returnerar ett fel. Anrop av **Delete BLOB** i en blob med mjuka borttagna ögonblicks bilder returnerar inte ett fel. Du kan fortfarande ta bort en blob och alla dess ögonblicks bilder i en enskild åtgärd när mjuk borttagning är aktiverat. Då markeras bas-blob och ögonblicks bilder som mjuk borttagning.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-explicit-include.png)

*Mjuk borttagning av data är grått, medan aktiva data är blå. Mer nyligen skrivna data visas under äldre data. Här görs ett **Delete BLOB** -anrop för att ta bort B2 och alla associerade ögonblicks bilder. Den aktiva blobben, B2 och alla associerade ögonblicks bilder markeras som mjuk borttagen.*

> [!NOTE]  
> När en mjuk borttagen BLOB skrivs över skapas en mjuk raderad ögonblicks bild av blobens tillstånd innan Skriv åtgärden genereras automatiskt. Den nya blobben ärver den överskrivna blobens nivå.

Mjuk borttagning sparar inte dina data i fall av container eller konto borttagningar, eller när BLOB-metadata och blob-egenskaper skrivs över. Om du vill skydda ett lagrings konto från felaktig borttagning kan du konfigurera ett lås med hjälp av Azure Resource Manager. Se Azure Resource Manager artikel [Lås resurser för att förhindra oväntade ändringar](../../azure-resource-manager/resource-group-lock-resources.md) av mer information.

Följande tabell information förväntas när mjuk borttagning är aktiverat:

| REST API åtgärd | Resurstyp | Beskrivning | Funktions förändring |
|--------------------|---------------|-------------|--------------------|
| [Ta bort](/rest/api/storagerp/StorageAccounts/Delete) | Konto | Tar bort lagrings kontot, inklusive alla behållare och blobbar som det innehåller.                           | Ingen ändring. Behållare och blobbar i det borttagna kontot går inte att återskapa. |
| [Ta bort container](/rest/api/storageservices/delete-container) | Container | Tar bort behållaren, inklusive alla blobbar som den innehåller. | Ingen ändring. Blobbar i den borttagna behållaren går inte att återskapa. |
| [Placera blob](/rest/api/storageservices/put-blob) | Blockera, lägga till och Page blobbar | Skapar en ny BLOB eller ersätter en befintlig BLOB i en behållare | Om den används för att ersätta en befintlig BLOB genereras en ögonblicks bild av blobens tillstånd före anropet automatiskt. Detta gäller även för en tidigare mjuk borttagen BLOB om den ersätts av en blob av samma typ (block, tillägg eller sida). Om den ersätts av en blob av en annan typ, kommer alla befintliga data för mjuk borttagning att upphöra att gälla permanent. |
| [Ta bort blob](/rest/api/storageservices/delete-blob) | Blockera, lägga till och Page blobbar | Markerar en BLOB-eller BLOB-ögonblicksbild för borttagning. Blobben eller ögonblicks bilden raderas senare under skräp insamlingen | Om den används för att ta bort en BLOB-ögonblicksbild, markeras den ögonblicks bilden som mjuk borttagen. Om den används för att ta bort en BLOB markeras denna blob som mjuk borttagning. |
| [Kopiera blob](/rest/api/storageservices/copy-blob) | Blockera, lägga till och Page blobbar | Kopierar en käll-blob till en mål-BLOB i samma lagrings konto eller i ett annat lagrings konto. | Om den används för att ersätta en befintlig BLOB genereras en ögonblicks bild av blobens tillstånd före anropet automatiskt. Detta gäller även för en tidigare mjuk borttagen BLOB om den ersätts av en blob av samma typ (block, tillägg eller sida). Om den ersätts av en blob av en annan typ, kommer alla befintliga data för mjuk borttagning att upphöra att gälla permanent. |
| [Spärra block](/rest/api/storageservices/put-block) | Blockblob-objekt | Skapar ett nytt block som ska allokeras som en del av en Block-Blob. | Om det används för att genomföra ett block till en blob som är aktiv, sker ingen ändring. Om den används för att genomföra ett block till en blob som är mjuk borttagning, skapas en ny blob och en ögonblicks bild skapas automatiskt för att avbilda statusen för den mjuka borttagna blobben. |
| [Lista över blockerade](/rest/api/storageservices/put-block-list) | Blockblob-objekt | Genomför en BLOB genom att ange den uppsättning block-ID: n som utgör block-bloben. | Om den används för att ersätta en befintlig BLOB genereras en ögonblicks bild av blobens tillstånd före anropet automatiskt. Detta gäller även för en tidigare mjuk borttagen BLOB om det är en Block-Blob. Om den ersätts av en blob av en annan typ, kommer alla befintliga data för mjuk borttagning att upphöra att gälla permanent. |
| [Placerings sida](/rest/api/storageservices/put-page) | Sidblob-objekt | Skriver ett intervall med sidor till en sid-blob. | Ingen ändring. Sid-BLOB-data som skrivs över eller rensas med den här åtgärden sparas inte och går inte att återskapa. |
| [Lägg till block](/rest/api/storageservices/append-block) | Tilläggsblobar | Skriver ett data block till slutet av en tilläggs-BLOB | Ingen ändring. |
| [Ange BLOB-egenskaper](/rest/api/storageservices/set-blob-properties) | Blockera, lägga till och Page blobbar | Anger värden för system egenskaper som definierats för en blob. | Ingen ändring. Det går inte att återskapa de överskrivna BLOB-egenskaperna. |
| [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata) | Blockera, lägga till och Page blobbar | Anger användardefinierade metadata för angiven blob som ett eller flera namn/värde-par. | Ingen ändring. Överskrivna BLOB-metadata går inte att återskapa. |

Det är viktigt att Observera att anrop av "placerings sida" för att skriva över eller rensa intervall i en sid-BLOB inte automatiskt genererar ögonblicks bilder. Virtuella dator diskar backas upp av Page blobbar och använder **sidan sätt** för att skriva data.

### <a name="recovery"></a>Återställning

Om du anropar [Undelete-blobben](/rest/api/storageservices/undelete-blob) i en mjuk borttagen bas-BLOB återställs den och alla tillhör ande mjuka borttagna ögonblicks bilder aktive rad. Om du anropar `Undelete Blob`-åtgärden för en aktiv bas-BLOB återställs alla tillhör ande överförbara borttagna ögonblicks bilder som aktiva. När ögonblicks bilder återställs som aktiva ser de ut som användardefinierade ögonblicks bilder. de skriver inte över bas-bloben.

Om du vill återställa en blob till en angiven mjuk, borttagen ögonblicks bild kan du anropa `Undelete Blob` på bas-bloben. Sedan kan du kopiera ögonblicks bilden över den nu aktiva blobben. Du kan också kopiera ögonblicks bilden till en ny blob.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-recover.png)

*Mjuk borttagning av data är grått, medan aktiva data är blå. Mer nyligen skrivna data visas under äldre data. Här kallas **Undelete BLOB** i BLOB B, vilket återställer bas-bloben, B1 och alla associerade ögonblicks bilder, precis B0, som aktiva. I det andra steget kopieras B0 över bas-bloben. Den här kopierings åtgärden genererar en mjuk borttagen ögonblicks bild av B1.*

Om du vill visa mjuka borttagna blobbar och blob-ögonblicksbilder kan du välja att inkludera borttagna data i **list blobbar**. Du kan välja att bara Visa mjuka borttagna bas-blobar, eller inkludera mjuka borttagna BLOB-ögonblicksbilder. För all mjuk borttagning av data kan du Visa den tid då data togs bort samt antalet dagar innan data kommer att upphöra att gälla permanent.

### <a name="example"></a>Exempel

Följande är konsol resultatet av ett .NET-skript som laddar upp, skriver över, ögonblicks bilder, tar bort och återställer en blob med namnet *HelloWorld* när mjuk borttagning är aktiverat:

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

I avsnittet [Nästa steg](#next-steps) visas en pekare till det program som skapade utdata.

## <a name="pricing-and-billing"></a>Priser och fakturering

Alla mjuka borttagna data debiteras enligt samma taxa som aktiva data. Du kommer inte att debiteras för data som tas bort permanent efter den konfigurerade kvarhållningsperioden. En djupare inblick i ögonblicks bilder och hur de påförs debiteras finns i [förstå hur ögonblicks bilder debiteras](storage-blob-snapshots.md).

Du debiteras inte för transaktioner som rör automatisk generering av ögonblicks bilder. Du faktureras för att **ta bort BLOB** -transaktioner enligt priset för Skriv åtgärder.

Mer information om priser för Azure Blob Storage i allmänhet finns på [sidan med priser för azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

När du först aktiverar mjuk borttagning rekommenderar vi att du använder en liten kvarhållningsperiod för att bättre förstå hur funktionen kommer att påverka din faktura.

## <a name="get-started"></a>Kom i gång

Följande steg visar hur du kommer igång med mjuk borttagning.

# <a name="portaltabazure-portal"></a>[Portalen](#tab/azure-portal)

Aktivera mjuk borttagning för blobbar på ditt lagrings konto med hjälp av Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt lagrings konto. 

2. Navigera till alternativet för **data skydd** under **BLOB service**.

3. Klicka på **aktive rad** under **BLOB Soft Delete**

4. Ange antalet dagar som du vill *behålla för* **bevarande principer**

5. Välj knappen **Spara** för att bekräfta dina data skydds inställningar

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

Om du vill visa mjuka borttagna blobbar markerar du kryss rutan **Visa borttagna blobbar** .

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted.png)

Om du vill visa mjuka borttagna ögonblicks bilder för en specifik BLOB väljer du bloben och klickar sedan på **Visa ögonblicks bilder**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Kontrol lera att kryss rutan **Visa borttagna ögonblicks bilder** är markerad.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Lägg märke till de nya blobb egenskaperna när du klickar på en mjuk borttagen BLOB eller ögonblicks bild. De anger när objektet togs bort och hur många dagar som återstår tills BLOB-eller BLOB-ögonblicksbilden har gått ut permanent. Om det mjuka borttagna objektet inte är en ögonblicks bild, kan du också välja att ta bort det.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-properties.png)

Kom ihåg att om du tar bort en BLOB raderas även alla associerade ögonblicks bilder. Om du vill ta bort mjuka borttagna ögonblicks bilder för en aktiv BLOB, klickar du på blobben och väljer **ångra borttagning av alla ögonblicks bilder**.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

När du tar bort en blobs ögonblicks bilder kan du klicka på **befordra** för att kopiera en ögonblicks bild över rot-bloben så att bloben kan återställas till ögonblicks bilden.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper. I följande exempel aktive ras mjuk borttagning för en delmängd av konton i en prenumeration:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
Du kan kontrol lera att mjuk borttagning har Aktiver ATS genom att använda följande kommando:

```powershell
$MatchingAccounts | Get-AzStorageServiceProperty -ServiceType Blob
```

Om du vill återställa blobar som tagits bort av misstag kan du anropa Undelete på dessa blobbar. Kom ihåg att om du anropar **Undelete-BLOB**, både på aktiva och mjuka borttagna blobar, återställs alla tillhör ande mjuka borttagna ögonblicks bilder som aktiva. I följande exempel anropar Undelete för alla mjuka borttagna och aktiva blobbar i en behållare:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Använd följande kommando för att hitta den aktuella bevarande principen för mjuk borttagning:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="clitabazure-cli"></a>[CLI](#tab/azure-CLI)

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Om du vill verifiera att mjuk borttagning är aktiverat, använder du följande kommando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper:

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

# <a name="nettabnet"></a>[NET](#tab/net)

Om du vill aktivera mjuk borttagning uppdaterar du en BLOB-klients tjänst egenskaper:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Om du vill återställa blobar som tagits bort av misstag kan du anropa Undelete på dessa blobbar. Kom ihåg att om du anropar **Undelete-BLOB**, både på aktiva och mjuka borttagna blobar, återställs alla tillhör ande mjuka borttagna ögonblicks bilder som aktiva. I följande exempel anropar Undelete för alla mjuka borttagna och aktiva blobbar i en behållare:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Om du vill återställa till en angiven blob-version måste du först anropa Undelete på en blob och sedan kopiera önskad ögonblicks bild över blobben. I följande exempel återställs en Block-Blob till den senast skapade ögonblicks bilden:

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

## <a name="special-considerations"></a>Att tänka på

Om det finns en chans att dina data ändras eller tas bort av misstag av ett program eller en annan lagrings konto användare rekommenderas du att aktivera mjuk borttagning. Om du aktiverar mjuk borttagning för ofta överskrivna data kan det leda till ökade kapacitets kostnader för lagring och ökad fördröjning vid registrering av blobbar. Du kan minimera denna ytterligare kostnad och svars tid genom att lagra ofta skrivna data i ett separat lagrings konto där mjuk borttagning har inaktiverats. 

## <a name="faq"></a>FAQ

### <a name="for-which-storage-services-can-i-use-soft-delete"></a>För vilka lagrings tjänster kan jag använda mjuk borttagning?

Mjuk borttagning är för närvarande endast tillgängligt för blob-lagring (objekt).

### <a name="is-soft-delete-available-for-all-storage-account-types"></a>Är mjuk borttagning tillgänglig för alla typer av lagrings konton?

Ja, mjuk borttagning är tillgängligt för Blob Storage-konton samt för blobbar i generell användning (både GPv1 och GPv2) lagrings konton. Det finns stöd för både standard-och Premium konto typer. Mjuk borttagning är tillgängligt för ohanterade diskar, som är sid-blobar under försättsblad. Mjuk borttagning är inte tillgängligt för hanterade diskar.

### <a name="is-soft-delete-available-for-all-storage-tiers"></a>Är mjuk borttagning tillgänglig för alla lagrings nivåer?

Ja, mjuk borttagning är tillgängligt för alla lagrings nivåer, inklusive frekvent, låg frekvent och Arkiv lag ring. Mjuk borttagning ger dock inte överskrivning av blobar på Arkiv nivå.

### <a name="can-i-use-the-set-blob-tier-api-to-tier-blobs-with-soft-deleted-snapshots"></a>Kan jag använda API-API: et för att ta bort blobar med mjuk borttagna ögonblicks bilder?

Ja. De mjuka borttagna ögonblicks bilderna blir kvar på den ursprungliga nivån, men bas-bloben flyttas till den nya nivån. 

### <a name="premium-storage-accounts-have-a-per-blob-snapshot-limit-of-100-do-soft-deleted-snapshots-count-toward-this-limit"></a>Premium Storage-konton har en gräns per BLOB-ögonblicksbild på 100. Räknas den mjuka borttagna ögonblicks bilder mot den här gränsen?

Nej, mjuka borttagna ögonblicks bilder räknas inte mot den här gränsen.

### <a name="can-i-turn-on-soft-delete-for-existing-storage-accounts"></a>Kan jag aktivera mjuk borttagning för befintliga lagrings konton?

Ja, mjuk borttagning kan konfigureras för både befintliga och nya lagrings konton.

### <a name="if-i-delete-an-entire-account-or-container-with-soft-delete-turned-on-will-all-associated-blobs-be-saved"></a>Om jag tar bort ett helt konto eller en behållare med mjuk borttagning aktiverat, kommer alla associerade blobbar att sparas?

Nej, om du tar bort ett helt konto eller en behållare tas alla kopplade blobbar bort permanent. Mer information om hur du skyddar ett lagrings konto från oavsiktliga borttagningar finns i [Lås resurser för att förhindra oväntade ändringar](../../azure-resource-manager/resource-group-lock-resources.md).

### <a name="can-i-view-capacity-metrics-for-deleted-data"></a>Kan jag Visa kapacitets mått för borttagna data?

Soft Deleted data ingår som en del av din totala kapacitet för lagrings kontot. Mer information om att spåra och övervaka lagrings kapacitet finns [Lagringsanalys](../common/storage-analytics.md).

### <a name="if-i-turn-off-soft-delete-will-i-still-be-able-to-access-soft-deleted-data"></a>Om jag inaktiverar mjuk borttagning kommer jag fortfarande att kunna komma åt mjuka borttagna data?

Ja, du kommer fortfarande att kunna komma åt och återställa ej utgångna Soft Deleted-data när mjuk borttagning är inaktive rad.

### <a name="can-i-read-and-copy-out-soft-deleted-snapshots-of-my-blob"></a>Kan jag läsa och kopiera mjuk borttagna ögonblicks bilder av min BLOB?  

Ja, men du måste anropa Undelete i blobben först.

### <a name="is-soft-delete-available-for-all-blob-types"></a>Är mjuk borttagning tillgänglig för alla BLOB-typer?

Ja, mjuk borttagning är tillgängligt för block-blobar, tillägg av blobbar och sid-blobar.

### <a name="is-soft-delete-available-for-virtual-machine-disks"></a>Är mjuk borttagning tillgängligt för virtuella dator diskar?  

Mjuk borttagning är tillgängligt för både Premium-och ohanterade icke-hanterade diskar, som är sid-blobar under försättsblad. Med mjuk borttagning kan du bara återställa data som har tagits bort genom att **ta bort BLOB**, **Skicka BLOB**, **lagra blockeringslistan**, **lagra block** och **Kopiera BLOB** -åtgärder. Data som skrivs över av ett anrop till en **sida** kan inte återställas.

En virtuell Azure-dator skriver till en ohanterad disk med hjälp av anrop till att använda **sidan**, så Använd mjuk borttagning för att ångra skrivningar till en ohanterad disk från en virtuell Azure-dator är inte ett scenario som stöds.

### <a name="do-i-need-to-change-my-existing-applications-to-use-soft-delete"></a>Måste jag ändra mina befintliga program för att använda mjuk borttagning?

Det är möjligt att dra nytta av mjuk borttagning oavsett vilken API-version du använder. Om du vill visa och återställa mjuka borttagna blobbar och blob-ögonblicksbilder måste du dock använda version 2017-07-29 av [lagrings tjänsterna REST API](https://docs.microsoft.com/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) eller senare. Microsoft rekommenderar att alltid använda den senaste versionen av Azure Storage API.

## <a name="next-steps"></a>Nästa steg

* [.NET-exempel kod](https://github.com/Azure-Samples/storage-dotnet-blob-soft-delete)
* [Blob-tjänstens REST-API](/rest/api/storageservices/blob-service-rest-api)
* [Azure Storage replikering](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Utforma hög tillgängliga program med RA-GRS](../common/storage-designing-ha-apps-with-ragrs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Haveri beredskap och lagrings konto redundans (för hands version) i Azure Storage](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
