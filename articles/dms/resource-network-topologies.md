---
title: Nätverk topologier för Azure SQL-hanterade databasinstans migreringar med tjänsten Azure Database migrering | Microsoft Docs
description: Läs om käll- och konfigurationer för migrering databastjänsten.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 9fcee103854209016d73e29b598c9f33d35c4b6c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316875"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Nätverkstopologier för hanterade Azure SQL DB-instans migreringar med tjänsten Azure Database migrering
Den här artikeln beskrivs olika nätverkstopologier som tjänsten Azure Database migrering kan arbeta med för att tillhandahålla en omfattande migrering från lokala SQL-servrar till hanterade Azure SQL Database-instans.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database hanteras-instans som konfigurerats för Hybrid arbetsbelastningar 
Använd den här topologin om din Azure SQL-hanterade databasinstans är anslutet till det lokala nätverket. Den här metoden ger den mest förenklad nätverksroutning och ger maximal datagenomströmning under migreringen.

![Nätverkstopologi för Hybrid arbetsbelastningar](media\resource-network-topologies\hybrid-workloads.png)

**Krav**
- Azure SQL-hanterade databasinstans och migrering tjänstinstans för Azure-databas skapas i samma Azure VNET i det här scenariot, men de använder olika undernät.  
- Det VNET som används i det här scenariot är också ansluten till det lokala nätverket genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL-hanterade databasinstans isoleras från det lokala nätverket
Använd den här nätverkstopologi om miljön kräver en eller flera av följande scenarier:
- Azure SQL-hanterade databasinstans är isolerad från lokal anslutning, men din migrering tjänstinstans för Azure-databas är anslutet till det lokala nätverket.
- Om principer för rollbaserad åtkomstkontroll (RBAC) är på plats och du behöver att begränsa användare åtkomst till samma prenumeration som är värd för Azure SQL-hanterade databasinstans.
- Vnet som används för Azure SQL-hanterade databasinstans och tjänsten Azure Database migrering finns i olika prenumerationer.

![Nätverkstopologi för hanterade instansen isoleras från det lokala nätverket](media\resource-network-topologies\mi-isolated-workload.png)

**Krav**
- Det VNET som Azure-databas migreringstjänsten använder för det här scenariot måste också vara ansluten till det lokala nätverket genom att använda antingen (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Ställ in [VNET nätverk peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mellan VNET som används för hanteras Azure SQL Database-instans och tjänsten Azure Database migrering.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Moln-to-cloud-migrering: delade virtuella nätverk

Använd den här topologin om källan SQL Server är värd för en virtuell dator i Azure och delar samma virtuella nätverk med hanteras Azure SQL Database-instans och tjänsten Azure Database migrering.

![Nätverkstopologi för moln-to-Cloud-migreringar med en delad vnet](media\resource-network-topologies\cloud-to-cloud.png)

**Krav**
- Inga ytterligare krav.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Molnet till molnet migreringar: isolerade virtuella nätverk

Använd den här nätverkstopologi om miljön kräver en eller flera av följande scenarier:
- Azure SQL-hanterade databasinstans etableras i ett isolerat virtuellt nätverk.
- Om principer för rollbaserad åtkomstkontroll (RBAC) är på plats och du behöver att begränsa användare åtkomst till samma prenumeration som är värd för Azure SQL-hanterade databasinstans.
- Vnet som används för hanteras Azure SQL Database-instans och tjänsten Azure Database migrering finns i olika prenumerationer.

![Nätverkstopologi för moln-to-Cloud-migreringar med ett isolerat virtuellt nätverk](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Krav**
- Ställ in [VNET nätverk peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mellan VNET som används för hanteras Azure SQL Database-instans och tjänsten Azure Database migrering.


## <a name="see-also"></a>Se även
- [Migrera SQLServer till Azure SQL-hanterade databasinstans](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Översikt över krav för att använda tjänsten Azure Database migrering](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Skapa ett virtuellt nätverk med Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Nästa steg
En översikt över Azure databastjänst för migrering och regional tillgänglighet under förhandsversion, finns i artikeln [vad är Azure databas migrering Service Preview](dms-overview.md). 