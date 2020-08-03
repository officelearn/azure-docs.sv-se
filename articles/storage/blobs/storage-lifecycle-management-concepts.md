---
title: Hantera Azure Storage livs cykeln
description: Lär dig hur du skapar policy regler för livs cykeln för att överföra ålders data från frekvent till låg frekvent lagring och Arkiv lag ring.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 865263d22d6f92dec74ef2820e80481e1a308804
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494561"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Hantera Azure Blob Storage-livscykeln

Data uppsättningar har unika livscykler. Tidigt i livs cykeln får människor ofta åtkomst till vissa data. Men behovet av åtkomst sjunker drastiskt när data åldras. Vissa data förblir inaktiva i molnet och används sällan när de lagras. Vissa data upphör att gälla dagar eller månader efter att de har skapats, medan andra data uppsättningar har lästs och ändrats under hela sin livs längd. Azure Blob Storage livs cykel hantering ger en omfattande, regel-baserad princip för GPv2-och Blob Storage-konton. Använd principen för att överföra data till lämpliga åtkomst nivåer eller förfaller i slutet av data livs cykeln.

Med policyn för livs cykel hantering kan du:

- Över gångs blobbar till en låg frekvent lagrings nivå (frekvent till låg frekvent, frekvent till arkiv eller låg frekvent att arkivera) för att optimera prestanda och kostnad
- Ta bort blobbar i slutet av deras livscykler
- Definiera regler som ska köras en gång per dag på lagrings konto nivå
- Tillämpa regler på behållare eller en delmängd av blobbar (med hjälp av prefix för namn eller [BLOB-index](storage-manage-find-blobs.md) som filter)

Tänk dig ett scenario där data får frekvent åtkomst under de tidiga faserna i livs cykeln, men bara ibland efter två veckor. Utöver den första månaden kommer data uppsättningen sällan att användas. I det här scenariot är frekvent lagring bäst i de tidiga faserna. Låg frekvent lagring är lämplig för tillfällig åtkomst. Arkiv lag ring är det bästa alternativet på nivån efter att data har funnits under en månad. Genom att justera lagrings nivåer avseende ålder på data kan du utforma de billigaste lagrings alternativen för dina behov. För att uppnå den här över gången är policy regler för livs cykel hantering tillgängliga för att flytta ålders data till låg frekventa nivåer.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="availability-and-pricing"></a>Tillgänglighet och priser

Funktionen för livs cykel hantering är tillgänglig i alla Azure-regioner för Generell användning v2-konton (GPv2), Blob Storage-konton och Premium Block Blob Storage-konton. I Azure Portal kan du uppgradera ett befintligt Generell användning-konto (GPv1) till ett GPv2-konto. Mer information om lagrings konton finns i [Översikt över Azure Storage-konto](../common/storage-account-overview.md).  

