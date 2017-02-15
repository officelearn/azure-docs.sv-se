---
title: "PowerShell: Konfigurera Azure SQL Database-brandväggsregler | Microsoft Docs"
description: "Lär dig hur du konfigurerar brandväggsregler på servernivå för IP-adresser som har åtkomst till Azure SQL-databaser med hjälp av PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 30dcea72-61c1-48b6-8e1d-b1db2eb61567
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 86bc7d89bb5725add8ba05b6f0978467147fd3ca
ms.openlocfilehash: d80bd1fbb5cdb0492e521a4d600f657fac0e3325


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Översikt](sql-database-firewall-configure.md)
> * [Azure Portal](sql-database-configure-firewall-settings.md)
> * [TSQL](sql-database-configure-firewall-settings-tsql.md)
> * [PowerShell](sql-database-configure-firewall-settings-powershell.md)
> * [REST-API](sql-database-configure-firewall-settings-rest.md)
> 
> 

Azure SQL Database använder brandväggsregler för att tillåta anslutningar till dina servrar och databaser. Du kan definiera brandväggsinställningar på servernivå och databasnivå för huvuddatabasen eller en användardatabas på din SQL Database-server för att selektivt tillåta åtkomst till databasen.

> [!IMPORTANT]
> Om du vill tillåta att program från Azure ansluter till din databasserver måste Azure-anslutningar vara aktiverade. Mer information om brandväggsregler och hur du aktiverar anslutningar från Azure finns i avsnittet om [Azure SQL Database-brandväggen](sql-database-firewall-configure.md). Om du skapar anslutningar inom gränsen för Azure-molnet kan du behöva öppna ytterligare TCP-portar. Mer information finns i avsnittet ”V12 of SQL Database: Outside vs inside” (V12 av SQL Database: utanför eller inuti) i [Ports beyond 1433 for ADO.NET 4.5 and SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md) (Portar utöver 1433 för ADO.NET 4.5 och SQL Database V12).
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Skapa brandväggsregler för servern
Du kan skapa, uppdatera och ta bort brandväggsregler på servernivå med hjälp av Azure PowerShell.

Om du vill skapa en ny brandväggsregel på servernivå kör du cmdleten [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). I följande exempel aktiveras ett intervall med IP-adresser på Contoso-servern.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'        

Om du vill ändra en brandväggsregel på servernivå kör du cmdleten [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx). I följande exempel ändras intervallet med godkända IP-adresser för regeln med namnet ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -StartIPAddress 192.168.1.4 -EndIPAddress 192.168.1.10 -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'

Om du vill ta bort en brandväggsregel på servernivå kör du cmdleten [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx). I följande exempel tas regeln med namnet ContosoFirewallRule bort.

    Remove-AzureRmSqlServerFirewallRule -FirewallRuleName 'ContosoFirewallRule' -ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Hantera brandväggsregler med hjälp av PowerShell
Du kan också använda PowerShell för att hantera brandväggsregler. Mer information finns i följande avsnitt:

* [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx)
* [Remove-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603588\(v=azure.300\).aspx)
* [Set-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603789\(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603586\(v=azure.300\).aspx)

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar brandväggsregler på servernivå och databasnivå med hjälp av Transact-SQL finns i [Konfigurera Azure SQL Database-brandväggsregler på servernivå och databasnivå med hjälp av T-SQL](sql-database-configure-firewall-settings-tsql.md).

Information om hur du skapar brandväggsregler på servernivå med hjälp av andra metoder finns här:

* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av Azure Portal](sql-database-configure-firewall-settings.md)
* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av REST-API:et](sql-database-configure-firewall-settings-rest.md)

En självstudiekurs om hur du skapar en databas finns i [Skapa en SQL-databas på bara några minuter med hjälp av Azure Portal](sql-database-get-started.md).
Hjälp med att ansluta till en Azure SQL-databas från öppen källkod eller program från tredje part finns i [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Snabbstart med kodexempel för att ansluta klienter till SQL Database).
Information om hur du navigerar till databaser finns i [Manage database access and login security](https://msdn.microsoft.com/library/azure/ee336235.aspx) (Hantera databasåtkomst och inloggningssäkerhet).

## <a name="additional-resources"></a>Ytterligare resurser
* [Skydda en databas](sql-database-security-overview.md)
* [Security Center för SQL Server Database Engine och Azure SQL Database](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->



<!--HONumber=Jan17_HO1-->


