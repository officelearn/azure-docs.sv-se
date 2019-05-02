---
title: Hantera Azure Storage-livscykel
description: Lär dig mer om att skapa livscykel principregler övergången föråldras data från frekvent till lågfrekvent nivå eller arkivnivå.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: conceptual
ms.date: 4/29/2019
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: f1fdd1b239301a5340716e1d5d098487afe27f9f
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938573"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Hantera Azure Blob storage livscykel

Datauppsättningar har unika livscykler. Tidigt i livscykeln, personer åtkomst till vissa data ofta. Men behov av åtkomst sjunker drastiskt som data registrerar dig. Vissa data förblir inaktiva i molnet och används sällan lagras en gång. Vissa data upphör dagar eller månader när du har skapat, medan andra datauppsättningar läses aktivt och ändras under hela sin livslängd. Livscykelhantering för Azure Blob storage erbjuder en omfattande, regelbaserad princip för GPv2- och Blob storage-konton. Använda för att överföra data till lämpliga åtkomstnivåer eller upphör att gälla i slutet av den data livscykel.

Principen för livscykelhantering kan du:

- Överföra BLOB-och en mer lågfrekvent lagringsnivå (frekvent till lågfrekvent, frekvent för att arkivera eller lågfrekvent för att arkivera) att optimera för prestanda och kostnader
- Ta bort blobar i slutet av deras livscykler
- Definiera regler som ska köras en gång per dag på nivån för storage-konto
- Tillämpa regler på behållare eller en delmängd av BLOB-objekt (med prefix som filter)

Föreställ dig ett scenario där en datauppsättning hämtar frekvent åtkomst i början av livscykel, men sedan bara ibland efter två veckor. Utöver den första månaden får datauppsättningen i sällan. I det här scenariot är lagring för frekvent / bäst under de tidiga stadierna. Lågfrekvent lagring är mest lämplig för tillfällig åtkomst och archive storage är det bästa alternativet nivå när du registrerar dig data över en månad. Du kan utforma de billigaste lagringsalternativ för dina behov genom att justera lagringsnivåer i jämfört med åldern på data. Livscykeln för hantering av principregler är tillgängliga att flytta föråldras data till mer lågfrekvent nivå för att uppnå den här ändringen.

## <a name="storage-account-support"></a>Stöd för Storage-konton

Principen för livscykelhantering är tillgängligt med både generell användning v2 (GPv2)-konton och Blob storage-konton. Du kan uppgradera ett befintligt konto för generell användning (GPv1) till ett GPv2-konto via en enkel process med ett klick i Azure-portalen. Mer information om lagringskonton finns i [kontoöversikten för Azure Storage](../common/storage-account-overview.md).  

## <a name="pricing"></a>Prissättning 

