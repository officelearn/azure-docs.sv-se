---
title: Utföra åtgärder för Azure Table storage med PowerShell | Microsoft Docs
description: Utföra åtgärder för Azure Table storage med PowerShell.
services: cosmos-db
author: roygara
ms.service: cosmos-db
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: cosmosdb-table
ms.openlocfilehash: b1cae7dc553ce324349e66f1bcb8a281d7c7c7e0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995616"
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

Exemplen kräver Az PowerShell-moduler `Az.Storage (1.1.0 or greater)` och `Az.Resources (1.2.0 or greater)`. Kör i ett PowerShell-fönster `Get-Module -ListAvailable Az*` att hitta versionen. Om inget att visas eller om du behöver uppgradera kan du se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Med den här funktionen i Azure från PowerShell måste du ha den `Az` -modulen installerad. Den aktuella versionen av `AzTable` är inte kompatibelt med äldre AzureRM-modulen.
> Följ den [senaste Installationsinstruktioner för att installera modulen Az](/powershell/azure/install-az-ps) om det behövs.

När Azure PowerShell har installerats eller uppdaterats, måste du installera modulen **AzTable**, som innehåller kommandon för att hantera entiteterna. Installera den här modulen genom att köra PowerShell som administratör och Använd den **Install-Module** kommando.

> [!IMPORTANT]
> För modulen namn kompatibilitet orsakerna till att vi fortfarande publicerar det här samma modul med det gamla namnet `AzureRmStorageTables` i PowerShell-galleriet. Det här dokumentet ska referera till det nya namnet.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Add-AzAccount` och följ anvisningarna på skärmen.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta lista över platser

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. De här exemplen använder **eastus**. Store det här värdet i variabeln **plats** för framtida användning.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Store resursgruppens namn i en variabel för framtida användning. I det här exemplet, en resursgrupp med namnet *pshtablesrg* skapas i den *eastus* region.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa ett allmänt standardlagringskonto med lokalt redundant lagring (LRS) med hjälp av [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Glöm inte att ange ett unikt namn på lagringskontot. Hämta sedan kontexten som representerar storage-konto. När du arbetar med ett storage-konto, refererar du till kontexten i stället för att ange dina autentiseringsuppgifter flera gånger.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Skapa en ny tabell

Du kan skapa en tabell med den [New AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) cmdlet. I det här exemplet tabellen kallas `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Hämta en lista över tabeller i lagringskontot

Hämta en lista över tabeller i storage-konto med [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Hämta en referens till en viss tabell

För att utföra åtgärder på en tabell, behöver du en referens till tabellen. Hämta en referens med hjälp av [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Referens för CloudTable-egenskapen för en viss tabell

> [!IMPORTANT]
> Användning av CloudTable är obligatorisk när du arbetar med **AzTable** PowerShell-modulen. Anropa den **Get-AzTableTable** kommando för att hämta en referens till det här objektet. Det här kommandot skapar även tabellen om den inte redan finns.

Att utföra åtgärder på en tabell med **AzTable**, behöver du en referens till CloudTable egenskap för en specifik tabell.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Ta bort en tabell

Ta bort en tabell genom att använda [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Denna cmdlet tar bort tabellen, samt alla data.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny grupp och lagring resurskontot i början av den här anvisningen, kan du ta bort alla resurser som du har skapat i den här övningen genom att ta bort resursgruppen. Det här kommandot tar bort alla resurser som ingår i gruppen samt själva resursgruppen.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
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

* [Storage PowerShell cmdletar](/powershell/module/az.storage#storage)

* [Arbeta med Azure-tabeller från PowerShell - AzureRmStorageTable/AzTable PS-modulen v2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
