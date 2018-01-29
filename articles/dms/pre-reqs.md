---
title: "Översikt över krav för att använda tjänsten Azure Database migrering | Microsoft Docs"
description: "Lär dig en översikt över krav för att använda tjänsten Azure Database migrering för att utföra migrering av databasen."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Översikt över krav för att använda tjänsten Azure Database migrering
Det finns flera förutsättningar som krävs för att se till att tjänsten Azure Database migrering smidigt körs när du utför en migrering av databasen. Vissa krav tillämpa över alla scenarier (källa mål par) som stöds av tjänsten, medan andra förutsättningar är unika för ett specifikt scenario.

Kraven som är associerad med tjänsten Azure Database migrering finns i följande avsnitt.

## <a name="prerequisites-common-across-migration-scenarios"></a>Krav för vanliga över Migreringsscenarier
Krav för Azure Migreringstjänst för databasen som är gemensamma för alla migreringsscenarier som stöds är behovet av att:
- Skapa ett virtuellt nätverk för tjänsten Azure Database migrering med hjälp av Azure Resource Manager distributionsmodell, som ger plats-till-plats-anslutning till din lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Se till att din Azure virtuella nätverk (VNET) Nätverkssäkerhetsgruppen regler gör inte blockera följande meddelande portarna 443, 53, 9354, 445, 12000. Mer information om Azure VNET NSG trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- När du använder en brandväggsinstallation framför käll-databaserna, kanske du måste lägga till brandväggsregler som tillåter tjänsten Azure Database migrering till databaserna källa för migrering.

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Krav för att migrera SQL-servern till Azure SQL Database 
Förutom Azure migrering databastjänsten krav som är gemensamma för alla Migreringsscenarier finns också krav som gäller specifikt för ett scenario eller någon annan.

När du använder tjänsten Azure Database migrering för att utföra SQL Server till Azure SQL Database-migreringar, utöver de krav som är gemensamma för alla Migreringsscenarier måste du kontrollera att åtgärda följande ytterligare krav:
- Konfigurera din [Windows-brandväggen för databasmotoråtkomst](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aktiverar du TCP/IP-protokollet är inaktiverat som standard under installationen av SQL Server Express av följa anvisningarna i artikeln [aktivera eller inaktivera nätverksprotokoll Server](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Skapa en instans av Azure SQL Database-instans som du kan göra med följande information i artikeln C[kapa en Azure SQL database i Azure portal](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal).
- Hämta och installera den [Data Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=53595) v3.3 eller senare.
- Öppna Windows-brandväggen så att tjänsten Azure Database migrering till käll-databaserna.
- Skapa en servernivå [brandväggsregel](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) för Azure SQL Database-server för att ge åtkomst till måldatabaserna Azure databastjänst för migrering. Ange undernätets intervall för det VNET som används för tjänsten Azure Database migrering.
- Se till att de autentiseringsuppgifter som används för att ansluta till datakällan SQL Server-instansen har [KONTROLLSERVERN](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql) behörigheter.
- Se till att de autentiseringsuppgifter som används för att ansluta till Azure SQL Database målinstansen CONTROL DATABASE-behörighet på mål Azure SQL-databaser.

   > [!NOTE]
   > En fullständig lista över kraven för att använda tjänsten Azure Database migrering för att utföra migrering från SQL Server till Azure SQL Database finns i självstudiekursen [migrera SQL Server till Azure SQL Database](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="next-steps"></a>Nästa steg
En översikt över Azure databastjänst för migrering och regional tillgänglighet under förhandsversion, finns i artikeln [vad är Azure databas migrering Service Preview](dms-overview.md). 