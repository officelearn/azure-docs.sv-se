---
title: Azure PowerShell-exempel för Azure Cosmos DB | Microsoft Docs
description: Azure PowerShell-exempel – Skript som hjälper dig att skapa och hantera Azure Cosmos DB-konton.
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 066826661381f40af2943359eb75abae8306bb7b
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855591"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till Azure PowerShell-exempelskript för Azure Cosmos DB. Just nu kan du bara hantera Azure Cosmos DB-kontot via PowerShell. Andra resurser, som databaser och containrar, kan inte hanteras via PowerShell.

| |  |
|---|---|
|**Skapa ett Azure Cosmos DB-konto**||
|[Skapa ett SQL API-konto](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med SQL-API:et. |
|[Skapa ett MongoDB API-konto](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med MongoDB API:et. |
|[Skapa ett Gremlin API-konto](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med Gremlin API:et. |
|**Skala Azure Cosmos DB**||
|[Replikera ett Azure Cosmos DB-konto i flera regioner och konfigurera redundansprioriteringar](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Replikerar kontodata globalt till flera regioner med en angiven redundansprioritet.|
|**Skydda Azure Cosmos DB**||
| [Hämta kontonycklar](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämtar de primära och sekundära huvudskrivnycklarna och de primära och sekundära skrivskyddade nycklarna för kontot.|
| [Hämta anslutningssträngen för MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämtar anslutningssträngen för att ansluta MongoDB-appen till Azure Cosmos DB-kontot.|
|[Återskapa kontonycklar](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Återskapar huvudnyckeln eller den skrivskyddade nyckeln för kontot.|
|[Skapa en brandvägg](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en princip för inkommande IP-åtkomstkontroll för att begränsa åtkomsten till kontot från en godkänd uppsättning datorer och/eller molntjänster.|
|**Hög tillgänglighet, haveriberedskap, säkerhetskopiering och återställning**||
|[Konfigurera redundansprincip](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Ställer in redundansprioritet för varje region i vilken kontot är replikerat.|
|||
