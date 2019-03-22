---
title: Hantera Azure Storage-livscykel
description: Lär dig mer om att skapa livscykel principregler övergången föråldras data från frekvent till lågfrekvent nivå eller arkivnivå.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: 1428c2925ab57642899732bd4504b2d5b38781a8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315155"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Hantera Azure Blob storage livscykel (förhandsversion)

Datauppsättningar har unika livscykler. Tidigt i livscykeln, personer åtkomst till vissa data ofta. Men behov av åtkomst sjunker drastiskt som data registrerar dig. Vissa data förblir inaktiva i molnet och används sällan lagras en gång. Vissa data upphör dagar eller månader när du har skapat, medan andra datauppsättningar läses aktivt och ändras under hela sin livslängd. Azure Blob storage livscykelhantering (förhandsversion) erbjuder en omfattande, regelbaserad princip för GPv2- och Blob storage-konton. Använda för att överföra data till lämpliga åtkomstnivåer eller upphör att gälla i slutet av den data livscykel.

Principen för livscykelhantering kan du:

- Överföra BLOB-och en mer lågfrekvent lagringsnivå (frekvent till lågfrekvent, frekvent för att arkivera eller lågfrekvent för att arkivera) att optimera för prestanda och kostnader
- Ta bort blobar i slutet av deras livscykler
- Definiera regler som ska köras en gång per dag på nivån för storage-konto
- Tillämpa regler på behållare eller en delmängd av BLOB-objekt (med prefix som filter)

Föreställ dig ett scenario där en datauppsättning hämtar frekvent åtkomst i början av livscykel, men sedan bara ibland efter två veckor. Utöver den första månaden får datauppsättningen i sällan. I det här scenariot är lagring för frekvent / bäst under de tidiga stadierna. Lågfrekvent lagring är mest lämplig för tillfällig åtkomst och archive storage är det bästa alternativet nivå när du registrerar dig data över en månad. Du kan utforma de billigaste lagringsalternativ för dina behov genom att justera lagringsnivåer i jämfört med åldern på data. Livscykeln för hantering av principregler är tillgängliga att flytta föråldras data till mer lågfrekvent nivå för att uppnå den här ändringen.

## <a name="storage-account-support"></a>Stöd för Storage-konton

Principen för livscykelhantering är tillgängligt med både generell användning v2 (GPv2)-konton och Blob storage-konton. Du kan uppgradera ett befintligt konto för generell användning (GPv1) till ett GPv2-konto via en enkel process med ett klick i Azure-portalen. Mer information om lagringskonton finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).  

## <a name="pricing"></a>Prissättning 

