---
title: Översikt över Azure Database Migration Service | Microsoft Docs
description: Översikt över Azure Database Migration Service, som ger sömlös migrering från många databaskällor till Azure-Dataplattformar.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 38bce2316110259298ff478d6019b33c78a53d21
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533494"
---
# <a name="what-is-the-azure-database-migration-service"></a>Vad är Azure Database Migration Service?
Azure Database Migration Service är en helt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid (online migreringar).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrera databaser till Azure med välbekanta verktyg
Azure Database Migration Service integrerar några av funktionerna i vår befintliga verktyg och tjänster. Tjänsten ger kunderna med en omfattande lösning med hög tillgänglighet. Tjänsten använder den [Data Migration Assistant](https://aka.ms/dma) att generera bedömningsrapporter som ger rekommendationer att guida dig genom de ändringar som krävs innan du utför en migrering. Det är upp till du kan utföra alla åtgärder som krävs. När du är redo att påbörja migreringsprocessen utför Azure Database Migration Service alla nödvändiga steg. Du kan utlöses och Glöm ditt migreringsprojekt med tryggheten att veta att processen drar nytta av bästa praxis enligt Microsofts bedömning.

> [!NOTE]
> Användning av Azure Database Migration Service för att utföra en onlinemigrering kräver att en instans skapas baserad på prisnivån Premium.

## <a name="regional-availability"></a>Regional tillgänglighet
Uppdaterad information om regional tillgänglighet för Azure Database Migration Service finns i [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Prissättning
Uppdaterad information om priser för Azure Database Migration Service finns i [priser för Azure Database Migration Service](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Nästa steg
- [Status för migreringsscenarier som stöds av Azure Database Migration Service](resource-scenario-status.md).
- [Skapa en instans av Azure Database Migration Service med hjälp av Azure portal](quickstart-create-data-migration-service-portal.md).
- [Migrera SQLServer till Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
- [Översikt över krav för att använda Azure Database Migration Service](pre-reqs.md).
- [Vanliga frågor och svar om hur du använder Azure Database Migration Service](faq.md).
- [Tjänster och verktyg som är tillgängliga för migreringsscenarion](dms-tools-matrix.md).
