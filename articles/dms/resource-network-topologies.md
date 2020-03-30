---
title: Nätverkstopologier för SQL-hanterade instansmigreringar
titleSuffix: Azure Database Migration Service
description: Lär dig käll- och målkonfigurationerna för Azure SQL Database-hanterade instansmigreringar med hjälp av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254962"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Nätverkstopologier för Azure SQL DB-migreringar för hanterade instanser med hjälp av Azure Database Migration Service

I den här artikeln beskrivs olika nätverkstopologier som Azure Database Migration Service kan arbeta med för att ge en omfattande migreringsupplevelse från lokala SQL-servrar till Azure SQL Database Managed Instance.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Hanterad Azure SQL-databas-instans som konfigurerats för hybridarbetsbelastningar 

Använd den här topologin om din Azure SQL Database Managed Instance är ansluten till ditt lokala nätverk. Den här metoden ger den mest förenklade nätverksdirigeringen och ger maximalt dataflöde under migreringen.

![Nätverkstopologi för hybridarbetsbelastningar](media/resource-network-topologies/hybrid-workloads.png)

**Krav**

- I det här fallet azure SQL Database hanterad instans och Azure Database Migration Service-instansen skapas i samma Microsoft Azure Virtual Network, men de använder olika undernät.  
- Det virtuella nätverket som används i det här scenariot är också anslutet till det lokala nätverket med hjälp av antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Hanterad azure SQL-databas-instans isolerad från det lokala nätverket

Använd den här nätverkstopologin om din miljö kräver ett eller flera av följande scenarier:

- Den hanterade instansen för Azure SQL-databas är isolerad från lokal anslutning, men din Azure Database Migration Service-instans är ansluten till det lokala nätverket.
- Om RBAC-principer (Role Based Access Control) finns på plats och du måste begränsa användarna till att komma åt samma prenumeration som är värd för den hanterade Instansen för Azure SQL Database.
- De virtuella nätverk som används för Azure SQL Database Managed Instance och Azure Database Migration Service finns i olika prenumerationer.

![Nätverkstopologi för hanterad instans isolerad från det lokala nätverket](media/resource-network-topologies/mi-isolated-workload.png)

**Krav**

- Det virtuella nätverk som Azure Database Migration Service använder för det här scenariothttps://docs.microsoft.com/azure/expressroute/expressroute-introduction) måste också vara anslutet till det lokala nätverket med antingen ( eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Konfigurera [VNet-nätverks peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mellan det virtuella nätverket som används för Azure SQL Database-hanterad instans och Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Moln-till-moln-migreringar: Delat virtuellt nätverk

Använd den här topologin om källan SQL Server finns i en Virtuell Azure-dator och delar samma virtuella nätverk med Azure SQL Database-hanterad instans och Azure Database Migration Service.

![Nätverkstopologi för moln-till-moln-migreringar med ett delat virtuella nätverk](media/resource-network-topologies/cloud-to-cloud.png)

**Krav**

- Inga ytterligare krav.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Moln-till-molnmigreringar: Isolerat virtuellt nätverk

Använd den här nätverkstopologin om din miljö kräver ett eller flera av följande scenarier:

- Den hanterade instansen för Azure SQL Database etableras i ett isolerat virtuellt nätverk.
- Om RBAC-principer (Role Based Access Control) finns på plats och du måste begränsa användarna till att komma åt samma prenumeration som är värd för den hanterade Instansen för Azure SQL Database.
- De virtuella nätverk som används för Azure SQL Database Managed Instance och Azure Database Migration Service finns i olika prenumerationer.

![Nätverkstopologi för moln-till-moln-migreringar med ett isolerat virtuella nätverk](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Krav**

- Konfigurera [VNet-nätverks peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mellan det virtuella nätverket som används för Azure SQL Database-hanterad instans och Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Ingående säkerhetsregler

| **NAMN**   | **Port** | **Protokollet** | **Källkod** | **Destination** | **Åtgärder** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Alla      | Alla          | DMS-UNDERNÄT | Alla             | Tillåt      |

## <a name="outbound-security-rules"></a>Säkerhetsregler för utgående trafik

| **NAMN**                  | **Port**                                              | **Protokollet** | **Källkod** | **Destination**           | **Åtgärder** | **Orsak till regel**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management                | 443,9354                                              | TCP          | Alla        | Alla                       | Tillåt      | Hanteringsplan kommunikation via Service Bus och Azure blob lagring. <br/>(Om Microsoft-peering är aktiverat kanske du inte behöver den här regeln.)                                                             |
| Diagnostik               | 12000                                                 | TCP          | Alla        | Alla                       | Tillåt      | DMS använder den här regeln för att samla in diagnostikinformation för felsökning.                                                                                                                      |
| SQL-källserver         | 1433 (eller TCP IP-port som SQL Server lyssnar på) | TCP          | Alla        | Lokalt adressutrymme | Tillåt      | Anslutning till SQL Server-källa från DMS <br/>(Om du har anslutning från plats till plats kanske du inte behöver den här regeln.)                                                                                       |
| SQL Server namngiven instans | 1434                                                  | UDP          | Alla        | Lokalt adressutrymme | Tillåt      | SQL Server namngiven instanskälla anslutning från DMS <br/>(Om du har anslutning från plats till plats kanske du inte behöver den här regeln.)                                                                        |
| SMB-aktie                 | 445                                                   | TCP          | Alla        | Lokalt adressutrymme | Tillåt      | SMB-nätverksresurs för DMS för att lagra databassäkerhetskopieringsfiler för migreringar till Azure SQL Database MI och SQL-servrar på Azure VM <br/>(Om du har anslutning från plats till plats kanske du inte behöver den här regeln). |
| DMS_subnet                | Alla                                                   | Alla          | Alla        | DMS_Subnet                | Tillåt      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Se även

- [Migrera SQL Server till Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Översikt över förutsättningar för att använda Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Skapa ett virtuellt nätverk med Azure-portalen](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Nästa steg

- En översikt över Migreringstjänsten för Azure Database finns i artikeln [Vad är Azure Database Migration Service?](dms-overview.md).
- Information om regional tillgänglighet för Azure Database Migration Service finns på sidan [Produkter som är tillgängliga efter region.](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)
