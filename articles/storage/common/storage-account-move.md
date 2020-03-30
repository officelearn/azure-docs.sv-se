---
title: Flytta ett Azure Storage-konto till en annan region | Microsoft-dokument
description: Visar hur du flyttar ett Azure Storage-konto till en annan region.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838705"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Flytta ett Azure Storage-konto till en annan region

Om du vill flytta ett lagringskonto skapar du en kopia av ditt lagringskonto i en annan region. Flytta sedan dina data till det kontot med hjälp av AzCopy eller något annat verktyg som du väljer.

I den här artikeln får du lära dig hur du:

> [!div class="checklist"]
> 
> * Exportera en mall.
> * Ändra mallen genom att lägga till målregionen och lagringskontonamnet.
> * Distribuera mallen för att skapa det nya lagringskontot.
> * Konfigurera det nya lagringskontot.
> * Flytta data till det nya lagringskontot.
> * Ta bort resurserna i källregionen.

## <a name="prerequisites"></a>Krav

- Kontrollera att de tjänster och funktioner som ditt konto använder stöds i målregionen.

- För förhandsgranskningsfunktioner kontrollerar du att din prenumeration är vitlistad för målregionen.

<a id="prepare" />

## <a name="prepare"></a>Förbereda

Exportera och sedan ändra en Resource Manager-mall för att komma igång. 

### <a name="export-a-template"></a>Exportera en mall

Den här mallen innehåller inställningar som beskriver ditt lagringskonto. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här exporterar du en mall med Hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla resurser** och välj sedan ditt lagringskonto.

3. Välj**mallen** **Exportera inställningar** > > inställningar .

4. Välj **Hämta** i **mallbladet Exportera.**

5. Leta reda på ZIP-filen som du hämtade från portalen och packa upp filen till en valfri mapp.

   Den här zip-filen innehåller DE Json-filer som består av mallen och skripten för att distribuera mallen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Så här exporterar du en mall med PowerShell:

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Om din identitet är associerad med mer än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på det lagringskonto som du vill flytta.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportera mallen för ditt källlagringskonto. Dessa kommandon sparar en json-mall i den aktuella katalogen.

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

Ändra mallen genom att ändra namn och region för lagringskonto.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här distribuerar du mallen med hjälp av Azure Portal:

1. I Azure-portalen väljer du **Skapa en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution** och trycker sedan på **RETUR**.

