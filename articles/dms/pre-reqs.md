---
title: Översikt över krav för att använda tjänsten Azure Database migrering | Microsoft Docs
description: Lär dig en översikt över krav för att använda tjänsten Azure Database migrering för att utföra migrering av databasen.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: b480ca189b3d63d92c48abf8d9c398c1c9b22241
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Översikt över krav för att använda tjänsten Azure Database migrering
Det finns flera förutsättningar som krävs för att se till att tjänsten Azure Database migrering smidigt körs när du utför en migrering av databasen. Vissa krav tillämpa över alla scenarier (källa mål par) som stöds av tjänsten, medan andra förutsättningar är unika för ett specifikt scenario.

Kraven som är associerad med tjänsten Azure Database migrering finns i följande avsnitt.

## <a name="prerequisites-common-across-migration-scenarios"></a>Krav för vanliga över Migreringsscenarier
Krav för Azure Migreringstjänst för databasen som är gemensamma för alla migreringsscenarier som stöds är behovet av att:
- Skapa ett virtuellt nätverk för tjänsten Azure Database migrering med hjälp av Azure Resource Manager distributionsmodell, som ger plats-till-plats-anslutning till din lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Se till att din Azure virtuella nätverk (VNET) Nätverkssäkerhetsgruppen regler gör inte blockera följande meddelande portarna 443, 53, 9354, 445, 12000. Mer information om Azure VNET NSG trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- När du använder en brandväggsinstallation framför käll-databaserna, kanske du måste lägga till brandväggsregler som tillåter tjänsten Azure Database migrering till databaserna källa för migrering.
- Konfigurera din [Windows-brandväggen för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aktiverar du TCP/IP-protokollet är inaktiverat som standard under installationen av SQL Server Express av följa anvisningarna i artikeln [aktivera eller inaktivera nätverksprotokoll Server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Krav för att migrera SQL-servern till Azure SQL Database 
Förutom Azure migrering databastjänsten krav som är gemensamma för alla Migreringsscenarier finns också krav som gäller specifikt för ett scenario eller någon annan.

När du använder tjänsten Azure Database migrering för att utföra SQL Server till Azure SQL Database-migreringar, utöver de krav som är gemensamma för alla Migreringsscenarier måste du kontrollera att åtgärda följande ytterligare krav:

- Skapa en instans av Azure SQL Database-instans som du kan göra med följande information i artikeln C[kapa en Azure SQL database i Azure portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Hämta och installera den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 eller senare.
- Öppna Windows-brandväggen så att tjänsten Azure databas migrering att få åtkomst till källan SQL Server som är TCP-port 1433 som standard.
- Om du använder flera namngivna SQL Server-instanser som använder dynamiska portar, kan du aktivera tjänsten SQL Browser och ge åtkomst till UDP-port 1434 genom dina brandväggar så att tjänsten Azure Database migrering kan ansluta till en namngiven instans på datakällan Server.
- Skapa en servernivå [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för Azure SQL Database-server för att ge åtkomst till måldatabaserna Azure databastjänst för migrering. Ange undernätets intervall för det VNET som används för tjänsten Azure Database migrering.
- Se till att de autentiseringsuppgifter som används för att ansluta till datakällan SQL Server-instansen har [KONTROLLSERVERN](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) behörigheter.
- Se till att de autentiseringsuppgifter som används för att ansluta till Azure SQL Database målinstansen CONTROL DATABASE-behörighet på mål Azure SQL-databaser.

   > [!NOTE]
   > En fullständig lista över kraven för att använda tjänsten Azure Database migrering för att utföra migrering från SQL Server till Azure SQL Database finns i självstudiekursen [migrera SQL Server till Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>Krav för att migrera SQL-servern till hanterade Azure SQL Database-instans
- Skapa en instans av hanterade Azure SQL Database-instans genom att följa detaljerat i artikeln [skapa en Azure SQL Database hanteras instans i Azure portal](https://aka.ms/sqldbmi).
- Öppna dina brandväggar för att tillåta SMB-trafik på port 445 Azure databas migrering Service IP-adressen eller undernätet adressintervallet.
- Öppna Windows-brandväggen så att tjänsten Azure databas migrering att få åtkomst till källan SQL Server som är TCP-port 1433 som standard.
- Om du använder flera namngivna SQL Server-instanser som använder dynamiska portar, kan du aktivera tjänsten SQL Browser och ge åtkomst till UDP-port 1434 genom dina brandväggar så att tjänsten Azure Database migrering kan ansluta till en namngiven instans på datakällan Server.
- Kontrollera att de inloggningar som används för att ansluta källan SQL Server och hanterade målinstans är medlemmar i serverrollen sysadmin.
- Skapa en nätverksresurs som tjänsten Azure Database migrering kan använda för att säkerhetskopiera källdatabasen.
- Kontrollera att tjänstkontot som kör SQL Server-instansen källa har skrivbehörighet för den nätverksresurs som du skapade.
- Anteckna en Windows-användare (och lösenord) som har behörigheten Fullständig behörighet för den nätverksresurs som du skapade ovan. Tjänsten Azure Database migrering personifierar användarens inloggningsuppgifter för att ladda upp säkerhetskopian till Azure storage-behållare för återställningen.
- Skapa en blob-behållare och hämta dess SAS-URI med hjälp av stegen i artikeln [hantera Azure Blob Storage-resurser med Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Se till att välja alla behörigheter (läsa, skriva, ta bort, lista) i fönstret princip när du skapar SAS-URI.

   > [!NOTE]
   > En fullständig lista över kraven för att använda tjänsten Azure Database migrering för att utföra migrering från SQL Server till hanterade Azure SQL Database-instans finns i självstudiekursen [migrera SQL Server till hanterade Azure SQL Database-instans ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Nästa steg
En översikt över Azure databastjänst för migrering och regional tillgänglighet under förhandsversion, finns i artikeln [vad är Azure databas migrering Service Preview](dms-overview.md). 