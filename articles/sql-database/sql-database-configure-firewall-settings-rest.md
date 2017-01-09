---
title: "Azure SQL Database-brandväggsregler på servernivå med hjälp av REST-API:et | Microsoft Docs"
description: "Lär dig hur du konfigurerar brandväggen för IP-adresser som har åtkomst till Azure SQL-databaser."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: fc874f3c-c623-4924-8cb7-32a8c31e666c
ms.service: sql-database
ms.custom: authentication and authorization
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: cc0faa49daaafe19c71d2c765b8e865be04f81e2


---
# <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av REST-API:et
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

## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Hantera brandväggsregler på servernivå via REST-API:et
1. Hanteringen av brandväggsregler via REST-API:et måste autentiseras. Mer information finns i [utvecklarguiden för auktorisering med Azure Resource Manager-API:et](../azure-resource-manager/resource-manager-api-authentication.md).
2. Du kan skapa, uppdatera eller ta bort regler på servernivå med hjälp av REST-API:et
   
    Om du vill skapa eller uppdatera en brandväggsregel på servernivå kör du PUT-metoden med hjälp av följande:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
   
    Begärandetext
   
        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 

    Om du vill ta bort en befintlig brandväggsregel på servernivå kör du DELETE-metoden med hjälp av följande:

        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Hantera brandväggsregler med hjälp av REST-API:et
* [Skapa eller uppdatera en brandväggsregel](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Ta bort en brandväggsregel](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Hämta en brandväggsregel](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Visa en lista över alla brandväggsregler](https://msdn.microsoft.com/library/azure/mt604478.aspx)

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar brandväggsregler på servernivå och databasnivå med hjälp av Transact-SQL finns i [Konfigurera Azure SQL Database-brandväggsregler på servernivå och databasnivå med hjälp av T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Artiklar om hur du skapar brandväggsregler på servernivå med andra metoder finns här: 

* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av Azure Portal](sql-database-configure-firewall-settings.md)
* [Konfigurera Azure SQL Database-brandväggsregler på servernivå med hjälp av PowerShell](sql-database-configure-firewall-settings-powershell.md)

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


