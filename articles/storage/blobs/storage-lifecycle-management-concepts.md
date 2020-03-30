---
title: Hantera livscykeln för Azure Storage
description: Lär dig hur du skapar livscykelprincipregler för att överföra åldersdata från nivåer för frekvent till kyla och arkiv.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598314"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Hantera Azure Blob Storage-livscykeln

Datauppsättningar har unika livscykler. Tidigt i livscykeln får personer ofta åtkomst till vissa data. Men behovet av tillgång sjunker drastiskt när data åldras. Vissa data förblir inaktiva i molnet och nås sällan när de har lagrats. Vissa data upphör att gälla dagar eller månader efter skapandet, medan andra datauppsättningar läss aktivt och ändras under hela deras livstid. Livscykelhantering för Azure Blob-lagring erbjuder en omfattande, regelbaserad princip för GPv2- och Blob-lagringskonton. Använd principen för att överföra dina data till lämpliga åtkomstnivåer eller upphör att gälla i slutet av datans livscykel.

Med livscykelhanteringsprincipen kan du:

- Övergångsblobar till en svalare lagringsnivå (varm till sval, varm att arkivera eller cool för att arkivera) för att optimera för prestanda och kostnad
- Ta bort blobbar i slutet av deras livscykel
- Definiera regler som ska köras en gång per dag på lagringskontonivå
- Tillämpa regler på behållare eller en delmängd blobbar (med prefix som filter)

Tänk dig ett scenario där data får frekvent åtkomst under de tidiga stadierna av livscykeln, men bara ibland efter två veckor. Efter den första månaden nås datauppsättningen sällan. I det här fallet är varm lagring bäst under de tidiga stadierna. Cool lagring är mest lämplig för tillfällig åtkomst. Arkivlagring är det bästa nivåalternativet efter dataåldrarna över en månad. Genom att justera lagringsnivåer med avseende på dataåldern kan du utforma de billigaste lagringsalternativen för dina behov. För att uppnå den här övergången är livscykelhanteringsprincipregler tillgängliga för att flytta åldersfördelningsdata till kallare nivåer.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Support för lagringskonto

Livscykelhanteringsprincipen är tillgänglig med GPv2-konton (General Purpose v2), Blob storage-konton och Premium Block Blob-lagringskonton. I Azure-portalen kan du uppgradera ett befintligt GPv1-konto (General Purpose) till ett GPv2-konto. Mer information om lagringskonton finns i [Översikt över Azure storage-konto](../common/storage-account-overview.md).  

## <a name="pricing"></a>Prissättning

