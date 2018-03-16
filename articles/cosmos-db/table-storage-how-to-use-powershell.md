---
title: "Utföra åtgärder för Azure Table storage med PowerShell | Microsoft Docs"
description: "Utföra åtgärder för Azure Table storage med PowerShell"
services: cosmos-db
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: robinsh
ms.openlocfilehash: c09809e9cf513dbb9420f675bbf431c176f740bd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Utföra åtgärder för Azure Table storage med Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table storage är en NoSQL-datalager som du kan använda för att lagra och fråga stora mängder strukturerad, icke-relationella data. Huvudkomponenterna i tjänsten är tabeller, enheter och egenskaper. En tabell är en samling med entiteter. En entitet är en uppsättning egenskaper. Varje entitet kan ha upp till 252 egenskaper, som är alla namn / värde-par. Den här artikeln förutsätter att du redan är bekant med principerna för Azure Table Storage-tjänsten. Detaljerad information finns i [förstå den tabelltjänst-datamodellen](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) och [komma igång med Azure Table storage med hjälp av .NET](table-storage-how-to-use-dotnet.md).

Den här artikeln beskrivs vanliga åtgärder för Azure Table storage. Lär dig att: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägg till tabellentiteter
> * Fråga en tabell
> * Ta bort tabellentiteter
> * Ta bort en tabell

Den här artikeln visar hur du skapar ett nytt Azure Storage-konto i en ny resursgrupp, så du kan enkelt ta bort den när du är klar. Om du föredrar att använda ett befintligt lagringskonto, kan du göra det i stället.

Exemplen kräver Azure PowerShell Modulversion 4.4.0 eller senare. Kör i PowerShell-fönster `Get-Module -ListAvailable AzureRM` versionen. Om inget att visas eller måste du uppgradera, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). 

När Azure PowerShell installerades eller uppdaterades, måste du installera modulen **AzureRmStorageTable**, som innehåller kommandon för att hantera enheterna. Om du vill installera den här modulen kör du PowerShell som administratör och Använd den **installera modulen** kommando.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta listan över platser

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. Dessa exempel används **eastus**. Lagra det här värdet i variabeln **plats** för framtida användning.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup). 

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Lagra resursgruppens namn i en variabel för framtida användning. I det här exemplet en resursgrupp med namnet *pshtablesrg* skapas i den *eastus* region.

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa en standard Allmänt lagringskonto med lokalt redundant lagring (LRS) med hjälp av [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Hämta kontexten för lagringskontot som definierar lagringskontot som ska användas. När fungerar på ett lagringskonto, referera kontexten i stället för att tillhandahålla autentiseringsuppgifterna flera gånger.

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

Du kan skapa en tabell med de [ny AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable) cmdlet. I det här exemplet tabellen kallas `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Hämta en lista över tabeller i storage-konto

Hämta en lista över tabeller i storage-konto med [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Hämta en referens till en viss tabell

Om du vill utföra åtgärder på en tabell, måste en referens till en viss tabell. Hämta en referens med hjälp av [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable). 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Ta bort en tabell

Ta bort en tabell genom att använda [ta bort AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable). Den här cmdleten tar bort tabellen, samt alla data.

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat ett nytt resurs grupp och storage-konto i början av den här anvisningar kan du ta bort alla tillgångar som du har skapat i den här övningen genom att ta bort resursgruppen. Det här kommandot tar bort alla resurser som ingår i gruppen samt resursgruppen sig själv.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln du lärt dig om vanliga Azure Table storage-åtgärder med PowerShell, inklusive hur du: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägg till tabellentiteter
> * Fråga en tabell
> * Ta bort tabellentiteter
> * Ta bort en tabell

Mer information finns i följande artiklar

* [Storage PowerShell cmdletar](/powershell/module/azurerm.storage#storage)

* [Arbeta med Azure Storage-tabeller från PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.