Funktionen för livs cykel hantering är kostnads fri. Kunderna debiteras den vanliga drift kostnaden för den [angivna](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API-anropen på BLOB-nivå. Borttagnings åtgärden är kostnads fri. Mer information om priser finns i [Block-Blob-prissättning](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>Lägga till eller ta bort en princip

Du kan lägga till, redigera eller ta bort en princip med någon av följande metoder:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST API:er](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

En princip kan läsas eller skrivas fullständigt. Del uppdateringar stöds inte. 

> [!NOTE]
> Om du aktiverar brand Väggs regler för ditt lagrings konto kan begäran om livs cykel hantering blockeras. Du kan avblockera dessa förfrågningar genom att tillhandahålla undantag för betrodda Microsoft-tjänster. Mer information finns i avsnittet undantag i [Konfigurera brand väggar och virtuella nätverk](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Den här artikeln visar hur du hanterar principer med hjälp av Portal-och PowerShell-metoder.  

# <a name="portal"></a>[Portal](#tab/azure-portal)

Det finns två sätt att lägga till en princip via Azure Portal. 

* [Azure Portal listvy](#azure-portal-list-view)
* [Azure Portal kodvyn](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Azure Portal listvy

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I Azure Portal söker du efter och väljer ditt lagrings konto. 

3. Under **BLOB service**väljer du **livs cykel hantering** för att visa eller ändra dina regler.

4. Välj fliken **listvy** .

5. Välj **Lägg till regel** och fyll sedan i formulär fälten för **Åtgärds uppsättning** . I följande exempel flyttas blobbar till låg frekvent lagring om de inte har ändrats i 30 dagar.

   ![Sidan livs cykel hantering åtgärds uppsättning i Azure Portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Välj **filter uppsättning** för att lägga till ett valfritt filter. Välj sedan **Bläddra** för att ange en behållare och mapp som du vill filtrera.

   ![Sidan livs cykel hantering filter uppsättning i Azure Portal](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Välj **Granska + Lägg** till för att granska princip inställningarna.

9. Välj **Lägg** till för att lägga till den nya principen.

#### <a name="azure-portal-code-view"></a>Azure Portal kodvyn
1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I Azure Portal söker du efter och väljer ditt lagrings konto.

3. Under **BLOB service**väljer du **livs cykel hantering** för att visa eller ändra principen.

4. Följande JSON är ett exempel på en princip som kan klistras in på fliken **kodvy** .

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

6. Mer information om det här JSON-exemplet finns i avsnittet [principer](#policy) och [regler](#rules) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan använda följande PowerShell-skript för att lägga till en princip i ditt lagrings konto. `$rgname`Variabeln måste initieras med resurs gruppens namn. `$accountName`Variabeln måste initieras med ditt lagrings konto namn.

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

Du kan definiera livs cykel hantering genom att använda Azure Resource Manager mallar. Här är en exempel-mall för att distribuera ett RA-GRS GPv2 lagrings konto med en princip för livs cykel hantering.

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

## <a name="policy"></a>Policy

En princip för livs cykel hantering är en samling regler i ett JSON-dokument:

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

| Parameternamn | Parameter typ | Kommentarer |
|----------------|----------------|-------|
| `rules`        | En matris med regel objekt | Minst en regel krävs i en princip. Du kan definiera upp till 100 regler i en princip.|

Varje regel i principen har flera parametrar:

| Parameternamn | Parameter typ | Kommentarer | Obligatorisk |
|----------------|----------------|-------|----------|
| `name`         | Sträng |Ett regel namn kan innehålla upp till 256 alfanumeriska tecken. Regel namnet är Skift läges känsligt.  Det måste vara unikt inom en princip. | Sant |
| `enabled`      | Boolesk | En valfri boolesk för att tillåta att en regel är tillfälligt inaktive rad. Standardvärdet är true om det inte har angetts. | Falskt | 
| `type`         | Ett uppräknings värde | Den aktuella giltiga typen är `Lifecycle` . | Sant |
| `definition`   | Ett objekt som definierar livs cykel regeln | Varje definition består av en filter uppsättning och en åtgärds uppsättning. | Sant |

## <a name="rules"></a>Regler

Varje regel definition innehåller en filter uppsättning och en åtgärds uppsättning. [Filter uppsättningen](#rule-filters) begränsar regel åtgärder till en viss uppsättning objekt inom en behållare eller objekt namn. Den [angivna åtgärden](#rule-actions) tillämpar nivån eller tar bort åtgärder på den filtrerade uppsättningen objekt.

### <a name="sample-rule"></a>Exempel regel

Följande exempel regel filtrerar kontot för att köra åtgärder på objekt som finns i `container1` och börjar med `foo` .  

>[!NOTE]
>- Livs cykel hantering har endast stöd för Block Blob-typ.<br>
>- Livs cykel hantering påverkar inte system behållare som $logs och $web.

- Nivå-blob till låg frekvent nivå 30 dagar efter senaste ändring
- Nivå-blob till Arkiv lag ring 90 dagar efter senaste ändring
- Ta bort BLOB 2 555 dagar (sju år) efter senaste ändring
- Ta bort BLOB-ögonblicksbilder 90 dagar efter att ögonblicks bilden har skapats

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

### <a name="rule-filters"></a>Regel filter

Filtrerar begränsnings regel åtgärder till en delmängd av blobbar i lagrings kontot. Om fler än ett filter definieras körs ett logiskt `AND` i alla filter.

Filtren är:

| Filternamn | Filtertyp | Kommentarer | Krävs |
|-------------|-------------|-------|-------------|
| blobTypes   | En matris med fördefinierade uppräknings värden. | Den aktuella versionen stöder `blockBlob` . | Yes |
| prefixMatch | En matris med strängar för prefix som ska matchas. Varje regel kan definiera upp till tio prefix. En prefixlängd måste börja med ett behållar namn. Om du till exempel vill matcha alla blobbar under `https://myaccount.blob.core.windows.net/container1/foo/...` för en regel är prefixMatch `container1/foo` . | Om du inte definierar prefixMatch gäller regeln för alla blobbar i lagrings kontot.  | No |
| blobIndexMatch | En matris med ordboks värden som består av BLOB index tag gen nyckel och värde villkor som ska matchas. Varje regel kan definiera upp till 10 tagg villkor för BLOB-index. Om du till exempel vill matcha alla blobbar med `Project = Contoso` under `https://myaccount.blob.core.windows.net/` för en regel är blobIndexMatch `{"name": "Project","op": "==","value": "Contoso"}` . | Om du inte definierar blobIndexMatch gäller regeln för alla blobbar i lagrings kontot. | No |

> [!NOTE]
> BLOB-indexet finns i en offentlig för hands version och är tillgängligt i regionerna **Kanada**, **östra**, **centrala Frankrike**och **södra Frankrike** . Mer information om den här funktionen tillsammans med kända problem och begränsningar finns i [Hantera och hitta data på Azure Blob Storage med BLOB index (för hands version)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Regel åtgärder

Åtgärder tillämpas på filtrerade blobbar när körnings villkoret är uppfyllt.

Livs cykel hantering stöder skiktning och borttagning av blobbar och borttagning av BLOB-ögonblicksbilder. Definiera minst en åtgärd för varje regel på blobbar eller BLOB-ögonblicksbilder.

| Action        | Bas-BLOB                                   | Ögonblicksbild      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Stöd för blobbar på frekvent nivå         | Stöds inte |
| tierToArchive | Stöd för blobbar på frekvent eller låg frekvent nivå | Stöds inte |
| ta bort        | Stöds                                   | Stöds     |

>[!NOTE]
>Om du definierar mer än en åtgärd på samma BLOB, tillämpar livs cykel hanteringen den minst dyra åtgärden på blobben. Till exempel är åtgärden `delete` billigare än åtgärd `tierToArchive` . Åtgärden `tierToArchive` är billigare än åtgärd `tierToCool` .

Körnings villkoren baseras på ålder. Bas-blobbar använder senaste ändrings tid för att spåra ålder och blob-ögonblicksbilder använder ögonblicks bilds skapande tiden för att spåra ålder.

| Åtgärds körnings villkor             | Villkors värde                          | Description                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Heltals värde som anger ålder i dagar | Villkoret för bas-BLOB-åtgärder     |
| daysAfterCreationGreaterThan     | Heltals värde som anger ålder i dagar | Villkoret för BLOB Snapshot-åtgärder |

## <a name="examples"></a>Exempel

Följande exempel visar hur du kan adressera vanliga scenarier med policy regler för livs cykeln.

### <a name="move-aging-data-to-a-cooler-tier"></a>Flytta ålders data till en kylare-nivå

I det här exemplet visas hur du översätter block-blobar som föregås av `container1/foo` eller `container2/bar` . Principen över blobar som inte har ändrats på över 30 dagar till låg frekvent lagring, och blobar som inte har ändrats på 90 dagar till Arkiv nivån:

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

### <a name="archive-data-after-ingest"></a>Arkivera data efter inmatning

Vissa data är inaktiva i molnet och är sällan, om de någonsin, används när de har lagrats. Följande livs cykel princip är konfigurerad för att arkivera data strax efter att den har matats in. Det här exemplet översätter block-blobar i lagrings kontot i behållaren `archivecontainer` till en Arkiv nivå. Över gången görs genom att agera på blobbar 0 dagar efter senaste ändrings tid:

> [!NOTE] 
> Vi rekommenderar att du överför dina blobar direkt till Arkiv nivån för att vara mer effektiv. Du kan använda huvudet x-MS-Access-Tier för [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) eller [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) med rest version 2018-11-09 och nyare eller våra senaste Blob Storage-klient bibliotek. 

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

### <a name="expire-data-based-on-age"></a>Förfaller data baserat på ålder

Vissa data förväntas gå ut dagar eller månader efter att de har skapats. Du kan konfigurera en princip för livs cykel hantering så att data upphör att gälla genom att ta bort baserat på data ålder. I följande exempel visas en princip som tar bort alla block blobbar som är äldre än 365 dagar.

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

### <a name="delete-data-with-blob-index-tags"></a>Ta bort data med BLOB-Taggar
Vissa data bör bara upphöra att gälla om de uttryckligen har marker ATS för borttagning. Du kan konfigurera en princip för livs cykel hantering så att den upphör att gälla data som är taggade med attribut för BLOB index nyckel/värde. I följande exempel visas en princip som tar bort alla block blobbar taggade med `Project = Contoso` . Mer information om BLOB-indexet finns i [Hantera och hitta data på Azure Blob Storage med BLOB index (för hands version)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="delete-old-snapshots"></a>Ta bort gamla ögonblicks bilder

För data som ändras och används regelbundet under hela livs längden används ögonblicks bilder ofta för att spåra äldre versioner av data. Du kan skapa en princip som tar bort gamla ögonblicks bilder baserat på ögonblicks bildens ålder. Ögonblicks bildens ålder bestäms genom utvärdering av ögonblicks bildens skapande tid. Den här princip regeln tar bort block BLOB-ögonblicksbilder i behållare `activedata` som är 90 dagar eller äldre efter att ögonblicks bilden har skapats.

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

## <a name="faq"></a>Vanliga frågor

**Jag skapade en ny princip, varför körs inte åtgärderna direkt?**  
Plattformen kör livs cykel principen en gång om dagen. När du har konfigurerat en princip kan det ta upp till 24 timmar innan vissa åtgärder körs för första gången.  

**Hur lång tid tar det för åtgärder att köra om jag uppdaterar en befintlig princip?**  
Den uppdaterade principen tar upp till 24 timmar innan den börjar gälla. När principen är aktive rad kan det ta upp till 24 timmar innan åtgärderna har körts. Därför kan det ta upp till 48 timmar innan princip åtgärderna har slutförts.   

**Jag har manuellt extraherat en arkiverad BLOB, hur gör jag för att förhindra att den flyttas tillbaka till Arkiv nivån tillfälligt?**  
När en BLOB flyttas från en åtkomst nivå till en annan ändras inte den senaste ändrings tiden. Om du manuellt dehydratiserar en arkiverad blob till frekvent nivå flyttas den tillbaka till Arkiv nivå av motorn för livs cykel hantering. Inaktivera regeln som påverkar den här bloben tillfälligt för att förhindra att den arkiveras igen. Återaktivera regeln när bloben kan flyttas tillbaka till Arkiv nivån på ett säkert sätt. Du kan också kopiera blobben till en annan plats om den behöver stanna i frekvent eller låg frekvent nivå.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du återställer data efter en oavsiktlig borttagning:

- [Mjuk borttagning för Azure Storage-blobar](../blobs/storage-blob-soft-delete.md)

Lär dig hur du hanterar och hittar data med BLOB-index:

- [Hantera och hitta data på Azure Blob Storage med BLOB-index](storage-manage-find-blobs.md)
