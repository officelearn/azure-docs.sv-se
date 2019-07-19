---
title: Azure Functions utlösare för Cosmos DB anslutnings princip
description: Lär dig hur du konfigurerar anslutnings principen som används av Azure Functions utlösare för Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 359b6a905e64046aad62b70ae53b993c86884ad2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335622"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Konfigurera anslutnings principen som används av Azure Functions utlösare för Cosmos DB

Den här artikeln beskriver hur du kan konfigurera anslutnings principen när du använder Azure Functions-utlösaren för Cosmos DB för att ansluta till ditt Azure Cosmos-konto.

## <a name="why-is-the-connection-policy-important"></a>Varför är anslutnings principen viktig?

Det finns två anslutnings lägen – direkt läge och gateway-läge. Mer information om dessa anslutnings lägen finns i artikeln [prestanda tips](./performance-tips.md#networking) . Som standard används **gatewayen** för att upprätta alla anslutningar i Azure Functions-utlösaren för Cosmos dB. Men det kanske inte är det bästa alternativet för prestanda drivna scenarier.

## <a name="changing-the-connection-mode-and-protocol"></a>Ändra anslutnings läge och protokoll

Det finns två nyckel konfigurations inställningar som är tillgängliga för att konfigurera klient anslutnings principen – **anslutnings läge** och **anslutnings protokoll**. Du kan ändra standard anslutnings läge och protokoll som används av Azure Functions-utlösaren för Cosmos DB och alla [Azure Cosmos DB bindningar](../azure-functions/functions-bindings-cosmosdb-v2.md#output)). Om du vill ändra standardinställningarna måste du leta upp `host.json` filen i Azure Functions-projektet eller Azure Functions-appen och lägga till följande [extra inställning](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Där `connectionMode` måste du ha önskat anslutnings läge (direkt eller Gateway) och `protocol` önskat anslutnings protokoll (TCP eller https). 

Om Azure Functions-projektet arbetar med Azure Functions v1-körningen har konfigurationen en liten namn skillnad, du bör använda `documentDB` i stället för: `cosmosDB`

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> När du arbetar med Azure Functions användnings plan som värd plan, har varje instans en gräns för antalet socketanslutningar som den kan underhålla. När du arbetar med direkt-/TCP-läge skapas fler anslutningar och du kan nå [gränsen för förbruknings planen](../azure-functions/manage-connections.md#connection-limit), i vilket fall du kan antingen använda Gateway-läge eller köra Azure Functions i [app service läge](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Nästa steg

* [Anslutnings gränser i Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Azure Cosmos DB prestanda tips](./performance-tips.md)
* [Kodexempel](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
