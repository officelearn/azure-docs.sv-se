---
title: Azure Functions-utlösare för Cosmos DB-anslutningsprincip
description: Lär dig hur du konfigurerar anslutningsprincipen som används av Azure Functions-utlösaren för Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 953121a9a15d4fef56d381e3aab85329fadacce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604962"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Konfigurera anslutningsprincipen som används av Azure Functions-utlösaren för Cosmos DB

I den här artikeln beskrivs hur du kan konfigurera anslutningsprincipen när du använder Azure Functions-utlösaren för Cosmos DB för att ansluta till ditt Azure Cosmos-konto.

## <a name="why-is-the-connection-policy-important"></a>Varför är anslutningsprincipen viktig?

Det finns två anslutningslägen - Direktläge och Gateway-läge. Mer information om dessa anslutningslägen finns i artikeln [med prestandatips.](./performance-tips.md#networking) Som standard används **Gateway** för att upprätta alla anslutningar på Azure Functions-utlösaren för Cosmos DB. Det kanske dock inte är det bästa alternativet för prestandadrivna scenarier.

## <a name="changing-the-connection-mode-and-protocol"></a>Ändra anslutningsläge och protokoll

Det finns två viktiga konfigurationsinställningar för att konfigurera klientanslutningsprincipen – **anslutningsläget** och **anslutningsprotokollet**. Du kan ändra standardanslutningsläget och protokollet som används av Azure Functions-utlösaren för Cosmos DB och alla [Azure Cosmos DB-bindningar](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Om du vill ändra standardinställningarna `host.json` måste du hitta filen i ditt Azure Functions-projekt eller Azure Functions App och lägga till följande [extrainställning:](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings)

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Var `connectionMode` måste ha önskat anslutningsläge `protocol` (Direkt eller Gateway) och önskat anslutningsprotokoll (Tcp eller Https). 

Om ditt Azure Functions-projekt fungerar med Azure Functions V1-körningen har `documentDB` konfigurationen `cosmosDB`en liten namnskillnad, bör du använda i stället för:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> När du arbetar med Azure Functions Consumption Plan Hosting-abonnemang har varje instans en gräns för hur många Socket-anslutningar som den kan underhålla. När du arbetar med Direkt/ TCP-läge skapas genom design fler anslutningar och kan nå [gränsen för förbrukningsplan](../azure-functions/manage-connections.md#connection-limit), i vilket fall du antingen kan använda gateway-läge eller köra dina Azure-funktioner i [apptjänstläge](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Nästa steg

* [Anslutningsgränser i Azure-funktioner](../azure-functions/manage-connections.md#connection-limit)
* [Prestandatips för Azure Cosmos DB](./performance-tips.md)
* [Kodexempel](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