Funktionen för hantering av livscykeln är kostnadsfritt i förhandsversionen. Kunderna debiteras den vanliga åtgärd kostnaden för den [lista Blobar](https://docs.microsoft.com/rest/api/storageservices/list-blobs) och [ange Blobnivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-anrop. Mer information om priser finns i [blockblobpriserna](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>Registrera dig för förhandsversion 
Om du vill registrera i offentlig förhandsversion, måste du ansöka om att registrera den här funktionen till din prenumeration. Begäranden godkänns vanligtvis inom 72 timmar. Vid godkännande är alla befintliga och nya GPv2 eller Blob storage-konton i följande regioner funktionen: Västra USA 2, USA, västra centrala, USA, östra 2 och Västeuropa. Preview stöder endast blockblob. Precis som med de flesta förhandsversioner bör inte du använda den här funktionen för produktionsarbetsbelastningar tills den når GA.

Kör följande PowerShell eller CLI-kommandon för att skicka en begäran.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Att skicka en begäran:

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Du kan kontrollera registreringsstatus för godkännande med följande kommando:
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Med godkännande och rätt registrering kan du få den *registrerad* tillstånd när du skickar in föregående begäranden.

### <a name="azure-cli"></a>Azure CLI

Att skicka en begäran: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Du kan kontrollera registreringsstatus för godkännande med följande kommando:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Med godkännande och rätt registrering kan du få den *registrerad* tillstånd när du skickar in föregående begäranden.


## <a name="add-or-remove-a-policy"></a>Lägg till eller ta bort en princip 

Du kan lägga till, redigera eller ta bort en princip med hjälp av Azure-portalen [PowerShell](https://www.powershellgallery.com/packages/Az.Storage), [Azure CLI](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create), [REST API: er](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate), eller klientverktyg på följande språk: [.NET ](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **alla resurser** och välj sedan ditt lagringskonto.

3. Välj **livscykelhantering (förhandsversion)** grupperade under Blob-tjänsten för att visa eller ändra din princip.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }'

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>Azure CLI

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
> Om du aktiverar brandväggsregler för ditt lagringskonto, blockeras livscykeln för hantering av begäranden. Du kan låsa upp dessa begäranden genom att ange undantag. Mer information finns i avsnittet undantag i [konfigurera brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policy"></a>Princip

En princip för livscykelhantering är en samling regler i ett JSON-dokument:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```


En princip kräver två parametrar:

| Parameternamn | Parametertyp | Anteckningar |
|----------------|----------------|-------|
| version        | En sträng som är uttryckt i `x.x` | Versionsnumret för förhandsversionen är 0,5. |
| regler          | En matris med regelobjekt | Du måste minst en regel i varje princip. Du kan ange upp till 4 regler per princip för förhandsversionen. |

Varje regel i principen kräver tre parametrar:

| Parameternamn | Parametertyp | Anteckningar |
|----------------|----------------|-------|
| Namn           | String | Ett regelnamn kan innehålla valfria kombinationer av alfanumeriska tecken. Regelnamnet är skiftlägeskänsligt. Det måste vara unika inom en princip. |
| typ           | Ett uppräkningsvärde | Det giltiga värdet för förhandsversionen är `Lifecycle`. |
| definition     | Ett objekt som definierar regeln för livscykel | Varje definition består av ett filter och en åtgärd. |

## <a name="rules"></a>Regler

Varje Regeldefinitionen innehåller en filteruppsättning och en uppsättning åtgärder. Den [filtrera set](#rule-filters) begränsar regelåtgärder till en viss uppsättning objekt i en behållare och objekt som namn. Den [åtgärd set](#rule-actions) gäller nivån eller ta bort åtgärder för att en filtrerad uppsättning objekt.

### <a name="sample-rule"></a>Exempelregel
Följande Exempelregel filtrerar konto för att köra åtgärderna som bara på `container1/foo`. Kör följande åtgärder för alla objekt som finns inuti `container1` **AND** börjar med `foo`: 

- Nivå-blob till lågfrekvent nivå 30 dagar efter senaste ändring
- Nivå-blob att arkivera nivån 90 dagar efter senaste ändringen
- Ta bort blob 2,555 dagar (sju år) efter senaste ändring
- Ta bort blobögonblicksbilderna 90 dagar efter ögonblicksbilder skapas

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "ruleFoo",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

```

### <a name="rule-filters"></a>Regeln filter

Filter begränsar regelåtgärder till en delmängd av blobbar i lagringskontot. Om mer än ett filter har definierats använder en logisk `AND` körs på alla filter.

I förhandsversionen är giltiga filter:

| Filternamn | Filtertyp | Anteckningar | Obligatorisk |
|-------------|-------------|-------|-------------|
| blobTypes   | En matris med fördefinierade enum-värden. | Förhandsversionen endast stöd för `blockBlob`. | Ja |
| prefixMatch | En matris med strängar för prefix som ska matcha. En sträng med prefixet måste börja med ett behållarnamn. Exempel: Om du vill matcha alla blobar under ”https:\//myaccount.blob.core.windows.net/container1/foo/...” för en regel i prefixMatch är `container1/foo`. | Om du inte definierar prefixMatch gäller reglerna för alla blobbar i kontot. | Nej |

### <a name="rule-actions"></a>Regelåtgärder

Åtgärder som tillämpas på filtrerade blobarna när körningen villkoret är uppfyllt.

I förhandsversion stöder livscykelhantering lagringsnivåer och borttagning av blobar och borttagning av blob-ögonblicksbilder. Ange minst en åtgärd för varje regel för blobar eller blobögonblicksbilder.

| Åtgärd        | Grundläggande Blob                                   | Ögonblicksbild      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Stöd för blobar för närvarande på frekvent nivå         | Stöds inte |
| tierToArchive | Stöd för blobar för närvarande på frekvent eller lågfrekvent nivå | Stöds inte |
| delete        | Stöds                                   | Stöds     |

> [!NOTE]
> Om du definierar mer än en åtgärd på samma blob gäller livscykelhantering den billigaste åtgärden blob. Till exempel åtgärden `delete` är billigare än åtgärd `tierToArchive`. Åtgärden `tierToArchive` är billigare än åtgärd `tierToCool`.

I förhandsversion baseras åtgärd körning villkor på ålder. Grundläggande blobbar använda det senaste ändringsdatum för att spåra ålder och blob-ögonblicksbilder används tiden för skapandet av ögonblicksbild för att spåra ålder.

| Åtgärdsstatus för körning | Villkorsvärdet | Beskrivning |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Heltalsvärde som anger ålder i dagar | Giltiga villkor för grundläggande blob-åtgärder |
| daysAfterCreationGreaterThan     | Heltalsvärde som anger ålder i dagar | Giltiga villkor för åtgärder för blob-ögonblicksbild | 

## <a name="examples"></a>Exempel
I följande exempel visar hur du hanterar vanliga scenarier med reglerna för livscykel.

### <a name="move-aging-data-to-a-cooler-tier"></a>Flytta föråldras data till en mer lågfrekvent nivå

Det här exemplet visar hur du övergår blockblob-objekt med prefixet `container1/foo` eller `container2/bar`. Principen övergår blobar som inte har ändrats i över 30 dagar till lågfrekvent lagring och BLOB-objekt som inte har ändrats i 90 dagar som arkivnivån:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "agingRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Arkivera data vid inmatning 

Vissa data förblir inaktiva i molnet och är data som sällan, kanske aldrig, en gång lagras. Arkivera data omedelbart när det matas in. Följande livscykelprincipen har konfigurerats för att arkivera data vid inmatning. Det här exemplet övergångar blockblobbar i storage-konto i behållaren `archivecontainer` direkt till en arkivnivån. Omedelbar övergången görs genom att fungera för blobbar 0 dagar efter tid för senaste ändring:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "archiveRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Ta bort data utifrån ålder

Vissa data förväntas upphör att gälla dagar eller månader när du har skapat för att minska kostnaderna och uppfyller myndighetskraven för. Du kan konfigurera en princip för livscykelhantering för att ta bort data genom att ta bort baserat på data ålder. I följande exempel visas en princip som tar bort alla blockblob-objekt (med inget prefix har angetts) som är äldre än 365 dagar.

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "expirationRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Ta bort gamla ögonblicksbilder

För data som ändras och komma åt regelbundet under hela dess livslängd, används ögonblicksbilder ofta för att spåra äldre versioner av data. Du kan skapa en princip som tar bort gamla ögonblicksbilder utifrån ögonblicksbild ålder. Ögonblicksbild ålder bestäms genom att utvärdera tiden för skapandet av ögonblicksbild. Den här principregeln borttagningar blockera blob-ögonblicksbilder i behållaren `activedata` som är 90 dagar eller äldre när ögonblicksbilden har skapats.

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "snapshotRule",
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>Vanliga frågor och svar – jag har skapat en ny princip, varför åtgärderna som inte körs direkt? 

Plattformen körs policyn för onlinelivscykeln en gång om dagen. När du har angett en ny princip kan ta det upp till 24 timmar för vissa åtgärder (till exempel lagringsnivåer och borttagning) för att starta och köra.  

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att återställa data efter oavsiktlig borttagning:

- [Mjuk borttagning för Azure Storage-blobbar](../blobs/storage-blob-soft-delete.md)
