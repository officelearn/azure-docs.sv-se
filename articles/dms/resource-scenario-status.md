---
title: Migrering scenariot databasstatus | Microsoft Docs
description: Läs mer om status för migreringsscenarier som stöds av Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 7017566092530dce2359d45314ac00dca63b8ad0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886241"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status för migreringsscenarier som stöds av Azure Database Migration Service
Azure Database Migration Service är utformad för att stödja olika migreringsscenarion (källa/mål-par) för både offline (engångs-) och online (kontinuerlig synkronisering) migreringar. Scenario-täcks av Azure Database Migration Service utökas över tid. Nya scenarier läggs regelbundet. Den här artikeln identifierar migreringsscenarier som för närvarande stöds av Azure Database Migration Service och status (privat förhandsversion, offentlig förhandsversion eller allmänt tillgänglig) för varje scenario.

## <a name="offline-versus-online-migrations"></a>Offline och online migreringar
Du kan göra en offline- eller en online-migrering med Azure Database Migration Service. Med *offline* migreringar, nedtid börjar samtidigt som startar migreringen. Om du vill begränsa avbrott på den tid som krävs för att klippa ut över till den nya miljön när migreringen är klar kan du använda en *online* migrering. Vi rekommenderar att du testar en offline-migrering för att avgöra om driftstoppet är okej; Annars kan du göra en online-migrering.

## <a name="migration-scenario-status"></a>Status för scenario
Status för migreringsscenarier som stöds av Azure Database Migration Service varierar med tiden. I allmänhet scenarier är introducerades i **privat förhandsgranskning**. Delta i privat förhandsvisning kräver kunder kan skicka en utnämning via den [plats DMS förhandsversion](https://aka.ms/dms-preview). Efter privat förhandsgranskning scenariot status ändras till **Public Preview**. Azure Database Migration Service-användare kan testa Migreringsscenarier i offentlig förhandsversion direkt från användargränssnittet. Utan att behöva registrera måste anges.  Migreringsscenarier som offentlig förhandsversion kanske inte tillgänglig i alla regioner och kan genomgå ytterligare ändringar innan slutversionen. Efter den offentliga granskningen scenariot status ändras till **allmänt tillgänglig**. Allmänt tillgänglig (GA) är slutversionen status och funktionen är klar och tillgänglig för alla användare. 

## <a name="migration-scenario-support"></a>Migreringsstöd för scenario
Följande tabeller visar vilka migreringsscenarier som stöds när du använder Azure Database Migration Service.

> [!NOTE]
> Om ett scenario som visas som stöds under inte visas i användargränssnittet kan du kontakta den [Data Migreringsteam](mailto:datamigrationteam@microsoft.com) för ytterligare information.

> [!IMPORTANT]
> Scenarier som stöds av Azure Database Migration Service i privat förhandsgranskning för närvarande finns det [plats DMS förhandsversion](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Stöd för offline-migrering (engångs)
I följande tabell visas Azure Database Migration Service stöd för offline-migrering.

| Mål  | Källa | Support | Status |
| ------------- | ------------- | :-------------: | :-------------: |
| **Azure SQL-databas** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-SQL |  |  |
|   | Oracle |  |   |
| **Virtuell Azure SQL-dator** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Allmänt tillgänglig förhandsversion |
| **Azure DB för MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB för PostgreSQL** | PostgreSQL |  |
|  | RDS-PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Migreringsstöd online (kontinuerlig synkronisering)
I följande tabell visar Azure Database Migration Service stöd, offentlig förhandsversion eller allmänt tillgängliga, för online migreringar.

| Mål  | Källa | Support | Status |
| ------------- | ------------- | :-------------: | :-------------: |
| **Azure SQL-databas** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-SQL | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-SQL | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle | ✔ | Privat förhandsgranskning |
| **Virtuell Azure SQL-dator** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Allmänt tillgänglig förhandsversion |
| **Azure DB för MySQL** | MySQL | ✔ | Allmän tillgänglighet (GA) |
|   | RDS MySQL | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för PostgreSQL** | PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | RDS-PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle | ✔ | Privat förhandsgranskning |

## <a name="next-steps"></a>Nästa steg
En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [vad är Azure Database Migration Service](dms-overview.md).
