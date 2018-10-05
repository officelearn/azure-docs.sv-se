---
title: Översikt över krav för att använda Azure Database Migration Service | Microsoft Docs
description: Lär dig en översikt över kraven för att använda Azure Database Migration Service för att utföra migrering av databaser.
services: database-migration
author: HJToland3
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: c90727db28bde0a1985104f1fbc9c7380bba8dee
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802941"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Översikt över krav för att använda Azure Database Migration Service
Det finns flera förutsättningar som krävs för att säkerställa att Azure Database Migration Service körs smidigt när du utför migrering av databaser. Några av förutsättningarna gäller för alla scenarier (källa / mål-par) som stöds av tjänsten, medan andra krav är unika för ett specifikt scenario.

Krav som är associerade med Azure Database Migration Service visas i följande avsnitt.

## <a name="prerequisites-common-across-migration-scenarios"></a>Krav som är vanliga i olika migreringsscenarion för
Krav för Azure Database Migration Service som är gemensamma för alla migreringsscenarier som stöds är behovet av att:
- Skapa virtuellt nätverk för Azure Database Migration Service genom att använda Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning för dina lokala källservrar genom att använda [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Kontrollera att dina regler för nätverkssäkerhetsgrupp (NSG) för Azure Virtual Network (VNET) inte blockerar följande kommunikationsportar: 443, 53, 9354, 445, 12000. Mer information om trafikfiltrering för Azure VNET NSG finns i artikeln om att [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
- Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aktivera TCP/IP-protokollet, som är inaktiverat som standard under SQL Server Express-installation, genom att följa instruktionerna i artikeln om att [aktivera eller inaktivera ett servernätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Förutsättningar för att migrera SQLServer till Azure SQL Database 
Förutom förutsättningar för Azure Database Migration Service som är gemensamma för alla Migreringsscenarier, finns även vissa krav som gäller specifikt för ett scenario eller någon annan.

När du använder Azure Database Migration Service för att utföra SQL Server till Azure SQL Database-migreringar, förutom kraven som är gemensamma för alla Migreringsscenarier måste du kontrollera att åtgärda följande ytterligare krav:

- Skapa en instans av Azure SQL-databasinstans, det gör du genom att följa detaljerat i artikeln C[kapa en Azure SQL database i Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) version 3.3 eller senare.
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
- Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
- Skapa en [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure SQL Database-servern för att tillåta åtkomst för Azure Database Migration Service till måldatabaserna. Ange undernätsintervallet för det virtuella nätverk som används för Azure Database Migration Service.
- Kontrollera att autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen har [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)-behörigheter.
- Kontrollera att autentiseringsuppgifterna som används för att ansluta Azure SQL Database-målinstansen har CONTROL DATABASE-behörighet i Azure SQL-måldatabaserna.

   > [!NOTE]
   > En fullständig lista över kraven för att använda Azure Database Migration Service för att utföra migreringar från SQL Server till Azure SQL Database finns i självstudierna [migrera SQL Server till Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database-managed-instance"></a>Förutsättningar för att migrera SQLServer till Azure SQL Database Managed Instance
- Skapa en instans av Azure SQL Database Managed Instance genom att följa detaljerat i artikeln [skapa en Azure SQL Database Managed Instance i Azure-portalen](https://aka.ms/sqldbmi).
- Öppna dina brandväggar för att tillåta SMB-trafik på port 445 för Azure Database Migration Service IP-adressen eller undernätet intervallet.
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
- Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
- Se till att inloggningarna som används för att ansluta SQL Server-källan och den hanterade målinstansen är medlemmar av sysadmin-serverrollen.
- Skapa en nätverksresurs som Azure Database Migration Service kan använda till att säkerhetskopiera källdatabasen.
- Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
- Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att ladda upp de säkerhetskopierade filerna till Azure Storage-container för återställning.
- Skapa en blob-behållare och hämta dess SAS-URI med hjälp av stegen i artikeln [hantera Azure Blob Storage-resurser med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Var noga med att välja alla behörigheter (läsa, skriva, ta bort, lista) i fönstret princip när du skapar SAS-URI.

   > [!NOTE]
   > En fullständig lista över kraven för att använda Azure Database Migration Service för att utföra migreringar från SQL Server till Azure SQL Database Managed Instance finns i självstudierna [migrera SQL Server till Azure SQL Database Managed Instance ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Nästa steg
En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [vad är Azure Database Migration Service](dms-overview.md). 