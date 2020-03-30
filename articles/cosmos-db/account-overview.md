---
title: Arbeta med Azure Cosmos DB-konton
description: I den här artikeln beskrivs hur du skapar och använder Azure Cosmos-konton. Den visar också hierarkin av element i ett Azure Cosmos-konto
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: d29ed68b2945b2473b33aa88176e6f5d832a0fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246908"
---
# <a name="work-with-azure-cosmos-account"></a>Arbeta med ett Azure Cosmos-konto

Azure Cosmos DB är en fullständigt hanterad plattform som en tjänst (PaaS). Om du vill börja använda Azure Cosmos DB bör du först skapa ett Azure Cosmos-konto i din Azure-prenumeration. Ditt Azure Cosmos-konto innehåller ett unikt DNS-namn och du kan hantera ett konto med hjälp av Azure Portal, Azure CLI eller genom att använda olika språkspecifika SDK:er. Mer information finns i [hur du hanterar ditt Azure Cosmos-konto](how-to-manage-database-account.md).

Azure Cosmos-kontot är den grundläggande enheten för global distribution och hög tillgänglighet. För att globalt distribuera dina data och dataflöde i flera Azure-regioner kan du lägga till och ta bort Azure-regioner till ditt Azure Cosmos-konto när som helst. Du kan konfigurera ditt Azure Cosmos-konto så att det har antingen en enda eller flera skrivregioner. Mer information finns i [hur du lägger till och tar bort Azure-regioner i ditt Azure Cosmos-konto](how-to-manage-database-account.md). Du kan konfigurera [standardkonsekvensnivån](consistency-levels.md) på Azure Cosmos-kontot. Azure Cosmos DB tillhandahåller omfattande SLA-system som omfattar dataflöde, svarstid vid den 99:e percentilen, konsekvensen och hög tillgänglighet. Mer information finns i [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Om du vill hantera åtkomsten till alla data i ditt Azure Cosmos-konto på ett säkert sätt kan du använda [huvudnycklarna](secure-access-to-data.md) som är kopplade till ditt konto. Om du vill ha ytterligare åtkomst till dina data kan du konfigurera en [slutpunkt för VNET-tjänsten](vnet-service-endpoint.md) och [IP-brandväggen](firewall-support.md) på ditt Azure Cosmos-konto. 

## <a name="elements-in-an-azure-cosmos-account"></a>Element i ett Azure Cosmos-konto

Azure Cosmos-behållaren är den grundläggande enheten för skalbarhet. Du kan praktiskt taget ha ett obegränsat etablerat dataflöde (RU/s) och lagring på en behållare. Azure Cosmos DB partitionerar transparent din behållare med den logiska partitionsnyckeln som du anger för att elastiskt skala ditt etablerade dataflöde och lagring. Mer information finns i [Arbeta med Azure Cosmos-behållare och objekt](databases-containers-items.md).

För närvarande kan du skapa högst 100 Azure Cosmos-konton under en Azure-prenumeration. Ett enda Azure Cosmos-konto kan praktiskt taget hantera obegränsad mängd data och etablerat dataflöde. Om du vill hantera dina data och etablerat dataflöde kan du skapa en eller flera Azure Cosmos-databaser under ditt konto och i databasen kan du skapa en eller flera behållare. Följande bild visar hierarkin av element i ett Azure Cosmos-konto:

![Hierarki för ett Azure Cosmos-konto](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Nästa steg

Läs om hur du hanterar ditt Azure Cosmos-konto och andra koncept:

* [Hantera ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Global distribution](distribute-data-globally.md)
* [Konsekvensnivåer](consistency-levels.md)
* [Arbeta med Azure Cosmos-behållare och objekt](databases-containers-items.md)
* [Slutpunkt för VNET-tjänst för ditt Azure Cosmos-konto](vnet-service-endpoint.md)
* [IP-brandvägg för ditt Azure Cosmos-konto](firewall-support.md)
* [Så här lägger du till och tar bort Azure-regioner i ditt Azure Cosmos-konto](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
