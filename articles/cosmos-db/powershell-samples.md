---
title: Azure PowerShell-exempel för Azure Cosmos DB
description: Azure PowerShell-exempel – Skript som hjälper dig att skapa och hantera Azure Cosmos DB-konton.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: 68e845a05f4ebe2d1f25b55c00042c8925c8109e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069307"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-exempel för Azure Cosmos DB

I följande tabell innehåller länkar till Azure PowerShell-exempelskript för Azure Cosmos DB för Core (SQL) API.

| |  |
|---|---|
|**Azure Cosmos-konton**||
|[Skapa ett konto](scripts/powershell/sql/ps-account-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos SQL API-konto. |
|[Skaffa ett konto](scripts/powershell/sql/ps-account-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta egenskaperna för ett Azure Cosmos-konto. |
|[Lägger till en region](scripts/powershell/sql/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Få ett Azure Cosmos-konto och lägger till en region i listan över platser. |
|[Ändra redundansprioritet för](scripts/powershell/sql/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändra redundansprioritet för ett Azure Cosmos-konto med en manuell redundans-utlösare. |
|[Uppdatera taggar](scripts/powershell/sql/ps-account-tags-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera taggar för ett Azure Cosmos-konto. |
|[Hämta kontonycklar](scripts/powershell/sql/ps-account-key-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta de primära och sekundära nycklarna för ett Azure Cosmos-konto. |
|[Återskapa kontonycklar](scripts/powershell/sql/ps-account-key-regenerate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Återskapa de primära och sekundära nycklarna för ett Azure Cosmos-konto. |
|[Lista anslutningssträngar](scripts/powershell/sql/ps-account-connection-string-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta de primära och sekundära anslutningssträngarna för ett Azure Cosmos-konto. |
|[Skapa IP-brandvägg](scripts/powershell/sql/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa en IP-brandväggsregel för ett Azure Cosmos-konto. |
|[Ta bort ett Azure Cosmos-konto](scripts/powershell/sql/ps-account-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ta bort ett Azure Cosmos-konto. |
|**Azure Cosmos-databaser**||
| [Skapa en databas](scripts/powershell/sql/ps-database-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa en databas i ett Azure Cosmos-konto.|
| [Skapa en databas med delade/databasnivå dataflöde](scripts/powershell/sql/ps-database-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa en Azure Cosmos-databas med databasnivå dataflöde som stöds och delas med dess behållare.|
| [Lista alla databaser](scripts/powershell/sql/ps-database-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Lista över alla databaser i ett Azure Cosmos-konto.|
| [Hämta en databas](scripts/powershell/sql/ps-database-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämta egenskaperna för en Azure Cosmos-databas.|
|**Azure Cosmos-behållare**||
| [Skapa en behållare](scripts/powershell/sql/ps-container-create.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa ett Azure Cosmos-behållare med dedikerat dataflöde.|
| [Skapa en behållare med delade dataflöde](scripts/powershell/sql/ps-container-create-shared.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa ett Azure Cosmos-behållare med dataflöde som delas med andra behållare i databasen.|
| [Skapa en behållare med index-princip](scripts/powershell/sql/ps-container-create-index-custom.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa ett Azure Cosmos-behållare med en princip för anpassat index.|
| [Skapa en behållare med ingen index-princip](scripts/powershell/sql/ps-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa ett Azure Cosmos-behållare med index princip stängs av.|
| [Skapa en behållare med unika nycklar och TTL](scripts/powershell/sql/ps-container-create-unique-key-ttl.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa en Azure-Cosmos behållare med en unik nyckel begränsning och time-to-live konfigurerats.|
| [Skapa en behållare med konfliktlösning](scripts/powershell/sql/ps-container-create-conflict-policy.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa ett Azure Cosmos-behållare med senaste skrivare vinner principen i konflikt med varandra.|
| [Lista över alla behållare](scripts/powershell/sql/ps-container-list.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Lista alla behållare i en Azure Cosmos-databas.|
| [Hämta en behållare](scripts/powershell/sql/ps-container-get.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämta egenskaperna för en behållare i en Azure Cosmos-databas.|
| [Ta bort en container](scripts/powershell/sql/ps-container-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Ta bort en behållare i en Azure Cosmos-databas.|
|||