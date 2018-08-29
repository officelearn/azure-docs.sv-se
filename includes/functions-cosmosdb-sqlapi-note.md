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
ms.openlocfilehash: bb915c18965a55c2b1fc7fe3cf1fa923d7f43fd6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052527"
---
Azure Cosmos DB-bindningar stöds endast för användning med SQL-API:n. För övriga Azure Cosmos DB-API:er bör du få tillgång till databasen från din funktion med hjälp av API:ns statiska klient, bland annat [API för MongoDB](../articles/cosmos-db/mongodb-introduction.md), [API för Cassandra](../articles/cosmos-db/cassandra-introduction.md), [API för Gremlin](../articles/cosmos-db/graph-introduction.md) och [API för Table](../articles/cosmos-db/table-introduction.md).
