---
title: Hantera Azure Storage-livscykeln
description: Lär dig skapa livscykel principregler till övergången againg data från frekvent till lågfrekvent och arkivera nivåer.
services: storage
author: yzheng-msft
manager: jwillis
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 04/30/2018
ms.author: yzheng
ms.openlocfilehash: 9721935f005bbd9a5dc261fe801ecc14744b004f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752800"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Hantera Azure Blob Storage livscykeln (förhandsgranskning)

Unik livscykler har datauppsättningar. Vissa data används ofta tidigt i livscykeln, men behovet av att åtkomst släpper drastiskt som dessa data blir äldre. Vissa data förblir inaktiva i molnet och används sällan lagras en gång. Vissa data ut dagar eller månader efter skapa medan andra datauppsättningar läses aktivt och ändras under hela sin livslängd. Livscykelhantering för Azure Blob Storage (förhandsversion) ger en omfattande och regelbaserad princip som du kan använda för att överföra data till den bästa åtkomstnivån och -data i slutet av dess livscykel.

Princip för hantering av livscykel hjälper dig att:

- Övergång BLOB till en kyligare lagringsnivå (frekvent kall frekvent till arkivet, eller kall till arkivet) att optimera prestanda och kostnad
- Ta bort blobbar i slutet av deras livscykler
- Definiera regler som ska köras en gång om dagen på kontonivå lagring (stöds både GPv2 och Blob storage-konton)
- Tillämpa regler på behållare eller en delmängd av blobbar (med prefix som filter)

Överväg att en uppsättning data som används ofta under tidigt i livscykeln, krävs endast ibland efter två veckor som används sällan efter en månad och framåt. I det här scenariot frekvent är bäst i början, lågfrekvent är mest lämplig för tillfällig åtkomst, och arkivlagring är det bästa alternativet för nivån efter dessa data blir äldre över en månad. Genom att justera lagringsnivåer med avseende på åldern på data kan utforma du billigaste lagringsalternativ för dina behov. För att uppnå den här övergången är principer för hantering av livscykel tillgängliga att flytta åldern data till kyligare nivåer.

## <a name="storage-account-support"></a>Stöd för Storage-konton

Princip för hantering av livscykel är tillgänglig med både generella v2 (GPv2) konto och Blob Storage-konto. Du kan konvertera ett befintligt konto generella (GPv1) till ett GPv2 konto via en enkel process för en enda klickning i Azure-portalen. Mer information finns i [Alternativ för Azure Storage-konton](../common/storage-account-options.md).  

## <a name="pricing"></a>Prissättning 

