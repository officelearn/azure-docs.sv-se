---
title: Utföra åtgärder för Azure Table storage med PowerShell | Microsoft Docs
description: Utföra åtgärder för Azure Table storage med PowerShell.
services: cosmos-db
author: robinsh
ms.service: cosmos-db
ms.topic: article
ms.date: 03/14/2018
ms.author: robinsh
ms.component: tables
ms.openlocfilehash: 21023dd8adcf5ba623ac1bac3c2dbea0dfe04c36
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39524047"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Utföra åtgärder för Azure Table storage med Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table storage är en NoSQL-datalager som du kan använda för att lagra och fråga stora mängder strukturerad, icke-relationella data. Huvudkomponenterna i tjänsten är tabeller, entiteter och egenskaper. En tabell är en samling entiteter. En entitet är en uppsättning egenskaper. Varje entitet kan ha upp till 252 egenskaper, som är alla namn / värde-par. Den här artikeln förutsätter att du redan är bekant med principerna för Azure Table Storage-tjänsten. Detaljerad information finns i [förstå den tabelltjänst-datamodellen](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) och [komma igång med Azure Table storage med hjälp av .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

I den här artikeln beskriver vanliga Azure Table storage-åtgärder. Lär dig att: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägg till tabellentiteter
> * Fråga en tabell
> * Ta bort tabellenheter
> * Ta bort en tabell

I den här artikeln visar hur du skapar ett nytt Azure Storage-konto i en ny resursgrupp så att du kan enkelt ta bort den när du är klar. Om du föredrar att använda ett befintligt lagringskonto, kan du göra det i stället.

Exemplen kräver Azure PowerShell-Modulversion 4.4.0 eller senare. Kör i ett PowerShell-fönster `Get-Module -ListAvailable AzureRM` att hitta versionen. Om inget att visas eller om du behöver uppgradera kan du se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). 

När Azure PowerShell har installerats eller uppdaterats, måste du installera modulen **AzureRmStorageTable**, som innehåller kommandon för att hantera entiteterna. Installera den här modulen genom att köra PowerShell som administratör och Använd den **Install-Module** kommando.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Connect-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta lista över platser

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. De här exemplen använder **eastus**. Store det här värdet i variabeln **plats** för framtida användning.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup). 

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Store resursgruppens namn i en variabel för framtida användning. I det här exemplet, en resursgrupp med namnet *pshtablesrg* skapas i den *eastus* region.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa ett allmänt standardlagringskonto med lokalt redundant lagring (LRS) med hjälp av [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Hämta lagringskontokontexten som definierar lagringskontot som ska användas. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Skapa en ny tabell

Du kan skapa en tabell med den [New AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable) cmdlet. I det här exemplet tabellen kallas `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Hämta en lista över tabeller i lagringskontot

Hämta en lista över tabeller i storage-konto med [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Hämta en referens till en viss tabell

För att utföra åtgärder på en tabell, behöver du en referens till tabellen. Hämta en referens med hjälp av [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Ta bort en tabell

Ta bort en tabell genom att använda [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Denna cmdlet tar bort tabellen, samt alla data.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny grupp och lagring resurskontot i början av den här anvisningen, kan du ta bort alla resurser som du har skapat i den här övningen genom att ta bort resursgruppen. Det här kommandot tar bort alla resurser som ingår i gruppen samt själva resursgruppen.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln berättade om vanliga Azure Table storage-åtgärder med PowerShell, inklusive hur du: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägg till tabellentiteter
> * Fråga en tabell
> * Ta bort tabellenheter
> * Ta bort en tabell

Mer information finns i följande artiklar

* [Storage PowerShell cmdletar](/powershell/module/azurerm.storage#storage)

* [Arbeta med Azure Storage-tabeller från PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
