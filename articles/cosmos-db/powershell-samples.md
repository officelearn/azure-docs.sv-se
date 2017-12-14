---
title: "Azure PowerShell-exempel för Azure Cosmos DB | Microsoft Docs"
description: "Azure PowerShell-exempel - skript som hjälper dig att skapa och hantera Azure DB som Cosmos-konton."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 10/16/2017
ms.author: mimig
ms.openlocfilehash: de892cc631585c55b0c15f4efe1e06ad55afdce5
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-exempel för Azure Cosmos DB

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Följande tabell innehåller länkar till exempel Azure PowerShell-skript för Azure Cosmos DB. Just nu kan du bara hantera Azure Cosmos DB accountlayer via PowerShell; andra resurser som databaser och samlingar kan inte hanteras via PowerShell.

| |  |
|---|---|
|**Skapa ett Azure DB som Cosmos-konto**||
|[Skapa ett konto för SQL-API](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en enda Azure Cosmos DB-konto som ska användas med SQL-API. |
|**Skala Azure Cosmos DB**||
|[Replikera Azure DB som Cosmos-kontot i flera områden och konfigurera redundans prioriteter](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Replikerar globalt kontodata i flera regioner med angivna redundans prioritet.|
|**Skydda Azure Cosmos DB**||
| [Hämta nycklar](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämtar den primära och sekundära master skrivning och primära och sekundära skrivskyddade nycklar för kontot.|
| [Hämta anslutningssträngen för MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämtar anslutningssträngen till Anslut MongoDB-appen till ditt konto i Azure Cosmos DB.|
|[Återskapa nycklar](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Återskapar nyckeln master eller skrivskyddat för kontot.|
|[Skapa en brandvägg](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en inkommande IP-principer för åtkomstkontroll för att begränsa åtkomst till kontot från en godkänd uppsättning datorer eller molntjänster.|
|**Hög tillgänglighet, katastrofåterställning, säkerhetskopiering och återställning**||
|[Konfigurera principen för växling vid fel](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Anger redundans prioriteten för varje region där kontot replikeras.|
|||
