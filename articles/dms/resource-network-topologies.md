---
title: Nätverkstopologier för Azure SQL Database Managed Instance-migrering med hjälp av Azure Database Migration Service | Microsoft Docs
description: Läs om käll- och konfigurationer för Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: d12d6b1274a756bfb13761ab999a1539bcee3657
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61097844"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Nätverkstopologier för Azure SQL DB Managed Instance-migrering med hjälp av Azure Database Migration Service
Den här artikeln beskrivs olika nätverkstopologier som Azure Database Migration Service kan arbeta med för att tillhandahålla en omfattande migreringen från en lokal SQL-servrar till Azure SQL Database Managed Instance.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database Managed Instance konfigurerats för hybridarbetsbelastningar 
Använd den här topologin om din Azure SQL Database Managed Instance är ansluten till ditt lokala nätverk. Den här metoden ger den mest förenklad nätverksroutning och återger maximalt datagenomflöde under migreringen.

![Nätverkstopologi för Hybridarbetsbelastningar](media/resource-network-topologies/hybrid-workloads.png)

**Krav**
- Azure SQL Database Managed Instance och Azure Database Migration Service-instans skapas i samma Azure virtuella nätverk i det här scenariot, men de använder olika undernät.  
- Det virtuella nätverket som används i det här scenariot är också ansluten till det lokala nätverket genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database Managed Instance isolerade från det lokala nätverket
Använd den här nätverkstopologi om miljön kräver en eller flera av följande scenarier:
- Azure SQL Database Managed Instance är isolerad från lokal anslutning, men din Azure Database Migration Service-instans som är ansluten till det lokala nätverket.
- Om principer för rollbaserad åtkomstkontroll (RBAC) är på plats och du behöver att begränsa användarnas åtkomst till samma prenumeration som är värd för Azure SQL Database Managed Instance.
- De virtuella nätverken för Azure SQL Database Managed Instance och Azure Database Migration Service finns i olika prenumerationer.

![Nätverkstopologi för hanterad instans isolerad från det lokala nätverket](media/resource-network-topologies/mi-isolated-workload.png)

**Krav**
- Det virtuella nätverket som Azure Database Migration Service använder för det här scenariot måste också vara anslutna till det lokala nätverket med hjälp av antingen (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Konfigurera [VNET nätverkspeering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mellan det virtuella nätverket som används för Azure SQL Database Managed Instance och Azure Database Migration Service.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Moln-till-moln-migrering: delade virtuella nätverk

Använd den här topologin om SQL Server-källans är värd för en Azure-dator och delar samma virtuella nätverk med Azure SQL Database Managed Instance och Azure Database Migration Service.

![Nätverkstopologi för moln-till-moln-migrering med delade virtuella nätverk](media/resource-network-topologies/cloud-to-cloud.png)

**Krav**
- Inga ytterligare krav.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Molnet för att molnmigreringar: isolerat virtuellt nätverk

Använd den här nätverkstopologi om miljön kräver en eller flera av följande scenarier:
- Azure SQL Database Managed Instance har etablerats i ett isolerat virtuellt nätverk.
- Om principer för rollbaserad åtkomstkontroll (RBAC) är på plats och du behöver att begränsa användarnas åtkomst till samma prenumeration som är värd för Azure SQL Database Managed Instance.
- De virtuella nätverken som används för Azure SQL Database Managed Instance och Azure Database Migration Service finns i olika prenumerationer.

![Nätverkstopologi för moln-till-moln-migrering med ett isolerat virtuellt nätverk](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Krav**
- Konfigurera [VNET nätverkspeering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mellan det virtuella nätverket som används för Azure SQL Database Managed Instance och Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Ingående säkerhetsregler

| **NAME**   | **PORT** | **PROTOCOL** | **KÄLLA** | **DESTINATION** | **ÅTGÄRD** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Alla      | Alla          | DMS-UNDERNÄT | Alla             | Tillåt      |

## <a name="outbound-security-rules"></a>Utgående säkerhetsregler

| **NAME**                  | **PORT**                                              | **PROTOCOL** | **KÄLLA** | **DESTINATION**           | **ÅTGÄRD** | **Orsak för regeln**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| hantering                | 443,9354                                              | TCP          | Alla        | Alla                       | Tillåt      | Hantering av plan kommunikation via service bus och Azure blob storage. <br/>(Om Microsoft-peering är aktiverad, du kanske inte behöver den här regeln.)                                                             |
| Diagnostik               | 12000                                                 | TCP          | Alla        | Alla                       | Tillåt      | DMS använder den här regeln för att samla in diagnostisk information för felsökning.                                                                                                                      |
| SQL-källservern         | 1433 (eller TCP IP-porten som SQL Server lyssnar på) | TCP          | Alla        | Lokalt adressutrymme | Tillåt      | SQL Server källa anslutningen från DMS <br/>(Om du har plats-till-plats-anslutning kan du kanske inte behöver den här regeln.)                                                                                       |
| SQL Server-namngivna instanser | 1434                                                  | UDP          | Alla        | Lokalt adressutrymme | Tillåt      | Namngivna SQL Server-instansen källa anslutningen från DMS <br/>(Om du har plats-till-plats-anslutning kan du kanske inte behöver den här regeln.)                                                                        |
| SMB-resurs                 | 445                                                   | TCP          | Alla        | Lokalt adressutrymme | Tillåt      | SMB-nätverksresurs för att DMS ska lagra säkerhetskopierade databasfilerna för migrering till Azure SQL Database MI och SQL-servrar på Azure VM <br/>(Om du har plats-till-plats-anslutning kan du kanske inte behöver den här regeln). |
| DMS_subnet                | Alla                                                   | Alla          | Alla        | DMS_Subnet                | Tillåt      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Se även
- [Migrera SQLServer till Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Översikt över förutsättningar för att använda Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Skapa ett virtuellt nätverk med Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Nästa steg
- En översikt över Azure Database Migration Service, finns i artikeln [vad är Azure Database Migration Service?](dms-overview.md).
- Aktuell information om regional tillgänglighet för Azure Database Migration Service finns i den [produkttillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) sidan.