3. Välj **Malldistribution**.

    ![Azure Resource Manager-mallbibliotek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Välj **Skapa**.

5. Välj alternativet för att **skapa din egen mall i redigeringsprogrammet**.

6. Välj **Läs in filen**och följ sedan instruktionerna för att läsa in filen **template.json** som du hämtade i det sista avsnittet.

7. I **filen template.json** namnger du mållagringskontot genom att ange standardvärdet för lagringskontots namn. I det här exemplet anges standardvärdet `mytargetaccount`för lagringskontots namn på .
    
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
    Information om hur du hämtar regionplatskoder finns i [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är regionnamnet utan blanksteg, **Central US** = **centralus**.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Så här distribuerar du mallen med PowerShell:

1. I **filen template.json** namnger du mållagringskontot genom att ange standardvärdet för lagringskontots namn. I det här exemplet anges standardvärdet `mytargetaccount`för lagringskontots namn på .
    
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

2. Redigera **location** platsegenskapen i **filen template.json** till målområdet. I det här exemplet `eastus`anges målområdet till .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Du kan hämta regionkoder genom att köra kommandot [Get-AzLocation.](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Flytta

Distribuera mallen för att skapa ett nytt lagringskonto i målregionen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Spara **filen template.json.**

2. Ange eller välj egenskapsvärden:

- **Prenumeration**: Välj en Azure-prenumeration.

- **Resursgrupp**: Välj **Skapa ny** och ge resursgruppen ett namn.

- **Plats**: Välj en Azure-plats.

3. Klicka på **kryssrutan Jag godkänner de villkor som anges ovan** och klicka sedan på knappen Välj **köp.**

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Skaffa prenumerations-ID där du vill distribuera målbaserade offentliga IP [med Get-AzSubscription:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Använd de här kommandona för att distribuera mallen:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Konfigurera det nya lagringskontot

Vissa funktioner exporteras inte till en mall, så du måste lägga till dem i det nya lagringskontot. 

I följande tabell visas dessa funktioner tillsammans med vägledning för att lägga till dem i ditt nya lagringskonto.

| Funktion    | Riktlinjer    |
|--------|-----------|
| **Principer för livscykelhantering** | [Hantera Azure Blob Storage-livscykeln](../blobs/storage-lifecycle-management-concepts.md) |
| **Statiska webbplatser** | [Vara värd för en statisk webbplats i Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Prenumerationer på händelser** | [Reagera på Blob Storage-händelser](../blobs/storage-blob-event-overview.md) |
| **Aviseringar** | [Skapa, visa och hantera aktivitetsloggaviseringar med hjälp av Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Använda Azure CDN för att komma åt blobbar med anpassade domäner via HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Om du konfigurerar ett CDN för källlagringskontot ändrar du bara ursprunget för ditt befintliga CDN till den primära blob-tjänstslutpunkten (eller den primära statiska webbplatsslutpunkten) för ditt nya konto. 

### <a name="move-data-to-the-new-storage-account"></a>Flytta data till det nya lagringskontot

Här är några sätt att flytta över dina data.

:heavy_check_mark: **Utforskaren för Azure Storage**

  Den är enkel att använda och lämplig för små datamängder. Du kan kopiera behållare och filresurser och sedan klistra in dem i målkontot.

  Se [Utforskaren för Azure Storage](https://azure.microsoft.com/features/storage-explorer/);

:heavy_check_mark: **AzCopy**

  Detta är det bästa tillvägagångssättet. Den är optimerad för prestanda.  Ett sätt att det är snabbare, är att data kopieras direkt mellan lagringsservrar, så AzCopy inte använder nätverksbandbredden på din dator. Använd AzCopy på kommandoraden eller som en del av ett anpassat skript.

  Se [Komma igång med AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: Azure **Data Factory** 

  Använd bara det här verktyget om du behöver funktioner som inte stöds i den aktuella versionen av AzCopy. I den aktuella versionen av AzCopy kan du till exempel inte kopiera blobbar mellan konton som har ett hierarkiskt namnområde. Även AzCopy inte bevarar filåtkomst kontrolllistor eller fil tidsstämplar (Till exempel: skapa och ändrade tidsstämplar). 

  Se dessa länkar:
  - [Kopiera data till eller från Azure Blob-lagring med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Kopiera data till eller från Azure Data Lake Storage Gen2 med Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Kopiera data från eller till Azure File Storage med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Kopiera data till och från Azure Table-lagring med hjälp av Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Ignorera eller rensa

Om du vill börja om efter distributionen kan du ta bort mållagringskontot och upprepa stegen som beskrivs i avsnitten [Förbered](#prepare) och [flytta](#move) i den här artikeln.

Om du vill genomföra ändringarna och slutföra flytten av ett lagringskonto tar du bort källlagringskontot.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här tar du bort ett lagringskonto med hjälp av Azure-portalen:

1. I Azure-portalen expanderar du menyn till vänster för att öppna menyn med tjänster och väljer **Lagringskonton** för att visa listan över dina lagringskonton.

2. Leta reda på målet lagringskonto att ta bort, och högerklicka på **knappen Mer** (**...**) till höger om listan.

3. Välj **Ta bort**och bekräfta.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill ta bort resursgruppen och dess associerade resurser, inklusive det nya lagringskontot, använder du kommandot [Ta bort AzStorageAccount:](/powershell/module/az.resources/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du ett Azure-lagringskonto från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
