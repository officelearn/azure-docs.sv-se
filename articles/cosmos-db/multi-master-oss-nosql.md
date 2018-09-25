---
title: Med hjälp av Azure Cosmos DB multimaster med öppen källkod NoSQL-databaser
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963908"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>Med hjälp av Azure Cosmos DB multimaster med öppen källkod NoSQL-databaser

Flera huvudservrar support för Azure Cosmos DB är en inbyggd serversidan implementering som är tillgänglig för alla dessa erbjudanden om öppen källkod NoSQL som stöds av Cosmos DB. Det är också tillgänglig från alla Cosmos DB SDK: er som stöds.

Azure Cosmos DB är den första tjänsten i hela världen att erbjuda flera huvudservrar stöd för dessa NoSQL-databaser med öppen källkod.

|Modell  |Support  |
|---------|---------|
|MongoDB  | Aktiv-aktiv  |
|Graph  | Aktiv-aktiv |
|Cassandra  | Aktiv-aktiv   |

## <a name="use-mongodb-clients-with-multi-master"></a>Använda MongoDB-klienter med Multi-Master

Flera huvudservrar funktionen är aktiverad för MongoDB API-konton på samma sätt som den är aktiverad för andra API: er för Azure Cosmos DB. När du har aktiverat multimaster för MongoDB API-konton, kan varje instans av ett klientprogram välja dess önskad region för läsningar och skrivningar. MongoDB-drivrutinen perspektiv visas önskad region för klienten som ange primära repliken. På så sätt kan kan varje region i din distribuerad databas fungera som replikuppsättning primära. Azure Cosmos DB-multimaster kan du avsevärt minska skrivfördröjning för dina globalt distribuerade program för MongoDB. 

### <a name="set-the-primary-region"></a>Ange den primära regionen

Varje instans av en MongoDB-klient kan välja den primära regionen genom att lägga till `@<preferred_primary_region_name>` till fältet ”programnamn” accepteras av klientdrivrutinen MongoDB. De flesta drivrutiner acceptera detta i anslutningssträngen, till exempel:

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

När du har anslutit, kan något av följande fall inträffa:

* Om namnet på önskad region är tillgängligt som en skrivregion, sedan väljer Azure Cosmos DB den som den primära regionen.

* Om en önskad regionnamn inte har angetts, väljer Azure Cosmos DB en standardregion som den primära regionen.

* Om en önskad regionnamn anges, men om det inte är tillgängligt som en skrivregion för kontot, Välj den närmaste skrivregionen som är tillgängliga som den primära regionen Azure Cosmos DB.

Vissa drivrutiner anges som NodeJS-drivrutinen ska alltid första problemet skriver till värden inledande anslutningssträngen. För att säkerställa skrivningar dirigeras till önskad region, förutom appnamn för sådana drivrutiner bör du ändra DNS-namnet i anslutningssträngen för att inkludera områdesnamnet. Kontrollera att du anger Regionsnamn utan blanksteg. Exempel:

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>Läge för lösning av konflikt

Konflikt Upplösningsläge för Azure Cosmos DB MongoDB API-konton är alltid senaste-skrivaren-wins, med hjälp av den regionala servertidsstämpel som godkända skrivningen.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Multi-Master-stöd för Azure Cosmos DB MongoDB API-konton. Nu ska titta på följande resurser:

* [Så här aktiverar du multimaster för Azure Cosmos DB-konton](enable-multi-master.md)

* [Förstå konfliktlösning i Azure Cosmos DB](multi-master-conflict-resolution.md)
