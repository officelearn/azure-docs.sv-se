---
title: Nätverkstopologier för migrering av SQL-hanterade instanser
titleSuffix: Azure Database Migration Service
description: Lär dig om käll-och mål konfigurationerna för Azure SQL Database migrering av hanterade instanser med hjälp av Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 9a313ea798519273ce57961544ec5b37c4d9c5ca
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749267"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Nätverkstopologier för Azure SQL DB Managed instance-migreringar med hjälp av Azure Database Migration Service

I den här artikeln beskrivs olika nätverkstopologier som Azure Database Migration Service kan arbeta med för att tillhandahålla en omfattande migrering från lokala SQL-servrar till Azure SQL Database Hanterad instans.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database Hanterad instans som kon figurer ATS för Hybrid arbets belastningar 

Använd den här topologin om din Azure SQL Database hanterade instans är ansluten till ditt lokala nätverk. Den här metoden ger den mest förenklade nätverks dirigeringen och ger högsta data genom strömning under migreringen.

![Nätverkstopologi för Hybrid arbets belastningar](media/resource-network-topologies/hybrid-workloads.png)

**Krav**

- I det här scenariot skapas den Azure SQL Database hanterade instansen och Azure Database Migration Service-instansen i samma Microsoft Azure Virtual Network, men de använder olika undernät.  
- Det virtuella nätverk som används i det här scenariot är också anslutet till det lokala nätverket med hjälp av antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database Hanterad instans isolerad från det lokala nätverket

Använd den här nätverks sto pol Ogin om din miljö kräver ett eller flera av följande scenarier:

- Den Azure SQL Database hanterade instansen är isolerad från lokal anslutning, men Azure Database Migration Service instansen är ansluten till det lokala nätverket.
- Om rollbaserade Access Control-principer (RBAC) är på plats och du behöver begränsa användarna till att komma åt samma prenumeration som är värd för den Azure SQL Database hanterade instansen.
- De virtuella nätverk som används för den Azure SQL Database hanterade instansen och Azure Database Migration Service finns i olika prenumerationer.

![Nätverkstopologi för en hanterad instans som är isolerad från det lokala nätverket](media/resource-network-topologies/mi-isolated-workload.png)

**Krav**

- Det virtuella nätverk som Azure Database Migration Service använder för det här scenariot måste också vara anslutet till det lokala nätverket genom att använda antingen (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Konfigurera [VNet-nätverks-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mellan det virtuella nätverk som används för Azure SQL Database Hanterad instans och Azure Database migration service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrering från moln till moln: delat virtuellt nätverk

Använd den här topologin om käll SQL Server finns på en virtuell Azure-dator och delar samma virtuella nätverk med Azure SQL Database Hanterad instans och Azure Database Migration Service.

![Nätverkstopologi för moln-till-moln-migrering med ett delat VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Krav**

- Inga ytterligare krav.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Moln-till-moln-migreringar: isolerat virtuellt nätverk

Använd den här nätverks sto pol Ogin om din miljö kräver ett eller flera av följande scenarier:

- Den Azure SQL Database hanterade instansen är etablerad i ett isolerat virtuellt nätverk.
- Om rollbaserade Access Control-principer (RBAC) är på plats och du behöver begränsa användarna till att komma åt samma prenumeration som är värd för den Azure SQL Database hanterade instansen.
- De virtuella nätverk som används för Azure SQL Database hanterade instansen och Azure Database Migration Service finns i olika prenumerationer.

![Nätverkstopologi för moln-till-moln-migrering med ett isolerat VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Krav**

- Konfigurera [VNet-nätverks-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mellan det virtuella nätverk som används för Azure SQL Database Hanterad instans och Azure Database migration service.

## <a name="inbound-security-rules"></a>Ingående säkerhetsregler

| **Namn**   | **LASTNING** | **PROTOKOLLHANTERARE** | **KÄLLA** | **MÅL** | **TGÄRD** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Alla      | Alla          | DMS-UNDERNÄT | Alla             | Tillåt      |

## <a name="outbound-security-rules"></a>Utgående säkerhetsregler

| **Namn**                  | **LASTNING**                                              | **PROTOKOLLHANTERARE** | **KÄLLA** | **MÅL**           | **TGÄRD** | **Orsak för regel**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management                | 443, 9354                                              | TCP          | Alla        | Alla                       | Tillåt      | Hanterings Plans kommunikation via Service Bus och Azure Blob Storage. <br/>(Om Microsoft-peering har Aktiver ATS kanske du inte behöver den här regeln.)                                                             |
| Diagnostik               | 12000                                                 | TCP          | Alla        | Alla                       | Tillåt      | DMS använder den här regeln för att samla in diagnostikinformation i fel söknings syfte.                                                                                                                      |
| SQL-webbserver         | 1433 (eller TCP IP-port som SQL Server lyssnar på) | TCP          | Alla        | Lokalt adressutrymme | Tillåt      | SQL Server käll anslutning från DMS <br/>(Om du har plats-till-plats-anslutning kanske du inte behöver den här regeln.)                                                                                       |
| SQL Server namngiven instans | 1434                                                  | UDP          | Alla        | Lokalt adressutrymme | Tillåt      | SQL Server namngiven instans källa anslutning från DMS <br/>(Om du har plats-till-plats-anslutning kanske du inte behöver den här regeln.)                                                                        |
| SMB-resurs                 | 445                                                   | TCP          | Alla        | Lokalt adressutrymme | Tillåt      | SMB-nätverks resurs för DMS för lagring av säkerhetskopierade databasfiler för migrering till Azure SQL Database MI-och SQL-servrar på den virtuella Azure-datorn <br/>(Om du har plats-till-plats-anslutning kanske du inte behöver den här regeln). |
| DMS_subnet                | Alla                                                   | Alla          | Alla        | DMS_Subnet                | Tillåt      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Se också

- [Migrera SQL Server till Azure SQL Database Hanterad instans](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Översikt över krav för att använda Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Skapa ett virtuellt nätverk med Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure Database Migration Service finns i artikeln [Vad är Azure Database migration service?](dms-overview.md).
- Aktuell information om regional tillgänglighet för Azure Database Migration Service finns på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .
