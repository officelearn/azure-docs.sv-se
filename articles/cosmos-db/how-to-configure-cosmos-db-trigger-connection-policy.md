---
title: Princip för Azure Cosmos DB-utlösare
description: Lär dig hur du konfigurerar principen används av Azure Cosmos DB-utlösare
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755334"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Så här konfigurerar du principen används av Azure Cosmos DB-utlösare

Den här artikeln beskrivs hur du kan konfigurera principen när du använder Azure Cosmos DB-utlösare för att ansluta till ditt Azure Cosmos-konto.

## <a name="why-is-the-connection-policy-important"></a>Varför är principen som är viktigt?

Det finns två anslutningsläge - direkt och Gateway-läge. Läs mer om dessa lägen för anslutningen i den [prestandatips](./performance-tips.md#networking) artikeln. Som standard **Gateway** används för att upprätta alla anslutningar i Azure Cosmos DB-utlösare. Men kanske det inte det bästa alternativet för prestanda-drivna scenarier.

## <a name="changing-the-connection-mode-and-protocol"></a>Ändra anslutningsläge och protokoll

Det finns två viktiga-konfigurationsinställningar är tillgängliga för att konfigurera principen för klienten – den **anslutningsläge** och **anslutningsprotokoll**. Du kan ändra standard-anslutningsläge och protokoll som används av Azure Cosmos DB-utlösare och alla de [Azure Cosmos DB-bindningar](../azure-functions/functions-bindings-cosmosdb-v2.md#output)). Om du vill ändra standardinställningarna, du behöver att hitta den `host.json` filen i projektet Azure Functions eller Azure Functions-App och Lägg till följande [extra inställning](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Där `connectionMode` måste ha det önskade anslutningsläget (direkt eller Gateway) och `protocol` önskade anslutningsprotokoll (Tcp eller Https). 

Om projektet Azure Functions fungerar med V1 för Azure Functions runtime, konfigurationen har en liten namn skillnaden, bör du använda `documentDB` i stället för `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> När du arbetar med Azure Functions värd för Förbrukningsplan plan måste har varje instans en gräns för mängden socketanslutningar som den kan underhållas. När du arbetar med Direct / TCP-läge, av design fler anslutningar skapas och kan nå den [Standardförbrukningsplanen gränsen](../azure-functions/manage-connections.md#connection-limit), då du kan använda gatewayen läge eller kör dina Azure-funktioner i [Appläge Service](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Nästa steg

* [Anslutningsgränser i Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Prestandatips för Azure Cosmos DB](./performance-tips.md)
* [Kodexempel](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
