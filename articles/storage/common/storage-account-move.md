---
title: Flytta ett Azure Storage-konto till en annan region | Microsoft Docs
description: Visar hur du flyttar ett Azure Storage-konto till en annan region.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: c8578c518ac45bea147790028c2904c7ce36fffb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81459040"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Flytta ett Azure Storage-konto till en annan region

Om du vill flytta ett lagrings konto skapar du en kopia av ditt lagrings konto i en annan region. Sedan flyttar du dina data till det kontot med hjälp av AzCopy eller ett annat verktyg som du själv väljer.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> 
> * Exportera en mall.
> * Ändra mallen genom att lägga till mål regionen och lagrings kontots namn.
> * Distribuera mallen för att skapa det nya lagrings kontot.
> * Konfigurera det nya lagrings kontot.
> * Flytta data till det nya lagrings kontot.
> * Ta bort resurserna i käll regionen.

## <a name="prerequisites"></a>Krav

- Kontrol lera att tjänsterna och funktionerna som ditt konto använder stöds i mål regionen.

- För för hands versions funktioner ser du till att din prenumeration är vit listas för mål regionen.

<a id="prepare" />

## <a name="prepare"></a>Förbereda

För att komma igång, exportera och sedan ändra en Resource Manager-mall. 

### <a name="export-a-template"></a>Exportera en mall

Den här mallen innehåller inställningar som beskriver ditt lagrings konto. 

# <a name="portal"></a>[Portalen](#tab/azure-portal)

Exportera en mall med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **alla resurser** och välj sedan ditt lagrings konto.

3. Välj**Exportera mall**för > **Inställningar** > .

4. Välj **Hämta** på bladet **Exportera mall** .

5. Leta upp zip-filen som du laddade ned från portalen och zippa upp filen till en valfri mapp.

   Den här zip-filen innehåller de JSON-filer som utgör mallen och skripten för att distribuera mallen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Exportera en mall med hjälp av PowerShell:

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagrings konto som du vill flytta.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportera mallen för ditt käll lagrings konto. De här kommandona sparar en JSON-mall i den aktuella katalogen.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Ändra mallen 

Ändra mallen genom att ändra lagrings kontots namn och region.

# <a name="portal"></a>[Portalen](#tab/azure-portal)

Distribuera mallen med hjälp av Azure Portal:

1. I Azure-portalen väljer du **Skapa en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution** och trycker sedan på **RETUR**.

