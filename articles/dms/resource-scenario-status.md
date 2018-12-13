---
title: Migrering scenariot databasstatus | Microsoft Docs
description: Läs mer om status för migreringsscenarier som stöds av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/11/2018
ms.openlocfilehash: 9eea3c28751ecaec0ffdb27e08f84cad1bf62acd
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53267874"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>Status för migreringsscenarier som stöds av Azure Database Migration Service
Azure Database Migration Service är utformad för att stödja olika Migreringsscenarier (källa/mål-par) för både offline (engångs-) och online (kontinuerlig synkronisering) migreringar. Scenario-täcks av Azure Database Migration Service utökas över tid. Nya scenarier läggs regelbundet. Den här artikeln identifierar migreringsscenarier som för närvarande stöds av Azure Database Migration Service och status (privat förhandsversion, offentlig förhandsversion eller allmänt tillgänglig) eller varje scenario.

## <a name="offline-versus-online-migrations"></a>Offline och online migreringar
När du migrerar databaser till Azure med hjälp av Azure Database Migration Service, kan du utföra en offline eller en online-migrering. Med *offline* migreringar, nedtid börjar samtidigt som startar migreringen. För *online* migreringar, avbrottstiden är begränsad till den tid som krävs för att klippa ut över till den nya miljön när migreringen är klar. Vi rekommenderar att du testar en offline-migrering för att avgöra om driftstoppet är okej; Annars kan du utföra en online-migrering.

## <a name="migration-scenario-status"></a>Status för scenario
Status för varje Migreringsscenario som stöds av Azure Database Migration Service varierar med tiden. I allmänhet scenarier är introducerades i **privat förhandsgranskning**, och genom att dra nytta av funktionerna som kräver att en kund skicka en utnämning via den [plats DMS förhandsversion](https://aka.ms/dms-preview). När privat förhandsgranskning slutförs, scenario status ändras till **Public Preview**. Alla Azure Database Migration Service-användare kan dra nytta av migreringsscenarier som är allmänt tillgänglig förhandsversion. Dock migrerar kanske inte tillgänglig i alla regioner och funktionerna kan genomgå ytterligare ändringar innan slutversionen. När ett Migreringsscenario blir **allmänt tillgänglig**, sista, som släpptes status, funktionen är klar och tillgänglig för alla användare med Azure Database Migration Service. 

## <a name="migration-scenario-support"></a>Migreringsstöd för scenario

Följande tabeller visar vilka migreringsscenarier som stöds när du använder Azure Database Migration Service.

> [!NOTE]
> Om ett scenario som visas som stöds under inte visas i användargränssnittet kan du kontakta den [Data Migreringsteam](mailto:datamigrationteam@microsoft.com) för ytterligare information.

### <a name="offline-one-time-migration-support"></a>Stöd för offline-migrering (engångs)
I följande tabell visas Azure Database Migration Service stöd för offline-migrering.

| Mål  | Källa | Support |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS-SQL  |  ✔ |
|   | Oracle  |   |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS-SQL  | ✔ |
|   | Oracle  | ✔  |
| **Azure SQL-MV**  | SQL Server  | ✔ |
|   | Oracle  |   |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB för MySQL**  | MySLQ  |  |
|   | RDS MySQL  |  |
| **Azure DB för PostgresSQL**  | PostgreSQL |  |
|  | RDS-PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>Migreringsstöd online (kontinuerlig synkronisering)
I följande tabell visas Azure Database Migration Service stöd för online migreringar.

| Mål  | Källa | Support |
| ------------- | ------------- | :-------------: |
| **Azure SQL DB**  | SQL Server | ✔ |
|   | RDS-SQL  |   |
|   | Oracle  |  ✔ |
| **Azure SQL DB MI**  | SQL Server  | ✔ |
|   | RDS-SQL  |  |
|   | Oracle  | ✔  |
| **Azure SQL-MV**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Azure DB för MySQL**  | MySLQ  | ✔ |
|   | RDS MySQL  | ✔ |
| **Azure DB för PostgresSQL**  | PostgreSQL | ✔ |
|  | RDS-PostgreSQL  | ✔ |

## <a name="next-steps"></a>Nästa steg
En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [vad är Azure Database Migration Service](dms-overview.md). 