Funktionen för hantering av livscykel är kostnadsfritt i förhandsgranskningen. Kunder som debiteras regelbunden drift kostnaden för den [lista Blobbar](https://docs.microsoft.com/rest/api/storageservices/list-blobs) och [ange Blob-nivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-anrop. Se [Blockblob priser](https://azure.microsoft.com/pricing/details/storage/blobs/) för mer information om priser.

## <a name="register-for-preview"></a>Registrera dig för förhandsgranskning 
Om du vill registrera i förhandsversion, behöver du skicka en begäran om att registrera den här funktionen till din prenumeration. När din begäran har godkänts (inom några dagar), har alla befintliga och nya GPv2 eller Blob Storage-konto i västra USA 2 och West centrala oss funktionen aktiverad. Under förhandsgranskningen gör stöds endast blockblob. Precis som med de flesta förhandsgranskningar ska funktionen inte användas för produktionsarbetsbelastningar tills GA.

Kör följande PowerShell eller CLI-kommandon för att skicka en begäran.

### <a name="powershell"></a>PowerShell

Att skicka en begäran:

```powershell
Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Du kan kontrollera godkännandestatusen registrering med följande kommando:
```powershell
Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Om funktionen har godkänts och registrerats korrekt, får tillståndet ”registrerad”.

### <a name="cli-20"></a>CLI 2.0

Att skicka en begäran: 
```cli
az feature register –-namespace Microsoft.Storage –-name DLM
```
Du kan kontrollera godkännandestatusen registrering med följande kommando:
```cli
-az feature show –-namespace Microsoft.Storage –-name DLM
```
Om funktionen har godkänts och registrerats korrekt, får tillståndet ”registrerad”. 


## <a name="add-or-remove-policies"></a>Lägg till eller ta bort principer 

Du kan lägga till, redigera eller ta bort en princip med hjälp av Azure portal [PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview), REST API: er eller klientverktyg på följande språk: [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [ Node.js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby]( https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

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
Om du aktiverar brandväggsregler för ditt lagringskonto, blockeras livscykeln för hantering av begäranden. Du kan låsa upp den genom att tillhandahålla undantag. Mer information finns i avsnittet undantag vid [konfigurera brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policies"></a>Principer

En princip för hantering av livscykel är en uppsättning regler i en JSON-dokument:

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
| version        | En sträng som är uttryckt i `x.x` | Förhandsgranska versionsnumret är 0,5 |
| regler          | En matris med regeln objekt | Det krävs minst en regel i varje princip. Du kan ange upp till 4 regler per princip under förhandsgranskning. |

Parametrar som krävs i en regel är:

| Parameternamn | Parametertyp | Anteckningar |
|----------------|----------------|-------|
| Namn           | Sträng | Ett namn för regeln kan innehålla vilken kombination av alfanumeriska tecken. Regelnamnet är skiftlägeskänslig. Det måste vara unika inom en princip. |
| typ           | Ett uppräkningsvärde | Det giltiga värdet för förhandsversionen är `Lifecycle` |
| definition     | Ett objekt som definierar livscykel regel | Varje definition består med ett filter och en åtgärd uppsättningen. |

## <a name="rules"></a>Regler

Varje regeldefinition innehåller ett filter och en åtgärd uppsättningen. Följande Exempelregel ändrar nivån för grundläggande blockblobbar med prefixet `foo`. Dessa regler har definierats som i principen:

- Nivån blob att kyla ner lagring 30 dagar efter senast ändrades
- Nivå-blob till arkivlagring 90 dagar efter senast ändrades
- Ta bort blobben 2,555 dagar (7 år) efter senaste ändring
- Ta bort ögonblicksbilder för blobben 90 dagar efter att ögonblicksbilden har skapats

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
          "prefixMatch": [ "foo" ]
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

Filter begränsa regelåtgärder till en delmängd av blobbar i lagringskontot. Om flera filter definieras en logisk `AND` utförs på alla filter.

Under förhandsgranskning är giltig filtren:

| Filternamn | Filtertyp | Anteckningar | Krävs |
|-------------|-------------|-------|-------------|
| blobTypes   | En matris med fördefinierade enum-värden. | För förhandsversionen endast `blockBlob` stöds. | Ja |
| prefixMatch | En matris med strängar för prefix som ska vara matchar. | Om det inte är definierad gäller den här regeln för alla blobbar i kontot. | Nej |

### <a name="rule-actions"></a>Regelåtgärder

Åtgärder som tillämpas på de filtrerade blobbarna när körningen är uppfyllt.

I preview stöder livscykelhantering skiktning och borttagning av blob och borttagning av blob-ögonblicksbilder. Varje regel måste ha minst en åtgärd som definierats på blobbar eller blob ögonblicksbilder.

| Åtgärd        | Grundläggande Blob                                   | Ögonblicksbild      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Stöd för antal BLOB-informationsblock för närvarande på Hot nivå         | Stöds inte |
| tierToArchive | Stöd för antal BLOB-informationsblock för närvarande på aktiv eller kall nivå | Stöds inte |
| delete        | Stöds                                   | Stöds     |

>[!NOTE] 
Om mer än en åtgärd har definierats för samma blob, gäller åtgärden billigaste blob livscykelhantering. (t.ex. åtgärden `delete` billigare än åtgärden `tierToArchive`. Åtgärden `tierToArchive` billigare än åtgärden `tierToCool`.)

I Förhandsgranska baserat villkor som åtgärden körning på ålder. Grundläggande blob använder senast ändrad att spåra ålder och blob ögonblicksbilder använder ögonblicksbilden skapelsetid att spåra ålder.

| Åtgärdsstatus för körning | Villkorsvärdet | Beskrivning |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Heltalsvärde som anger ålder i dagar | Giltiga villkor för grundläggande blob-åtgärder |
| daysAfterCreationGreaterThan     | Heltalsvärde som anger ålder i dagar | Giltiga villkor för blob ögonblicksbild åtgärder | 

## <a name="examples"></a>Exempel
Följande exempel visar hur vanliga scenarier med reglerna för livscykel.

### <a name="move-aging-data-to-a-cooler-tier"></a>Flytta åldern data till ett svalare skikt

I följande exempel visar hur du övergår blockblobbar med prefixet `foo` eller `bar`. Principen övergångar blob som inte har ändrats efter 30 dagar till lågfrekvent och blobbar som inte har ändrats i 90 dagar Arkiv-nivån:

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
            "prefixMatch": [ "foo", "bar" ]
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

### <a name="archive-data-at-ingest"></a>Arkivera data vid infogande 

Vissa data förblir inaktiva i molnet och används sällan, eller kanske aldrig, när de har lagrats. Dessa data är bäst att arkiveras omedelbart när den är inhämtas. Följande livscykel principen är konfigurerad för att arkivera data på infognings. Det här exemplet övergångar blockblobbar i storage-konto med prefixet `archive` direkt till en nivå i arkivet. Omedelbar övergången utförs genom att fungera på blobbar 0 dagar efter senast ändrad:

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
            "prefixMatch": [ "archive" ]
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

### <a name="expire-data-based-on-age"></a>Ut data baserat på ålder

Vissa data förväntas gälla dagar eller månader efter att minska kostnaderna och följa statliga regelverk har skapats. En princip för hantering av livscykeln kan ställas in att gälla data genom att ta bort baserat på data ålder. I följande exempel visas en princip som tar bort alla blockblobbar (med inget prefix har angetts) som är äldre än 365 dagar.

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

För data som ändras och komma åt regelbundet under dess livslängd används ögonblicksbilder ofta för att spåra äldre versioner av data. Du kan skapa en princip som tar bort gamla ögonblicksbilderna baserat på ögonblicksbild ålder. Ögonblicksbild ålder bestäms genom att utvärdera tiden för skapandet av ögonblicksbild. Den här principregeln borttagningar blockera blob-ögonblicksbilder med prefixet `activeData` som är 90 dagar eller senare efter att ögonblicksbilden har skapats.

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
            "prefixMatch": [ "activeData" ]
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

Lär dig hur du återställer data efter oavsiktlig borttagning:

- [Radera mjuka för Azure Storage BLOB ](../blobs/storage-blob-soft-delete.md)