3. Välj **Malldistribution**.

    ![Azure Resource Manager-mallbibliotek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Välj **Skapa**.

5. Välj alternativet för att **skapa din egen mall i redigeringsprogrammet**.

6. Välj **Läs in fil**och följ sedan anvisningarna för att läsa in filen **Template. JSON** som du laddade ned i det sista avsnittet.

7. I filen **Template. JSON** anger du ett namn på mål lagrings kontot genom att ange standardvärdet för lagrings konto namnet. I det här exemplet anges standardvärdet för lagrings konto `mytargetaccount`namnet till.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är region namnet utan några blank steg, **centrala USA** = **, centrala.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Distribuera mallen med hjälp av PowerShell:

1. I filen **Template. JSON** anger du ett namn på mål lagrings kontot genom att ange standardvärdet för lagrings konto namnet. I det här exemplet anges standardvärdet för lagrings konto `mytargetaccount`namnet till.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Redigera egenskapen **location** i filen **Template. JSON** till mål regionen. I det här exemplet anges mål regionen `eastus`till.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Du kan hämta region koder genom att köra kommandot [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) .

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Flytta

Distribuera mallen för att skapa ett nytt lagrings konto i mål regionen. 

# <a name="portal"></a>[Portalen](#tab/azure-portal)

1. Spara filen **Template. JSON** .

2. Ange eller Välj egenskaps värden:

- **Prenumeration**: Välj en Azure-prenumeration.

- **Resursgrupp**: Välj **Skapa ny** och ge resursgruppen ett namn.

- **Plats**: Välj en Azure-plats.

3. Klicka på kryss rutan **Jag accepterar villkoren som anges ovan** och klicka sedan på knappen **Välj inköp** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Hämta det prenumerations-ID där du vill distribuera den offentliga mål-IP-adressen med [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Använd följande kommandon för att distribuera mallen:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Konfigurera det nya lagrings kontot

Vissa funktioner exporteras till en mall, så du måste lägga till dem i det nya lagrings kontot. 

I följande tabell visas dessa funktioner tillsammans med rikt linjer för att lägga till dem i ditt nya lagrings konto.

| Funktion    | Riktlinjer    |
|--------|-----------|
| **Principer för livs cykel hantering** | [Hantera Azure Blob Storage-livscykeln](../blobs/storage-lifecycle-management-concepts.md) |
| **Statiska webbplatser** | [Vara värd för en statisk webbplats i Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Prenumerationer på händelser** | [Reagera på Blob Storage-händelser](../blobs/storage-blob-event-overview.md) |
| **Aviseringar** | [Skapa, Visa och hantera aktivitets logg aviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Använda Azure CDN för att få åtkomst till blobbar med anpassade domäner över HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Om du ställer in ett CDN för käll lagrings kontot ändrar du bara ursprunget för din befintliga CDN till den primära BLOB-tjänstens slut punkt (eller den primära statiska webbplats slut punkten) för ditt nya konto. 

### <a name="move-data-to-the-new-storage-account"></a>Flytta data till det nya lagrings kontot

Här är några sätt att flytta data över.

: heavy_check_mark: **Azure Storage Explorer**

  Det är enkelt att använda och lämpligt för små data uppsättningar. Du kan kopiera behållare och fil resurser och sedan klistra in dem i mål kontot.

  Se [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/);

: heavy_check_mark: **AzCopy**

  Detta är den bästa metoden. Den är optimerad för prestanda.  Ett sätt att det går snabbare är att data kopieras direkt mellan lagrings servrar, så AzCopy inte använder datorns nätverks bandbredd. Använd AzCopy på kommando raden eller som en del av ett anpassat skript.

  Se [Kom igång med AZCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

: heavy_check_mark: **Azure Data Factory** 

  Använd bara det här verktyget om du behöver funktioner som inte stöds i den aktuella versionen av AzCopy. I den aktuella versionen av AzCopy kan du till exempel inte kopiera blobbar mellan konton som har ett hierarkiskt namn område. AzCopy bevarar inte heller fil åtkomst kontrol listor eller fil-tidsstämplar (till exempel: skapa och ändra tidsstämplar). 

  Se följande länkar:
  - [Kopiera data till eller från Azure Blob Storage med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Kopiera data till eller från Azure Data Lake Storage Gen2 med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopiera data från eller till Azure File Storage med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Kopiera data till och från Azure Table Storage med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Ta bort eller rensa

Om du vill börja om efter distributionen kan du ta bort mål lagrings kontot och upprepa stegen som beskrivs i avsnittet [förbereda](#prepare) och [Flytta](#move) i den här artikeln.

Ta bort käll lagrings kontot för att genomföra ändringarna och slutföra flyttningen av ett lagrings konto.

# <a name="portal"></a>[Portalen](#tab/azure-portal)

Ta bort ett lagrings konto med hjälp av Azure Portal:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna menyn med tjänster och väljer **lagrings konton** för att visa listan över dina lagrings konton.

2. Leta upp mål lagrings kontot som du vill ta bort och högerklicka på knappen **mer** (**...**) på höger sida av listan.

3. Välj **ta bort**och bekräfta.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort resurs gruppen och dess kopplade resurser, inklusive det nya lagrings kontot, använder du kommandot [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du flyttat ett Azure Storage-konto från en region till en annan och rensade käll resurserna.  Mer information om hur du flyttar resurser mellan regioner och haveri beredskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Migrera virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
