---
title: Hantera Azure Cosmos DB-resurser med hjälp av Azure CLI | Microsoft Docs
description: Använda Azure CLI för att hantera ditt Azure Cosmos DB-konto, databas och behållare.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 3446f4f71349d0b7290a2514edf46efb37203324
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019227"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>Hantera Azure Cosmos DB-resurser med hjälp av Azure CLI

Enligt följande anvisningar beskriver kommandon för att automatisera hanteringen av dina Azure Cosmos DB-konton, databaser och behållare med hjälp av Azure CLI. Den innehåller också kommandon för att skala dataflöden i behållare. Det finns referenssidor för alla Azure Cosmos DB CLI-kommandon i [referensguiden för Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb). Du kan också hitta fler exempel i [Azure CLI-exempel för Azure Cosmos DB](cli-samples.md), inklusive hur du skapar och hanterar Cosmos DB-konton, databaser och behållare för MongoDB, Gremlin, Cassandra och tabell-API.

Det här CLI-exempelskriptet skapar ett Azure Cosmos DB SQL API-konto, databas och behållare.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Skapa ett Azure Cosmos DB-konto

Om du vill skapa ett Azure Cosmos DB-konto med SQL-API, sessionskonsekvens, multimaster aktiverat i östra USA och västra USA, öppna Azure CLI eller cloud shell och kör följande kommando:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>Skapa en databas

Öppna Azure CLI för att skapa en Cosmos DB-databas, eller cloud shell och kör följande kommando:

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>Skapa en container

Öppna Azure CLI för att skapa en Cosmos DB-behållare med RU/s på 1 000 och en partitionsnyckel, eller cloud shell och kör följande kommando:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>Ändra genomströmningen i en behållare

Om du vill ändra dataflödet för en Cosmos DB-behållare till 400 RU/s, öppna Azure CLI eller cloud shell och kör följande kommando:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>Lista nycklar

När du skapar ett Azure Cosmos DB-konto, genererar två master åtkomstnycklar som kan användas för autentisering när Azure Cosmos DB-kontot används i tjänsten. Genom att tillhandahålla två åtkomstnycklar för kan Azure Cosmos DB du återskapa nycklarna utan avbrott på ditt Azure Cosmos DB-konto. Det finns också skrivskyddade nycklar för att autentisera skrivskyddade åtgärder. Det finns två skrivskyddade nycklar (primära och sekundära) och två skrivskyddade nycklar (primär eller sekundär). Du kan hämta nycklarna för ditt konto genom att köra följande kommando:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>Lista anslutningssträngar

Anslutningssträngen för att ansluta ditt program till Cosmos DB-kontot kan hämtas med hjälp av följande kommando.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>Återskapa kontonyckeln

Du bör ändra åtkomstnycklar till ditt Azure Cosmos DB-konto med jämna mellanrum för att skydda dina anslutningar. Två åtkomstnycklar tilldelas så att du kan upprätthålla anslutningar till Azure Cosmos DB-kontot som den ena åtkomstnyckeln medan du återskapar den andra åtkomstnyckeln.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns:

- [Installera Azure CLI](/cli/azure/install-azure-cli)
- [Azure CLI-referens](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Ytterligare Azure CLI-exempel för Azure Cosmos DB](cli-samples.md)