Livscykelhanteringsfunktionen är kostnadsfri. Kunder debiteras den vanliga driftkostnaden för [API-anrop](https://docs.microsoft.com/rest/api/storageservices/list-blobs) för lista blob och [ange API-anrop på Blob-nivå.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) Borttagningsåtgärden är kostnadsfri. Mer information om prissättning finns i [Blockera Blob-priser](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Regional tillgänglighet

Livscykelhanteringsfunktionen är tillgänglig i alla Azure-regioner.

## <a name="add-or-remove-a-policy"></a>Lägga till eller ta bort en princip

Du kan lägga till, redigera eller ta bort en princip med någon av följande metoder:

* [Azure-portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API:er](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

En policy kan läsas eller skrivas i sin helhet. Partiella uppdateringar stöds inte. 

> [!NOTE]
> Om du aktiverar brandväggsregler för ditt lagringskonto kan livscykelhanteringsbegäranden blockeras. Du kan avblockera dessa begäranden genom att tillhandahålla undantag för betrodda Microsoft-tjänster. Mer information finns i avsnittet Undantag i [Konfigurera brandväggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Den här artikeln visar hur du hanterar principen med hjälp av portal- och PowerShell-metoderna.  

# <a name="portal"></a>[Portal](#tab/azure-portal)

Det finns två sätt att lägga till en princip via Azure-portalen. 

* [Vyn Azure Portal-lista](#azure-portal-list-view)
* [Vyn Azure Portal-kod](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Vyn Azure Portal-lista

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter och välj ditt lagringskonto i Azure-portalen. 

3. Under **Blob-tjänst**väljer du **Livscykelhantering** för att visa eller ändra dina regler.

4. Välj fliken **Lista vy.**

5. Välj **Lägg till regel** och fyll sedan i formulärfälten För **åtgärdsuppsättning.** I följande exempel flyttas blobbar till kyllagring om de inte har ändrats på 30 dagar.

   ![Åtgärdsuppsättningssida för livscykelhantering i Azure-portalen](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Välj **Filteruppsättning** om du vill lägga till ett valfritt filter. Välj sedan **Bläddra** för att ange en behållare och mapp som du vill filtrera efter.

   ![Ange sida för livscykelhanteringsfilter i Azure-portalen](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Välj **Granska + lägg** till om du vill granska principinställningarna.

9. Välj **Lägg till** om du vill lägga till den nya principen.

#### <a name="azure-portal-code-view"></a>Vyn Azure Portal-kod
1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Sök efter och välj ditt lagringskonto i Azure-portalen.

3. Under **Blob-tjänst**väljer du **Livscykelhantering** för att visa eller ändra din princip.

4. Följande JSON är ett exempel på en princip som kan klistras in på fliken **Kodvy.**

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

5. Välj **Spara**.

6. Mer information om det här JSON-exemplet finns i avsnitten [Policy](#policy) [and Rules.](#rules)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Följande PowerShell-skript kan användas för att lägga till en princip i ditt lagringskonto. Variabeln `$rgname` måste initieras med resursgruppsnamnet. Variabeln `$accountName` måste initieras med ditt lagringskontonamn.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

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

# <a name="template"></a>[Mall](#tab/template)

Du kan definiera livscykelhantering med hjälp av Azure Resource Manager-mallar. Här är en exempelmall för att distribuera ett RA-GRS GPv2-lagringskonto med en livscykelhanteringsprincip.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

---

## <a name="policy"></a>Princip

En livscykelhanteringsprincip är en samling regler i ett JSON-dokument:

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

En policy är en samling regler:

| Parameternamn | Parametertyp | Anteckningar |
|----------------|----------------|-------|
| `rules`        | En matris med regelobjekt | Minst en regel krävs i en princip. Du kan definiera upp till 100 regler i en princip.|

Varje regel i principen har flera parametrar:

| Parameternamn | Parametertyp | Anteckningar | Krävs |
|----------------|----------------|-------|----------|
| `name`         | String |Ett regelnamn kan innehålla upp till 256 alfanumeriska tecken. Regelnamnet är skiftlägeskänsligt.  Det måste vara unikt inom en politik. | True |
| `enabled`      | Boolean | En valfri boolean som tillåter att en regel inaktiveras tillfälligt. Standardvärdet är sant om det inte är inställt. | False | 
| `type`         | Ett uppräkningsvärde | Den aktuella `Lifecycle`giltiga typen är . | True |
| `definition`   | Ett objekt som definierar livscykelregeln | Varje definition består av en filteruppsättning och en åtgärdsuppsättning. | True |

## <a name="rules"></a>Regler

Varje regeldefinition innehåller en filteruppsättning och en åtgärdsuppsättning. [Filteruppsättningen begränsar](#rule-filters) regelåtgärder till en viss uppsättning objekt i en behållare eller objektnamn. [Åtgärdsuppsättningen](#rule-actions) tillämpar nivå- eller borttagningsåtgärder på den filtrerade objektuppsättningen.

### <a name="sample-rule"></a>Exempelregel

Följande exempelregel filtrerar kontot för att köra `container1` åtgärderna på `foo`objekt som finns inuti och börja med .  

>[!NOTE]
>Livscykelhantering stöder endast blockblolobtyp.  

- Tier blob för att kyla nivå 30 dagar efter senaste ändringen
- Nivåblolob till arkivnivå 90 dagar efter senaste ändringen
- Ta bort blob 2 555 dagar (sju år) efter senaste ändringen
- Ta bort blob-ögonblicksbilder 90 dagar efter att ögonblicksbilder har skapats

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

### <a name="rule-filters"></a>Regelfilter

Filtrerar begränsa regelåtgärder till en delmängd blobbar i lagringskontot. Om mer än ett filter `AND` har definierats körs ett logiskt på alla filter.

Filtren är:

| Filternamn | Filtertyp | Anteckningar | Krävs |
|-------------|-------------|-------|-------------|
| blobTypes   | En matris med fördefinierade uppräkningsvärden. | Den aktuella `blockBlob`versionen stöder . | Ja |
| prefixMatch | En matris med strängar för prefix som ska matchas. Varje regel kan definiera upp till 10 prefix. En prefixsträng måste börja med ett behållarnamn. Om du till exempel vill matcha alla `https://myaccount.blob.core.windows.net/container1/foo/...` blobbar under för `container1/foo`en regel är prefixmatchningen . | Om du inte definierar prefixMatch gäller regeln för alla blobbar i lagringskontot.  | Inga |

### <a name="rule-actions"></a>Regelåtgärder

Åtgärder tillämpas på filtrerade blobbar när körningsvillkoret uppfylls.

Livscykelhantering stöder nivåindelning och borttagning av blobbar och borttagning av blob-ögonblicksbilder. Definiera minst en åtgärd för varje regel på blobbar eller blob-ögonblicksbilder.

| Åtgärd        | Basblob                                   | Ögonblicksbild      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Stöd blobbar för närvarande på hot tier         | Stöds inte |
| nivåTillKiv | Stöd blobbar för närvarande på hot eller cool nivå | Stöds inte |
| delete        | Stöds                                   | Stöds     |

>[!NOTE]
>Om du definierar mer än en åtgärd på samma blob tillämpar livscykelhantering den billigaste åtgärden för bloben. Åtgärden `delete` är till exempel `tierToArchive`billigare än åtgärd . Åtgärder `tierToArchive` är billigare `tierToCool`än åtgärder .

Körningsförhållandena baseras på ålder. Basblobar använder den senaste ändrade tiden för att spåra ålder och blob-ögonblicksbilder använder tiden för att skapa ögonblicksbilder för att spåra ålder.

| Villkor för åtgärdskörning             | Villkorsvärde                          | Beskrivning                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| dagarEfterModificationGreaterT | Heltalsvärde som anger ålder i dagar | Villkoret för basblolobåtgärder     |
| dagarEfterCreationGreaterThan     | Heltalsvärde som anger ålder i dagar | Villkoret för blob-ögonblicksbildåtgärder |

## <a name="examples"></a>Exempel

Följande exempel visar hur du hanterar vanliga scenarier med livscykelprincipregler.

### <a name="move-aging-data-to-a-cooler-tier"></a>Flytta åldersdata till en svalare nivå

I det här exemplet visas hur du `container1/foo` övergår blockblobar som är förutbsatta med eller `container2/bar`. Principövergångarna blobbar som inte har ändrats på över 30 dagar för att kyla lagring och blobbar har inte ändrats på 90 dagar till arkivnivån:

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

### <a name="archive-data-after-ingest"></a>Arkivera data efter intag

Vissa data förblir inaktiva i molnet och används sällan, om någonsin, när de har lagrats. Följande livscykelprincip är konfigurerad för att arkivera data kort efter att den har förtärs. I det här exemplet flyttas blockblobar `archivecontainer` i lagringskontot i behållaren till en arkivnivå. Övergången sker genom att agera på blobbar 0 dagar efter senaste ändrade tiden:

> [!NOTE] 
> Vi rekommenderar att du laddar upp dina blobbar direkt arkivnivån för att bli mer effektiv. Du kan använda x-ms-acess-tier-huvudet för [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) eller [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) med REST version 2018-11-09 och nyare eller våra senaste blob storage-klientbibliotek. 

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

### <a name="expire-data-based-on-age"></a>Förfalla data baserat på ålder

Vissa data förväntas upphöra att gälla dagar eller månader efter skapandet. Du kan konfigurera en livscykelhanteringsprincip så att data upphör att gälla genom borttagning baserat på dataålder. I följande exempel visas en princip som tar bort alla blockblobar som är äldre än 365 dagar.

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

För data som ändras och nås regelbundet under hela dess livstid används ögonblicksbilder ofta för att spåra äldre versioner av data. Du kan skapa en princip som tar bort gamla ögonblicksbilder baserat på ögonblicksbilder ålder. Ögonblicksbildåldern bestäms genom att utvärdera tiden för att skapa ögonblicksbilder. Den här principregeln tar bort `activedata` blockblobbögonblicksbilder i behållare som är 90 dagar eller äldre efter att ögonblicksbilden har skapats.

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

**Jag skapade en ny princip, varför körs inte åtgärderna omedelbart?**  
Plattformen kör livscykelpolicyn en gång om dagen. När du har konfigurerat en princip kan det ta upp till 24 timmar innan vissa åtgärder körs för första gången.  

**Hur lång tid tar det innan åtgärderna körs om jag uppdaterar en befintlig princip?**  
Den uppdaterade principen tar upp till 24 timmar att träda i kraft. När principen är i kraft kan det ta upp till 24 timmar innan åtgärderna körs. Därför kan det ta upp till 48 timmar innan principåtgärderna är klara.   

**Jag rehydrerade manuellt en arkiverad blob, hur förhindrar jag att den flyttas tillbaka till arkivnivån tillfälligt?**  
När en blob flyttas från en åtkomstnivå till en annan ändras inte dess senaste ändringstid. Om du manuellt rehydrera en arkiverad blob till hot tier, skulle den flyttas tillbaka till arkivnivå av livscykelhanteringsmotorn. Inaktivera regeln som påverkar den här blobben tillfälligt för att förhindra att den arkiveras igen. Aktivera regeln igen när blobben kan flyttas tillbaka till arkivnivån på ett säkert sätt. Du kan också kopiera blobben till en annan plats om den behöver hålla sig på den varma eller svala nivån permanent.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du återställer data efter oavsiktlig borttagning:

- [Mjuk borttagning för Azure Storage-blobar](../blobs/storage-blob-soft-delete.md)
