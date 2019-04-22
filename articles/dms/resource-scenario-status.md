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
ms.date: 04/04/2019
ms.openlocfilehash: f25bc9bc3a958b2fa97ae4d5ab3715b602110393
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58915483"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status för migreringsscenarier som stöds av Azure Database Migration Service
Azure Database Migration Service är utformad för att stödja olika migreringsscenarion (källa/mål-par) för både offline (engångs-) och online (kontinuerlig synkronisering) migreringar. Scenario-täcks av Azure Database Migration Service utökas över tid. Nya scenarier läggs regelbundet. Den här artikeln identifierar migreringsscenarier som för närvarande stöds av Azure Database Migration Service och status (privat förhandsversion, offentlig förhandsversion eller allmän tillgänglighet) för varje scenario.

## <a name="offline-versus-online-migrations"></a>Offline och online migreringar
Du kan göra en offline- eller en online-migrering med Azure Database Migration Service. Med *offline* migreringar, nedtid börjar samtidigt som startar migreringen. Om du vill begränsa avbrott på den tid som krävs för att klippa ut över till den nya miljön när migreringen är klar kan du använda en *online* migrering. Vi rekommenderar att du testar en offline-migrering för att avgöra om driftstoppet är okej; Annars kan du göra en online-migrering.

## <a name="migration-scenario-status"></a>Status för scenario
Status för migreringsscenarier som stöds av Azure Database Migration Service varierar med tiden. I allmänhet scenarier är introducerades i **privat förhandsgranskning**. Delta i privat förhandsvisning kräver kunder kan skicka en utnämning via den [plats DMS förhandsversion](https://aka.ms/dms-preview). Efter privat förhandsgranskning scenariot status ändras till **förhandsversion**. Azure Database Migration Service-användare kan testa Migreringsscenarier i offentlig förhandsversion direkt från användargränssnittet. Utan att behöva registrera måste anges.  Migreringsscenarier som offentlig förhandsversion kanske inte tillgänglig i alla regioner och kan genomgå ytterligare ändringar innan slutversionen. Efter den offentliga granskningen scenariot status ändras till **Allmänt tillgänglighet**. Allmän tillgänglighet (GA) är slutversionen status och funktionen är klar och tillgänglig för alla användare.

## <a name="migration-scenario-support"></a>Migreringsstöd för scenario
Följande tabeller visar vilka migreringsscenarier som stöds när du använder Azure Database Migration Service.

> [!NOTE]
> Om ett scenario som visas som stöds under inte visas i användargränssnittet kan du kontakta den [Data Migreringsteam](mailto:datamigrationteam@microsoft.com) för ytterligare information.

> [!IMPORTANT]
> Alla scenarier som stöds av Azure Database Migration Service i privat förhandsgranskning för närvarande finns det [plats DMS förhandsversion](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Stöd för offline-migrering (engångs)
I följande tabell visas Azure Database Migration Service stöd för offline-migrering.

| Målinrikta  | Källa | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | Allmänt tillgänglig |
|   | RDS-SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Allmänt tillgänglig |
|   | RDS-SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | Allmänt tillgänglig |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Offentlig förhandsversion |
| **Azure DB för MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB för PostgreSQL** | PostgreSQL |  |
|  | RDS-PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Migreringsstöd online (kontinuerlig synkronisering)
I följande tabell visas Azure Database Migration Service stöd för online migreringar.

| Målinrikta  | Källa | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | Allmänt tillgänglig |
|   | RDS-SQL | ✔ | Allmänt tillgänglig |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Allmänt tillgänglig |
|   | RDS-SQL | ✔ | Allmänt tillgänglig |
|   | Oracle | ✔ | Privat förhandsgranskning |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Offentlig förhandsversion |
| **Azure DB för MySQL** | MySQL | ✔ | Allmänt tillgänglig |
|   | RDS MySQL | ✔ | Allmänt tillgänglig |
| **Azure DB för PostgreSQL** | PostgreSQL | ✔ | Allmänt tillgänglig |
|   | RDS-PostgreSQL | ✔ | Allmänt tillgänglig |
|   | Oracle | ✔ | Privat förhandsgranskning |

## <a name="next-steps"></a>Nästa steg
En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [vad är Azure Database Migration Service](dms-overview.md).
