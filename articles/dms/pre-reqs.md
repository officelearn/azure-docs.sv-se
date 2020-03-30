---
title: Förutsättningar för Azure Database Migration Service
description: Lär dig mer om en översikt över förutsättningarna för att använda Azure Database Migration Service för att utföra databasmigreringar.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 89cb63630e3dbe953ed3f4fd8796d01ba0d36067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651499"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Översikt över förutsättningar för att använda Azure Database Migration Service

Det finns flera förutsättningar som krävs för att säkerställa att Azure Database Migration Service fungerar smidigt när du utför databasmigreringar. Vissa av förutsättningarna gäller i alla scenarier (källmålpar) som stöds av tjänsten, medan andra förutsättningar är unika för ett visst scenario.

Förutsättningar som är associerade med att använda Migreringstjänsten för Azure Database visas i följande avsnitt.

## <a name="prerequisites-common-across-migration-scenarios"></a>Förutsättningar som är vanliga i migreringsscenarier

Azure Database Migration Service förutsättningar som är vanliga i alla scenarier för migrering som stöds inkluderar behovet av att:

* Skapa en Microsoft Azure Virtual Network for Azure Database Migration Service med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Kontrollera att NSG-reglerna (Virtual Network Network Security Group) inte blockerar följande kommunikationsportar 443, 53, 9354, 445, 12000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* När du använder en brandväggsinstallation framför källdatabasen eller källdatabaserna kan du behöva lägga till brandväggsregler så att Azure Database Migration Service kan komma åt källdatabaserna för migrering.
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aktivera TCP/IP-protokollet, som är inaktiverat som standard under SQL Server Express-installation, genom att följa instruktionerna i artikeln om att [aktivera eller inaktivera ett servernätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Skapa en instans av Azure Database Migration Service kräver åtkomst till virtuella nätverksinställningar som normalt inte finns inom samma resursgrupp. Därför kräver användaren som skapar en instans av DMS behörighet på prenumerationsnivå. Om du vill skapa de roller som krävs, som du kan tilldela vid behov, kör du följande skript:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Förutsättningar för att migrera SQL Server till Azure SQL Database

Förutom förutsättningar för Azure Database Migration Service som är gemensamma för alla migreringsscenarier finns det också förutsättningar som gäller specifikt för ett eller annat scenario.

När du använder Azure Database Migration Service för att utföra SQL Server till Azure SQL Database migreringar, utöver de förutsättningar som är gemensamma för alla migreringsscenarier, se till att ta itu med följande ytterligare förutsättningar:

* Skapa en Azure SQL Database-instans, vilket du kan göra genom att följa informationen i artikeln om att [skapa Azure SQL Database i Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) version 3.3 eller senare.
* Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
* Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
* Skapa en [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure SQL Database-servern för att tillåta åtkomst för Azure Database Migration Service till måldatabaserna. Ange undernätsområdet för det virtuella nätverk som används för Azure Database Migration Service.
* Kontrollera att autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen har [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)-behörigheter.
* Kontrollera att autentiseringsuppgifterna som används för att ansluta Azure SQL Database-målinstansen har CONTROL DATABASE-behörighet i Azure SQL-måldatabaserna.

   > [!NOTE]
   > En fullständig lista över de förutsättningar som krävs för att använda Azure Database Migration Service för att utföra migreringar från SQL Server till Azure SQL Database finns i självstudien [Migrera SQL Server till Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   >

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Förutsättningar för att migrera SQL Server till en hanterad Azure SQL-databas-hanterad instans

* Skapa en Hanterad Azure SQL-databas-instans genom att följa detaljerna i artikeln [Skapa en Hanterad Azure SQL-databas-instans i Azure-portalen](https://aka.ms/sqldbmi).
* Öppna brandväggarna så att SMB-trafik på port 445 tillåts för IP-adressen för Azure Database Migration Service eller undernätsintervallet.
* Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
* Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
* Se till att inloggningarna som används för att ansluta SQL Server-källan och den hanterade målinstansen är medlemmar av sysadmin-serverrollen.
* Skapa en nätverksresurs som Azure Database Migration Service kan använda till att säkerhetskopiera källdatabasen.
* Se till att tjänstkontot som kör SQL Server-källinstansen har skrivbehörighet på nätverksresursen som du har skapat och att datorkontot för källservern har läs-/skrivåtkomst till samma resurs.
* Anteckna en Windows-användare (och lösenordet) som har fullständig kontrollbehörighet på nätverksresursen som du tidigare har skapat. Azure Database Migration Service personifierar användarens autentiseringsuppgifter för att ladda upp säkerhetskopior till Azure Storage-behållaren för återställning.
* Skapa en blob-behållare och hämta dess SAS URI med hjälp av stegen i artikeln [Hantera Azure Blob Storage-resurser med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Var noga med att markera alla behörigheter (Läs, Skriv, Ta bort, Lista) i principfönstret när du skapar SAS URI.

   > [!NOTE]
   > En fullständig lista över de förutsättningar som krävs för att använda Azure Database Migration Service för att utföra migreringar från SQL Server till Azure SQL Database Managed Instance finns i självstudien [Migrera SQL Server till Azure SQL Database Managed Instance](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Nästa steg

En översikt över Azure Database Migration Service och regional tillgänglighet finns i artikeln [Vad är Migreringstjänsten för Azure Database](dms-overview.md).