Funktionen för hantering av livscykeln är kostnadsfri. Kunderna debiteras den vanliga åtgärd kostnaden för den [lista Blobar](https://docs.microsoft.com/rest/api/storageservices/list-blobs) och [ange Blobnivå](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-anrop. Borttagningen är kostnadsfri. Mer information om priser finns i [blockblobpriserna](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Regional tillgänglighet 
Livscykeln för hantering av funktionen är tillgänglig i alla offentliga Azure-regioner. 


## <a name="add-or-remove-a-policy"></a>Lägg till eller ta bort en princip 

Du kan lägga till, redigera eller ta bort en princip med hjälp av Azure-portalen [Azure PowerShell](https://github.com/Azure/azure-powershell/releases), Azure CLI, [REST API: er](https://docs.microsoft.com/rest/api/storagerp/managementpolicies), eller ett klientverktyg. Den här artikeln visar hur du hanterar princip med hjälp av portalen och PowerShell-metoder.  

> [!NOTE]
> Om du aktiverar brandväggsregler för ditt lagringskonto, blockeras livscykeln för hantering av begäranden. Du kan låsa upp dessa begäranden genom att ange undantag. Nödvändiga förbikopplingen är: `Logging,  Metrics,  AzureServices`. Mer information finns i avsnittet undantag i [konfigurera brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Välj **alla resurser** och välj sedan ditt lagringskonto.

3. Under **Blobtjänsten**väljer **livscykelhantering** visa eller ändra din princip.

### <a name="powershell"></a>PowerShell

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery 

#Create a new action object

$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd 

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy 
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1

```
## <a name="arm-template-with-lifecycle-management-policy"></a>ARM-mall med principen för livscykelhantering

Du kan definiera och distribuera livscykelhantering som en del av distributionen av Azure-lösning med hjälp av ARM-mallar. Följ är en exempelmall för att distribuera ett RA-GRS GPv2-konto med en princip för livscykelhantering. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

## <a name="policy"></a>Princip

En princip för livscykelhantering är en samling regler i ett JSON-dokument:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
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


En princip är en samling regler:

| Parameternamn | Parametertyp | Anteckningar |
|----------------|----------------|-------|
| regler          | En matris med regelobjekt | Du måste minst en regel i en princip. Du kan ange upp till 100 regler i en princip.|

Varje regel i principen har flera parametrar:

| Parameternamn | Parametertyp | Anteckningar | Krävs |
|----------------|----------------|-------|----------|
| namn           | String |Ett regelnamn kan innehålla upp till 256 alfanumeriska tecken. Regelnamnet är skiftlägeskänsligt.  Det måste vara unika inom en princip. | True |
| aktiverad | Boolean | Ett valfritt booleskt värde att tillåta en regel för att vara tillfälligt inaktiverats. Standardvärdet är SANT om det inte har angetts. | False | 
| typ           | Ett uppräkningsvärde | Den aktuella giltiga typen är `Lifecycle`. | True |
| definition     | Ett objekt som definierar regeln för livscykel | Varje definition består av ett filter och en åtgärd. | True |

## <a name="rules"></a>Regler

Varje Regeldefinitionen innehåller en filteruppsättning och en uppsättning åtgärder. Den [filtrera set](#rule-filters) begränsar regelåtgärder till en viss uppsättning objekt i en behållare och objekt som namn. Den [åtgärd set](#rule-actions) gäller nivån eller ta bort åtgärder för att en filtrerad uppsättning objekt.

### <a name="sample-rule"></a>Exempelregel
Följande Exempelregel filtrerar konto för att köra åtgärder på objekt som finns inuti `container1` **AND** börjar med `foo`.  

- Nivå-blob till lågfrekvent nivå 30 dagar efter senaste ändring
- Nivå-blob att arkivera nivån 90 dagar efter senaste ändringen
- Ta bort blob 2,555 dagar (sju år) efter senaste ändring
- Ta bort blobögonblicksbilderna 90 dagar efter ögonblicksbilder skapas

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
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

Giltigt filtren är:

| Filternamn | Filtertyp | Anteckningar | Obligatorisk |
|-------------|-------------|-------|-------------|
| blobTypes   | En matris med fördefinierade enum-värden. | Den aktuella versionen stöder `blockBlob`. | Ja |
| prefixMatch | En matris med strängar för prefix som ska matcha. Varje regel kan definiera upp till 10 prefix. En sträng med prefixet måste börja med ett behållarnamn. Exempel: Om du vill matcha alla blobar under ”https://myaccount.blob.core.windows.net/container1/foo/..”. för en regel i prefixMatch är `container1/foo`. | Om du inte definierar prefixMatch gäller regeln för alla blobbar i lagringskontot.  | Nej |

### <a name="rule-actions"></a>Regelåtgärder

Åtgärder som tillämpas på filtrerade blobbar när kör villkoret är uppfyllt.

Livscykelhantering stöder blobnivåindelning och borttagning av blobar och borttagning av blob-ögonblicksbilder. Ange minst en åtgärd för varje regel för blobar eller blobögonblicksbilder.

| Åtgärd        | Grundläggande Blob                                   | Ögonblicksbild      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Stöd för blobar för närvarande på frekvent nivå         | Stöds inte |
| tierToArchive | Stöd för blobar för närvarande på frekvent eller lågfrekvent nivå | Stöds inte |
| delete        | Stöds                                   | Stöds     |

>[!NOTE] 
>Om du definierar mer än en åtgärd på samma blob gäller livscykelhantering den billigaste åtgärden blob. Till exempel åtgärden `delete` är billigare än åtgärd `tierToArchive`. Åtgärden `tierToArchive` är billigare än åtgärd `tierToCool`.

Kör villkor baseras på ålder. Grundläggande blobbar använda det senaste ändringsdatum för att spåra ålder och blob-ögonblicksbilder används tiden för skapandet av ögonblicksbild för att spåra ålder.

| Åtgärden kör villkor | Villkorsvärdet | Beskrivning |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Heltalsvärde som anger ålder i dagar | Giltiga villkor för grundläggande blob-åtgärder |
| daysAfterCreationGreaterThan     | Heltalsvärde som anger ålder i dagar | Giltiga villkor för åtgärder för blob-ögonblicksbild | 

## <a name="examples"></a>Exempel
I följande exempel visar hur du hanterar vanliga scenarier med reglerna för livscykel.

### <a name="move-aging-data-to-a-cooler-tier"></a>Flytta föråldras data till en mer lågfrekvent nivå

Det här exemplet visar hur du övergår blockblob-objekt med prefixet `container1/foo` eller `container2/bar`. Principen övergår blobar som inte har ändrats i över 30 dagar till lågfrekvent lagring och BLOB-objekt som inte har ändrats i 90 dagar som arkivnivån:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
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

Vissa data förblir inaktiva i molnet och är data som sällan, kanske aldrig, en gång lagras. Följande livscykelprincipen har konfigurerats för att arkivera data när det matas in. Det här exemplet övergångar blockblobbar i storage-konto i behållaren `archivecontainer` i en arkivnivån. Övergången görs genom att fungera för blobbar 0 dagar efter tid för senaste ändring:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
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
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
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
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
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
## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR 
**Jag har skapat en ny princip, varför åtgärderna som inte körs direkt?**  
Plattformen körs policyn för onlinelivscykeln en gång om dagen. När du konfigurerar en princip, kan det ta upp till 24 timmar för vissa åtgärder (till exempel lagringsnivåer och borttagning) körs för första gången.  

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att återställa data efter oavsiktlig borttagning:

- [Mjuk borttagning för Azure Storage-blobbar](../blobs/storage-blob-soft-delete.md)
