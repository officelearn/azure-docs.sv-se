---
title: "Utföra åtgärder i Azure Cosmos DB tabell API med PowerShell | Microsoft Docs"
description: Hur fungerar Azure Cosmos DB tabell API med PowerShell
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/02/2017
ms.author: robinsh
ms.openlocfilehash: 35d05b7003d731610df816c8470acc9133a4a6de
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>Utföra åtgärder i Azure Cosmos DB tabell API med Azure PowerShell 

>[!NOTE]
>Azure Cosmos DB tabell API innehåller premium-funktioner för tabellagring, till exempel NYCKELFÄRDIGT global distributionsplatsen, låg latens läsningar och skrivningar, automatisk sekundära indexering och dedikerad genomströmning. PowerShell-kommandon i den här artikeln fungerar för både Azure Cosmos DB tabell API och Azure Table storage, men den här artikeln är specifika för Azure Cosmos DB tabell API i de flesta fall. Om du använder Azure Table storage, se [utföra Azure Table storage-åtgärder med Azure PowerShell](table-storage-how-to-use-powershell.md).
>

Azure Cosmos DB tabell API kan du lagra och fråga stora mängder strukturerad, icke-relationella data. Huvudkomponenterna i tjänsten är tabeller, enheter och egenskaper. En tabell är en samling med entiteter. En entitet är en uppsättning egenskaper. Varje entitet kan ha upp till 252 egenskaper, som är alla namn / värde-par. Den här artikeln förutsätter att du redan är bekant med principerna för Azure Cosmos DB tabell API. Detaljerad information finns i [introduktion till Azure Cosmos DB tabell API](table-introduction.md) och [skapar ett .NET-program med hjälp av tabellen API](create-table-dotnet.md).

Den här artikeln beskrivs vanliga tabell-API: et. Lär dig att: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägg till tabellentiteter
> * Fråga en tabell
> * Ta bort tabellentiteter

## <a name="prerequisites"></a>Krav

Exemplen kräver Azure PowerShell Modulversion 4.4.0 eller senare. Kör i PowerShell-fönster `Get-Module -ListAvailable AzureRM` versionen. Om inget att visas eller måste du uppgradera, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). 

När Azure PowerShell installerades eller uppdaterades, måste du installera modulen **AzureRmStorageTable**, som innehåller kommandon för att hantera enheterna. Om du vill installera den här modulen kör du PowerShell som administratör och Använd den **installera modulen** kommando.

```powershell
Install-Module AzureRmStorageTable
```

När Azure Cosmos DB tabell API är i förhandsversionen kan behöva du också installera dess sammansättningar lokalt för att kunna använda dessa PowerShell-cmdlets. Instruktioner om hur du gör finns [Azure RM lagring tabeller PowerShell-modulen för Cosmos DB tabeller](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/).

Om du vill prova följande övningar behöver du ett konto för Azure DB som Cosmos-databasen. Om du inte redan har en, skapa en ny Azure Cosmos DB-konto med hjälp av den [Azure-portalen](https://portal.azure.com). Hur du skapar ett nytt databaskonto finns [Azure Cosmos DB: skapa ett databaskonto](create-table-dotnet.md#create-a-database-account).

Hämta den databas kontogruppen namn och en resurs från portalen. Du måste dessa värden att placera i ditt skript för att komma åt tabellerna. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>Skapa en tabell eller referera till en tabell

Om du vill skapa en tabell eller hämta en referens till en tabell använder **Get-AzureStorageTableTable**. Om du anropar den här cmdleten med namnet på en tabell som inte finns, skapas en ny tabell med samma namn och returnerar en referens till den nya tabellen. Om tabellen finns returnerar en referens till den befintliga tabellen.

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

Du kan lista tabellerna i Azure DB som Cosmos-konto med hjälp av PowerShell, men du kan logga in på portalen och finns i tabellen. Nu ska vi titta på hur du hanterar enheter i tabellen.

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Ta bort en tabell 

PowerShell stöder inte ta bort tabeller från Azure Cosmos DB. Om du vill ta bort en tabell, gå till den [Azure-portalen](https://azure.portal.com), leta upp Azure DB som Cosmos-kontot du använder, hitta och ta bort tabellen. 

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resursgrupp och skapa ett nytt Azure DB som Cosmos-konto i gruppen, kan du ta bort alla tillgångar har du skapat i den här övningen genom att ta bort resursgruppen. Det här kommandot tar bort alla resurser som ingår i gruppen samt resursgruppen sig själv.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln du lärt dig om vanliga tabell-API: et med PowerShell, inklusive hur du: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägg till tabellentiteter
> * Fråga en tabell
> * Ta bort tabellentiteter

Mer information finns i följande artiklar:

* [Arbeta med Azure Storage-tabeller från PowerShell](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/)

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.