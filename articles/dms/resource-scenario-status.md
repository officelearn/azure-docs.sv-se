---
title: Status för databasmigreringsscenario
titleSuffix: Azure Database Migration Service
description: Lär dig mer om status för migreringsscenarierna som stöds av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 9652b78674d6a6b905eb049564d1b17cdc7c17b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254919"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Status för migreringsscenarier som stöds av Azure Database Migration Service

Azure Database Migration Service är utformad för att stödja olika migreringsscenarier (käll-/målpar) för både offlinemigreringar (engångsåtgärder) och onlinemigreringar (kontinuerlig synkronisering). Scenariotäckningen som tillhandahålls av Azure Database Migration Service förlängs med tiden. Nya scenarier läggs till regelbundet. Den här artikeln identifierar migreringsscenarier som för närvarande stöds av Azure Database Migration Service och status (privat förhandsversion, offentlig förhandsversion eller allmän tillgänglighet) för varje scenario.

## <a name="offline-versus-online-migrations"></a>Offline kontra onlinemigreringar

Med Azure Database Migration Service kan du göra en offline- eller onlinemigrering. Med *offline* offlinemigreringar börjar programstoppet samtidigt som migreringen startar. Om du vill begränsa driftstoppet till den tid som krävs för att gå över till den nya miljön när migreringen är klar använder du en *onlinemigrering.* Vi rekommenderar att du testar en offlinemigrering för att avgöra om driftstoppet är acceptabelt. Om inte, gör en onlinemigrering.

## <a name="migration-scenario-status"></a>Status för migreringsscenario

Statusen för migreringsscenarier som stöds av Azure Database Migration Service varierar med tiden. I allmänhet släpps scenarier först i **privat förhandsversion**. Att delta i privat förhandsversion kräver att kunderna skickar in en nominering via [DMS Preview-webbplatsen](https://aka.ms/dms-preview). Efter privat förhandsgranskning ändras scenariostatusen till **offentlig förhandsversion**. Azure Database Migration Service-användare kan prova migreringsscenarier i offentlig förhandsversion direkt från användargränssnittet. Ingen anmälan krävs.  Migreringsscenarier i offentlig förhandsversion kanske inte är tillgängliga i alla regioner och kan genomgå ytterligare ändringar innan den slutliga versionen. Efter offentlig förhandsversion ändras scenariostatusen till **allmänt tillgänglig**. Allmän tillgänglighet (GA) är den slutliga utgivningsstatusen och funktionen är fullständig och tillgänglig för alla användare.

## <a name="migration-scenario-support"></a>Stöd för migreringsscenario

Följande tabeller visar vilka migreringsscenarier som stöds när du använder Azure Database Migration Service.

> [!NOTE]
> Om ett scenario som anges som stöds nedan inte visas i användargränssnittet kontaktar du aliaset [Fråga Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) för ytterligare information.

> [!IMPORTANT]
> Information om hur du visar alla scenarier som för närvarande stöds av Azure Database Migration Service i privat förhandsversion finns på [webbplatsen FÖR FÖRHANDSVERSIONEN av DMS](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Stöd för offlinemigrering (engångs)

I följande tabell visas stöd för Azure Database Migration Service för offlinemigreringar.

| Mål  | Källa | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-databas** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Virtuell Azure SQL-dator** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB för PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Stöd för migrering online (kontinuerlig synkronisering)

I följande tabell visas stöd för Azure Database Migration Service för onlinemigreringar.

| Mål  | Källa | Support | Status |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL-databas** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS SQL | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | Allmän tillgänglighet (GA) |
|   | RDS SQL | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle | ✔ | Privat förhandsgranskning |
| **Virtuell Azure SQL-dator** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för MySQL** | MySQL | ✔ | Allmän tillgänglighet (GA) |
|   | RDS MySQL | ✔ | Allmän tillgänglighet (GA) |
| **Azure DB för PostgreSQL** | PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | RDS PostgreSQL | ✔ | Allmän tillgänglighet (GA) |
|   | Oracle | ✔ | Offentlig förhandsgranskning |

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [Vad är Migreringstjänsten för Azure Database](dms-overview.md).
