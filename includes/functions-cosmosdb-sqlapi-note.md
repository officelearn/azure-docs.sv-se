---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c54aa861a47b11756f05e003e9b944df6c5b0e28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61343005"
---
Azure Cosmos DB-bindningar stöds endast för användning med SQL-API:n. För alla andra Azure Cosmos DB API: er, du ska få åtkomst till databasen från din funktion med hjälp av statiska klienten för ditt API, inklusive [Azure Cosmos DB-API för MongoDB](../articles/cosmos-db/mongodb-introduction.md), [Cassandra API](../articles/cosmos-db/cassandra-introduction.md), [ Gremlin-API](../articles/cosmos-db/graph-introduction.md), och [tabell-API](../articles/cosmos-db/table-introduction.md).
