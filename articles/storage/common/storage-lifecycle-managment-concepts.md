---
title: Hantera Azure Storage-livscykel
description: Lär dig mer om att skapa livscykel principregler övergången againg data från frekvent till lågfrekvent lagring och arkivlagring nivåer.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.component: common
ms.openlocfilehash: 25e6fba6ac8aa34c0c30fd61f5fe297b94720439
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983675"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Hantera Azure Blob Storage livscykel (förhandsversion)

Datauppsättningar har unika livscykler. Vissa data används ofta tidigt i livscykeln, men behov av åtkomst sjunker drastiskt som data registrerar dig. Vissa data förblir inaktiva i molnet och används sällan lagras en gång. Vissa data upphör att gälla av dagar eller månader när du har skapat, medan andra datauppsättningar läses aktivt och ändras under hela sin livslängd. Livscykelhantering för Azure Blob Storage (förhandsversion) erbjuder en omfattande, regel-baserad princip som du kan använda för att överföra data till bästa åtkomstnivå och för att ta bort data i slutet av livscykeln.

Princip för livscykelhantering hjälper dig att:

- Överföra BLOB-och en mer lågfrekvent lagringsnivå (frekvent till lågfrekvent, frekvent till Arkiv, eller lågfrekvent till Arkiv) till att optimera prestanda och kostnader
- Ta bort blobar i slutet av deras livscykler
- Definiera regler som ska köras en gång om dagen på lagringskontonivån (stöds både GPv2 och Blob storage-konton)
- Tillämpa regler på behållare eller en delmängd av BLOB-objekt (med prefix som filter)

Överväg att en uppsättning data som används ofta under tidigt under livscykeln, krävs bara ibland efter två veckor, och används sällan efter en månad och mycket mer. I det här scenariot frekvent lagring är bäst under de tidiga stadierna lågfrekvent lagring är mest lämplig för tillfällig åtkomst och archive storage är det bästa alternativet nivå när du registrerar dig data över en månad. Du kan utforma de billigaste lagringsalternativ för dina behov genom att justera lagringsnivåer i jämfört med åldern på data. För att uppnå den här ändringen finns principer för hantering av livscykeln för att flytta föråldras data till mer lågfrekvent nivå.

## <a name="storage-account-support"></a>Stöd för Storage-konton

Princip för livscykelhantering är tillgängligt med både generell användning v2 (GPv2)-konto och Blob Storage-konto. Du kan konvertera ett befintligt konto för generell användning (GPv1) till ett GPv2-konto via en enkel process för ett klick i Azure-portalen. Mer information om lagringskonton finns i [översikt över Azure storage-konton](../common/storage-account-overview.md) vill veta mer.  

## <a name="pricing"></a>Prissättning 

Funktionen för hantering av livscykeln är kostnadsfritt i förhandsversionen. Kunderna debiteras den vanliga åtgärd kostnaden för den [lista Blobar](https://docs.microsoft.com/rest/api/storageservices/list-blobs) och [ange Blobnivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-anrop. Se [blockblobpriserna](https://azure.microsoft.com/pricing/details/storage/blobs/) mer information om priser.

## <a name="register-for-preview"></a>Registrera dig för förhandsversion 
Om du vill registrera i offentlig förhandsversion, behöver du ansöka om att registrera den här funktionen till din prenumeration. När din begäran har godkänts (inom ett par dagar), har alla befintliga och nya GPv2 eller Blob Storage-konto i USA, västra 2, västra centrala USA och Västeuropa funktionen aktiverad. I förhandsversionen stöds endast blockblob. Precis som med de flesta förhandsversionerna av ska den här funktionen inte användas för produktionsarbetsbelastningar tills den når GA.

Kör följande PowerShell eller CLI-kommandon för att skicka en begäran.

### <a name="powershell"></a>PowerShell

Att skicka en begäran:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Du kan kontrollera registreringsstatus för godkännande med följande kommando:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Om funktionen är godkänd och registrerats korrekt, bör du få ”Registered” tillstånd.

### <a name="azure-cli"></a>Azure CLI

Att skicka en begäran: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Du kan kontrollera registreringsstatus för godkännande med följande kommando:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Om funktionen är godkänd och registrerats korrekt, bör du få ”Registered” tillstånd. 


## <a name="add-or-remove-policies"></a>Lägga till eller ta bort principer 

Du kan lägga till, redigera eller ta bort en princip med hjälp av Azure-portalen [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), [REST API: er](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/createorupdatemanagementpolicies), eller klientverktyg på följande språk: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](   https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Gå till ditt lagringskonto genom att välja Alla resurser och välj sedan ditt lagringskonto.

3. I bladet inställningar klickar du på **livscykelhantering** grupperade under Blob-tjänsten för att visa och/eller ändra principer.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }' 

Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

