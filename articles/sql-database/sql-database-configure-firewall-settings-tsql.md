---
title: "T-SQL: Azure SQL Database-brandväggsregler | Microsoft Docs"
description: "Lär dig hur du konfigurerar brandväggsregler på servernivå och databasnivå för IP-adresser som har åtkomst till Azure SQL-databaser med hjälp av Transact-SQL."
services: sql-database
documentationcenter: 
author: BYHAM
manager: jhubbard
editor: 
ms.assetid: 71e692a1-5e2f-4a18-a6d6-527b849cf68e
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: rickbyh
translationtype: Human Translation
ms.sourcegitcommit: 2f24c1c43300a3c5035fbb4e90c3d13f73c1bc74
ms.openlocfilehash: ef51f63984d342422ee5f2a93acfee6357df57ca


---
# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Konfigurera Azure SQL Database-brandväggsregler på servernivå och databasnivå med hjälp av T-SQL
> [!div class="op_single_selector"]
> * [Översikt](sql-database-firewall-configure.md)
> * [Azure Portal](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST-API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Microsoft Azure SQL Database använder brandväggsregler för att tillåta anslutningar till dina servrar och databaser. Du kan definiera brandväggsinställningar på servernivå och databasnivå för huvuddatabasen eller en användardatabas på din Azure SQL Database-server för att selektivt tillåta åtkomst till databasen.

> [!IMPORTANT]
> Om du vill tillåta att program från Azure ansluter till din databasserver måste Azure-anslutningar vara aktiverade. Mer information om brandväggsregler och hur du aktiverar anslutningar från Azure finns i avsnittet om [Azure SQL Database-brandväggen](sql-database-firewall-configure.md). Om du skapar anslutningar inom gränsen för Azure-molnet kan du behöva öppna ytterligare TCP-portar. Mer information finns i avsnittet **V12 av SQL Database: Outside vs inside** (V12 av SQL Database: utanför eller inuti) i [Ports beyond 1433 for ADO.NET 4.5 and SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md) (Portar utöver 1433 för ADO.NET 4.5 och SQL Database V12)
> 
> 

## <a name="server-level-firewall-rules"></a>Brandväggsregler på servernivå
Endast huvudkontoinloggningen på servernivå eller Azure Active Directory-administratören kan skapa en brandväggsregel på servernivå med hjälp av Transact-SQL.

1. Öppna ett frågefönster och anslut till den virtuella huvuddatabasen med hjälp av SQL Server Management Studio.
2. Du kan välja, skapa, uppdatera eller ta bort brandväggsregler på servernivå från frågefönstret.
3. Om du vill skapa eller uppdatera brandväggsregler på servernivå kör du den lagrade proceduren `sp_set_firewall_rule`. I följande exempel aktiveras ett intervall med IP-adresser på Contoso-servern.<br/>Börja med att se efter vilka regler som redan finns.
   
        SELECT * FROM sys.firewall_rules ORDER BY name;
   
    Lägg sedan till en brandväggsregel.
   
        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
   
    Kör den lagrade proceduren sp_delete_firewall_rule om du vill ta bort en brandväggsregel på servernivå. I följande exempel tas regeln med namnet ContosoFirewallRule bort.
   
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
   
   Mer information om dessa lagrade procedurer finns i [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) och [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Brandväggsregler på databasnivå
Endast en databasanvändare med behörigheten **KONTROLL** för databasen (till exempel databasägaren) kan skapa en brandväggsregel på databasnivå.

1. När du har skapat en brandvägg på servernivå för din IP-adress startar du ett frågefönster via den klassiska portalen eller SQL Server Management Studio.
2. Anslut till den databas som du vill skapa en brandväggsregel på databasnivå för.
   
    Om du vill skapa en ny eller uppdatera en befintlig brandväggsregel på databasnivå kör du den lagrade proceduren `sp_set_database_firewall_rule`. I följande exempel skapas en ny brandväggsregel med namnet ContosoFirewallRule.
   
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
   
    Om du vill ta bort en befintlig brandväggsregel på databasnivå kör du den lagrade proceduren `sp_delete_database_firewall_rule`. I följande exempel tas regeln med namnet ContosoFirewallRule bort.
   `
   
        EXEC sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Mer information om dessa lagrade procedurer finns i [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) och [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

> [!NOTE]
> En självstudiekurs som beskriver hur du använder brandväggar på databasnivå finns här: [SQL Database tutorial: SQL Server authentication, logins and user accounts, database roles, permissions, server-level firewall rules, and database-level firewall rules](sql-database-control-access-sql-authentication-get-started.md) (Själstudiekurs om SQL Database: SQL Server-autentisering, inloggningar och användarkonton, databasroller, behörigheter, brandväggsregler på servernivå och brandväggsregler på databasnivå).
>


## <a name="next-steps"></a>Nästa steg
Artiklar om hur du skapar brandväggsregler på servernivå med andra metoder finns här: 

* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av Azure Portal](sql-database-configure-firewall-settings.md)
* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av PowerShell](sql-database-configure-firewall-settings-powershell.md)
* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av REST-API:et](sql-database-configure-firewall-settings-rest.md)

En självstudiekurs om hur du skapar en databas finns i [Skapa en SQL-databas på bara några minuter med hjälp av Azure Portal](sql-database-get-started.md).
Hjälp med att ansluta till en Azure SQL-databas från öppen källkod eller program från tredje part finns i [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Snabbstart med kodexempel för att ansluta klienter till SQL Database).
Information om hur du navigerar till databaser finns i [Manage database access and login security](https://msdn.microsoft.com/library/azure/ee336235.aspx) (Hantera databasåtkomst och inloggningssäkerhet).

## <a name="additional-resources"></a>Ytterligare resurser
* [Skydda en databas](sql-database-security-overview.md)
* [Security Center för SQL Server Database Engine och Azure SQL Database](https://msdn.microsoft.com/library/bb510589)




<!--HONumber=Jan17_HO3-->


