---
title: "Nätverk topologier för Azure SQL DB hanteras instans migreringar med tjänsten Azure Database migrering | Microsoft Docs"
description: "Läs om käll- och konfigurationer för migrering databastjänsten."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/06/2018
ms.openlocfilehash: 892cff02b5b70f09236bb37ae786f180ddca9316
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Nätverkstopologier för hanterade Azure SQL DB-instans migreringar med tjänsten Azure Database migrering
I den här artikeln får du lära dig om olika nätverkstopologier att Azure databastjänsten migrering kan arbeta med för att tillhandahålla sömlös migrering till hanterade Azure SQL Database-instans från lokala SQL-servrar.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database hanteras-instans som konfigurerats för Hybrid arbetsbelastningar 
Använd den här topologin om din Azure SQL-hanterade databasinstans är anslutet till det lokala nätverket. Den här metoden ger den mest förenklad nätverksroutning och ger maximal datagenomströmning under migreringen.

![Nätverkstopologi för Hybrid arbetsbelastningar](media\resource-network-topologies\hybrid-workloads.png)

**Krav**
- Azure SQL-hanterade databasinstans och migrering tjänstinstans för Azure-databas skapas i samma Azure VNET i det här scenariot, men de använder olika undernät.  
- Det VNET som används i det här scenariot är också ansluten till det lokala nätverket med hjälp av ExpressRoute eller VPN.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL-hanterade databasinstans isoleras från det lokala nätverket
Använd den här nätverkstopologi om miljön kräver en eller flera av följande scenarier:
- Azure SQL-hanterade databasinstans är isolerad från lokal anslutning, men din migrering tjänstinstans för Azure-databas är anslutet till det lokala nätverket.
- Rollen principer för åtkomstkontroll (RBAC) finns på plats och du begränsa åtkomst till samma prenumeration som är värd för Azure SQL hanteras databasinstansen.
- Vnet som används för hanteras Azure SQL Database-instans och Azure databastjänst för migrering finns i olika prenumerationer.

![Nätverkstopologi för hanterade instansen isoleras från det lokala nätverket](media\resource-network-topologies\mi-isolated-workload.png)

**Krav**
- VNET som Azure-databas migreringstjänsten använder för det här scenariot måste också vara ansluten till det lokala nätverket med hjälp av ExpressRoute eller VPN.
- Skapa ett VNET-nätverk som peering mellan det VNET som används för hanteras Azure SQL Database-instans och Azure databastjänst för migrering.


## <a name="cloud-to-cloud-migrations"></a>Molnet till molnet migrering
Använd den här topologin om källan SQL Server är värd för en virtuell Azure-dator.

![Nätverkstopologi för moln-to-Cloud-migrering](media\resource-network-topologies\cloud-to-cloud.png)

**Krav**
- Skapa ett VNET-nätverk som peering mellan det VNET som används för hanteras Azure SQL Database-instans och Azure databastjänst för migrering.

## <a name="see-also"></a>Se även
- [Migrera SQLServer till Azure SQL-hanterade databasinstans](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Översikt över krav för att använda tjänsten Azure Database migrering](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Skapa ett virtuellt nätverk med Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Nästa steg
En översikt över Azure databastjänst för migrering och regional tillgänglighet under förhandsversion, finns i artikeln [vad är Azure databas migrering Service Preview](dms-overview.md). 