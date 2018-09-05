---
title: Översikt över Azure Database Migration Service | Microsoft Docs
description: Översikt över Azure Database Migration Service, som ger sömlös migrering från många databaskällor till Azure-Dataplattformar.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 09/01/2018
ms.openlocfilehash: d59850b0234912b02b003f4fc8089d76130151ba
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666237"
---
# <a name="what-is-the-azure-database-migration-service"></a>Vad är Azure Database Migration Service?
Azure Database Migration Service är en fullständigt hanterad tjänst som utformats för att aktivera sömlös migrering från flera databaskällor till Azure-Dataplattformar med minimal avbrottstid.

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrera databaser till Azure med välbekanta verktyg
Azure Database Migration Service integrerar några av funktionerna i vår befintliga verktyg och tjänster. Tjänsten ger kunderna med en omfattande lösning med hög tillgänglighet. Tjänsten använder den [Data Migration Assistant](http://aka.ms/dma) att generera bedömningsrapporter som ger rekommendationer att guida dig genom de ändringar som krävs innan du utför en migrering. Det är upp till du kan utföra alla åtgärder som krävs. När du är redo att påbörja migreringen, utför alla nödvändiga steg i Azure Database Migration Service. Du kan utlöses och Glöm ditt migreringsprojekt med tryggheten att veta att processen drar nytta av bästa praxis enligt Microsofts bedömning.

## <a name="regional-availability"></a>Regional tillgänglighet
Azure Database Migration Service är nu tillgänglig i följande regioner:

![Azure Database Migration Service regional tillgänglighet](media\overview\dms-regional-availability.png)

> [!NOTE]
> Online-migrering och SKU rekommendation funktioner är för närvarande endast tillgängliga i den **centrala USA**, **östra USA 2**, och **Västeuropa** regioner.

Den senaste informationen om regional tillgänglighet för Azure Database Migration Service, på global infrastruktur med Azure-webbplats, se [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>Nästa steg
- [Skapa en instans av Azure Database Migration Service med hjälp av Azure portal](quickstart-create-data-migration-service-portal.md).
- [Migrera SQLServer till Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
- [Översikt över krav för att använda Azure Database Migration Service](pre-reqs.md).
- [Vanliga frågor och svar om hur du använder Azure Database Migration Service](faq.md).
