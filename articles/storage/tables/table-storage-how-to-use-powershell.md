---
title: Utföra Azure Table-lagringsåtgärder med PowerShell | Microsoft-dokument
description: Lär dig hur du kör vanliga uppgifter som att skapa, fråga, ta bort data från Azure Table storage-konto med hjälp av PowerShell.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.openlocfilehash: 746044aa835df52e61c234c8b5ca61164fffbbc5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545955"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Utföra Azure Table-lagringsåtgärder med Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table storage är ett NoSQL-datalager som du kan använda för att lagra och fråga stora uppsättningar med strukturerade, icke-relationella data. Huvudkomponenterna i tjänsten är tabeller, entiteter och egenskaper. En tabell är en samling entiteter. En entitet är en uppsättning egenskaper. Varje entitet kan ha upp till 252 egenskaper, som alla är namn-värde par. Den här artikeln förutsätter att du redan är bekant med Azure Table Storage Service-begreppen. Detaljerad information finns [i Förstå tabelltjänstdatamodellen](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) och [komma igång med Azure Table Storage med .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

Den här how-to-artikeln beskriver vanliga Azure Table-lagringsåtgärder. Lär dig att: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägga till tabellentiteter
> * Fråga en tabell
> * Ta bort tabellentiteter
> * Ta bort en tabell

Den här artikeln visar hur du skapar ett nytt Azure Storage-konto i en ny resursgrupp så att du enkelt kan ta bort det när du är klar. Om du hellre vill använda ett befintligt lagringskonto kan du göra det i stället.

Exemplen kräver Az PowerShell-moduler `Az.Storage (1.1.0 or greater)` och `Az.Resources (1.2.0 or greater)`. I ett PowerShell-fönster kör du `Get-Module -ListAvailable Az*` för att hitta versionen. Om ingenting visas, eller om du behöver uppgradera, se [Installera Azure PowerShell-modul](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Om du använder den här Azure-funktionen från PowerShell måste du ha modulen `Az` installerad. Den aktuella `AzTable` versionen av är inte kompatibel med den äldre AzureRM-modulen.
> Följ de [senaste installationsinstruktionerna för installation av Az-modulen](/powershell/azure/install-az-ps) om det behövs.

När Azure PowerShell har installerats eller uppdaterats måste du installera modulen **AzTable**, som har kommandon för att hantera entiteterna. Om du vill installera den här modulen kör du PowerShell som administratör och använder kommandot **Installera modul.**

> [!IMPORTANT]
> Av kompatibilitetsskäl för modulnamn publicerar vi `AzureRmStorageTables` fortfarande samma modul under det gamla namnet i PowerShell-galleriet. Det här dokumentet refererar bara till det nya namnet.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Add-AzAccount` och följ anvisningarna på skärmen.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta lista över platser

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. Dessa exempel använder **eastus**. Lagra det här värdet på **variabelplatsen** för framtida bruk.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Lagra resursgruppsnamnet i en variabel för framtida bruk. I det här exemplet skapas en resursgrupp med namnet *pshtablesrg* i *regionen eastus.*

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa ett standardkonto för allmänt bruk med lokalt redundant lagring (LRS) med [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Var noga med att ange ett unikt lagringskontonamn. Hämta sedan kontexten som representerar lagringskontot. När du agerar på ett lagringskonto kan du referera till kontexten i stället för att upprepade gånger ange dina autentiseringsuppgifter.

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

Om du vill skapa en tabell använder du cmdleten [New-AzStorageTable.](/powershell/module/az.storage/New-AzStorageTable) I det här exemplet `pshtesttable`kallas tabellen .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Hämta en lista över tabeller i lagringskontot

Hämta en lista över tabeller i lagringskontot med [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Hämta en referens till en viss tabell

Om du vill utföra åtgärder i en tabell behöver du en referens till den specifika tabellen. Få en referens med [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Referens CloudTable-egenskap för en viss tabell

> [!IMPORTANT]
> Användning av CloudTable är obligatoriskt när du arbetar med **AzTable** PowerShell-modul. Anropa kommandot **Get-AzStorageTable** för att hämta referensen till det här objektet. Det här kommandot skapar också tabellen om den inte redan finns.

Om du vill utföra åtgärder i en tabell med **AzTable**behöver du en referens till CloudTable-egenskapen för en viss tabell.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Ta bort en tabell

Om du vill ta bort en tabell använder du [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Den här cmdleten tar bort tabellen, inklusive alla dess data.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du skapade en ny resursgrupp och ett nytt lagringskonto i början av det här programmet kan du ta bort alla tillgångar som du har skapat i den här övningen genom att ta bort resursgruppen. Det här kommandot tar bort alla resurser som finns i gruppen samt själva resursgruppen.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln om hur du gör fick du lära dig om vanliga Azure Table-lagringsåtgärder med PowerShell, inklusive hur du: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägga till tabellentiteter
> * Fråga en tabell
> * Ta bort tabellentiteter
> * Ta bort en tabell

Mer information finns i följande artiklar

* [Storage PowerShell cmdletar](/powershell/module/az.storage#storage)

* [Arbeta med Azure-tabeller från PowerShell - AzureRmStorageTable/AzTable PS-modul v2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