> [!NOTE]
Om du aktiverar brandväggsregler för ditt lagringskonto, blockeras livscykeln för hantering av begäranden. Du kan låsa upp den genom att ange undantag. Mer information finns i avsnittet undantag vid [konfigurera brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Principer

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


I en princip krävs två parametrar:

| Parameternamn | Parametertyp | Anteckningar |
|----------------|----------------|-------|
| version        | En sträng som är uttryckt i `x.x` | Versionsnumret för förhandsversionen är 0,5 |
| regler          | En matris med regelobjekt | Du måste minst en regel i varje princip. Du kan ange upp till 4 regler per princip för förhandsversionen. |

Parametrar som krävs i en regel är:

| Parameternamn | Parametertyp | Anteckningar |
|----------------|----------------|-------|
| Namn           | Sträng | Ett regelnamn kan innehålla en kombination av alfanumeriska tecken. Regelnamnet är skiftlägeskänsligt. Det måste vara unika inom en princip. |
| typ           | Ett uppräkningsvärde | Det giltiga värdet för förhandsversionen är `Lifecycle` |
| definition     | Ett objekt som definierar regeln för livscykel | Varje definition består med en filteruppsättning och en uppsättning åtgärder. |

## <a name="rules"></a>Regler

Varje Regeldefinitionen innehåller en filteruppsättning och en uppsättning åtgärder. Följande Exempelregel ändrar nivån för grundläggande blockblob-objekt med prefixet `container1/foo`. Dessa regler har definierats som i principen:

- Nivå-blob till lågfrekvent lagring 30 dagar efter senaste ändring
- Nivå-blob till arkivlagring 90 dagar efter senaste ändringen
- Ta bort blob 2,555 dagar (7 år) efter senaste ändring
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

Filter begränsar regelåtgärder till en delmängd av blobbar i lagringskontot. Om flera filter definieras en logisk `AND` utförs på alla filter.

I förhandsversionen är giltiga filter:

| Filternamn | Filtertyp | Anteckningar | Krävs |
|-------------|-------------|-------|-------------|
| blobTypes   | En matris med fördefinierade enum-värden. | I förhandsversionen endast `blockBlob` stöds. | Ja |
| prefixMatch | En matris med strängar för prefix som ska matcha. En sträng med prefixet måste börja med ett behållarnamn. Exempel: om alla BLOB-objekt ”https://myaccount.blob.core.windows.net/mycontainer/mydir/..”. som ska matchas för en regel är prefixet ”mycontainer/MinMapp”. | Om det inte är definierad gäller med den här regeln för alla blobbar i kontot. | Nej |

### <a name="rule-actions"></a>Regelåtgärder

Åtgärder som tillämpas på filtrerade blobarna när körningen villkoret är uppfyllt.

I förhandsversion stöder livscykelhantering lagringsnivåer och borttagning av blob och borttagning av blob-ögonblicksbilder. Varje regel måste ha minst en åtgärd som definierats för blobar eller blobögonblicksbilder.

| Åtgärd        | Grundläggande Blob                                   | Ögonblicksbild      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Stöd för blobar för närvarande på frekvent nivå         | Stöds inte |
| tierToArchive | Stöd för blobar för närvarande på frekvent eller lågfrekvent nivå | Stöds inte |
| delete        | Stöds                                   | Stöds     |

>[!NOTE] 
Om mer än en åtgärd har definierats på samma blob, gäller livscykelhantering billigaste åtgärden blob. (t.ex. åtgärden `delete` är billigare än åtgärd `tierToArchive`. Åtgärden `tierToArchive` är billigare än åtgärd `tierToCool`.)

I förhandsversion baseras åtgärd körning villkor på ålder. Grundläggande blob använder senast ändrad att spåra ålder och blob-ögonblicksbilder använder ögonblicksbilden Skapandetid att spåra ålder.

| Åtgärdsstatus för körning | Villkorsvärdet | Beskrivning |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Heltalsvärde som anger ålder i dagar | Giltiga villkor för grundläggande blob-åtgärder |
| daysAfterCreationGreaterThan     | Heltalsvärde som anger ålder i dagar | Giltiga villkor för åtgärder för blob-ögonblicksbild | 

## <a name="examples"></a>Exempel
I följande exempel visar hur du hanterar vanliga scenarier med reglerna för livscykel.

### <a name="move-aging-data-to-a-cooler-tier"></a>Flytta föråldras data till en mer lågfrekvent nivå

I följande exempel visar hur du övergår blockblob-objekt med prefixet `container1/foo` eller `container2/bar`. Principen övergår blobar som inte har ändrats i över 30 dagar till lågfrekvent lagring och BLOB-objekt som inte har ändrats i 90 dagar som arkivnivån:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "agingRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

Vissa data förblir inaktiva i molnet och används sällan, eller kanske aldrig, när de har lagrats. Dessa data är bäst att arkiveras omedelbart när det matas in. Följande livscykelprincipen har konfigurerats för att arkivera data vid inmatning. Det här exemplet övergångar blockblobbar i storage-konto i behållaren `archivecontainer` direkt till en arkivnivån. Omedelbar övergången görs genom att fungera för blobbar 0 dagar efter tid för senaste ändring:

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "archiveRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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

Vissa data förväntas upphör att gälla dagar eller månader efter att minska kostnaderna och följa statliga regleringar har skapats. En princip för livscykelhantering kan ställas in att upphöra att gälla data genom att ta bort baserat på data ålder. I följande exempel visas en princip som tar bort alla blockblob-objekt (med inget prefix har angetts) som är äldre än 365 dagar.

```json
{
  "version": "0.5",
  "rules": [ 
    {
      "name": "expirationRule", 
      "type": "Lifecycle", 
      "definition": 
        {
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
      "definition": 
        {
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

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att återställa data efter oavsiktlig borttagning:

- [Mjuk borttagning för Azure Storage-blobbar ](../blobs/storage-blob-soft-delete.md)
