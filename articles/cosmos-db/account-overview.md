---
title: Arbeta med Azure Cosmos DB konton
description: Den här artikeln beskriver hur du skapar och använder Azure Cosmos-konton. Den visar också hierarkin för element i ett Azure Cosmos-konto
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/09/2020
ms.reviewer: sngun
ms.openlocfilehash: a68207b3e5390a26e8d62ee6b725b0768ea565a4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85116085"
---
# <a name="work-with-azure-cosmos-account"></a>Arbeta med ett Azure Cosmos-konto

Azure Cosmos DB är en fullständigt hanterad PaaS-tjänst (Platform-as-a-Service). Om du vill börja använda Azure Cosmos DB bör du först skapa ett Azure Cosmos-konto i din Azure-prenumeration. Ditt Azure Cosmos-konto innehåller ett unikt DNS-namn och du kan hantera ett konto med hjälp av Azure Portal, Azure CLI eller med olika språkspecifika SDK: er. Mer information finns i [Hantera ditt Azure Cosmos-konto](how-to-manage-database-account.md).

Azure Cosmos-kontot är den grundläggande enheten för global distribution och hög tillgänglighet. För att globalt distribuera dina data och data flödet i flera Azure-regioner kan du när som helst lägga till och ta bort Azure-regioner till ditt Azure Cosmos-konto. Du kan konfigurera ditt Azure Cosmos-konto så att det har antingen en eller flera Skriv regioner. Mer information finns i [så här lägger du till och tar bort Azure-regioner till ditt Azure Cosmos-konto](how-to-manage-database-account.md). Du kan konfigurera [standard konsekvens](consistency-levels.md) nivån på Azure Cosmos-kontot. Azure Cosmos DB tillhandahåller omfattande service avtal-dataflöde, svars tid vid 99 percentil, konsekvens och hög tillgänglighet. Mer information finns i [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Om du vill hantera åtkomsten till alla data i ditt Azure Cosmos-konto på ett säkert sätt kan du använda de [huvud nycklar](secure-access-to-data.md) som är kopplade till ditt konto. För att ytterligare säkra åtkomsten till dina data kan du konfigurera en [VNet-tjänstens slut punkt](vnet-service-endpoint.md) och [IP-brandvägg](firewall-support.md) på ditt Azure Cosmos-konto. 

## <a name="elements-in-an-azure-cosmos-account"></a>Element i ett Azure Cosmos-konto

Azure Cosmos-behållaren är den grundläggande enheten för skalbarhet. Du kan i princip ha ett obegränsat, allokerat data flöde (RU/s) och lagrings utrymme i en behållare. Azure Cosmos DB transparent partitionering av din behållare med hjälp av den logiska partitionsnyckel som du anger för att elastiskt skala ditt etablerade data flöde och lagrings utrymme elastiskt. Mer information finns i [arbeta med Azure Cosmos-behållare och-objekt](databases-containers-items.md).

För närvarande kan du skapa högst 100 Azure Cosmos-konton under en Azure-prenumeration. Ett enda Azure Cosmos-konto kan i princip hantera obegränsad mängd data och etablerade data flöden. Om du vill hantera dina data och ett allokerat data flöde kan du skapa en eller flera Azure Cosmos-databaser under ditt konto och inom den databasen, kan du skapa en eller flera behållare. Följande bild visar hierarkin för element i ett Azure Cosmos-konto:

:::image type="content" source="./media/account-overview/hierarchy.png" alt-text="Hierarki för ett Azure Cosmos-konto" border="false":::

## <a name="next-steps"></a>Nästa steg

Lär dig hur du hanterar ditt Azure Cosmos-konto och andra koncept:

* [Så här hanterar du ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Global distribution](distribute-data-globally.md)
* [Konsekvensnivåer](consistency-levels.md)
* [Arbeta med Azure Cosmos-behållare och-objekt](databases-containers-items.md)
* [VNET-tjänstens slut punkt för ditt Azure Cosmos-konto](vnet-service-endpoint.md)
* [IP-brandvägg för ditt Azure Cosmos-konto](firewall-support.md)
* [Anvisningar för att lägga till och ta bort Azure-regioner i ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
