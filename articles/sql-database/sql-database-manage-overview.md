---
title: "Översikt: hanteringsverktyg för SQL Database | Microsoft Docs"
description: "Jämför verktyg och alternativ för hantering av Azure SQL Database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 37767380-975f-4dee-a28d-80bc2036dda3
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 73cc9d1d02ed915466f0eac2d9a42fe8cf9e2fb9
ms.openlocfilehash: 32d0a1fd8671bbd9d450711dd686ca49c2178c16


---
# <a name="overview-management-tools-for-sql-database"></a>Översikt: hanteringsverktyg för SQL Database
Det här avsnittet utforskar och jämför verktyg och alternativ för hantering av Azure SQL-databaser så att du kan välja rätt verktyg för jobbet, ditt företag och dig själv. Valet av rätt verktyg beror på hur många databaser du hanterar, aktiviteten och hur ofta en aktivitet utförs.

## <a name="azure-portal"></a>Azure Portal
[Azure Portal](https://portal.azure.com) är ett webbaserat program där du kan skapa, uppdatera och ta bort databaser och logiska servrar och övervaka databasaktivitet. Verktyget är bra om du precis har börjat med Azure, hanterar ett par databaser eller snabbt behöver göra något.

Mer information om hur du använder portalen finns i [Hantera SQL Database med hjälp av Azure Portal](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio och SQL Server Data Tools i Visual Studio
SQL Server Management Studio (SSMS) och SQL Server Data Tools (SSDT) är klientverktyg som körs på datorn för att hantera och utveckla din databas i molnet. Om du är programutvecklare och bekant med Visual Studio eller annan Integrated Development Environment (IDE:s), [försök använda SSDT i Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Många databasadministratörer är bekanta med SSMS, som kan användas med Azure SQL-databaser. [Hämta den senaste versionen av SSMS](https://msdn.microsoft.com/library/mt238290) och använd alltid den senaste versionen när du arbetar med Azure SQL Database. Mer information om hur du hanterar din Azure SQL Database med SSMS finns i [Hantera SQL-databaser med hjälp av SSMS](sql-database-manage-azure-ssms.md).

> [!IMPORTANT]
> Använd alltid den senaste versionen av [SQL Management Studio](https://msdn.microsoft.com/library/mt238290) och [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) för att förbli synkroniserad med uppdateringar av Microsoft Azure och SQL Database.
>  

## <a name="powershell"></a>PowerShell
Du kan använda PowerShell för att hantera databaser och elastiska pooler och för att automatisera Azure resursdistributioner. Microsoft rekommenderar det här verktyget vid hantering av ett stort antal databaser samt för automatiserad distribution och resursändringar i en produktionsmiljö.

Mer information finns i [Hantera SQL Database med PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Elastic Database-verktyg
Använd Elastic Database-verktygen när du vill utföra åtgärder som at 

* Köra ett T-SQL-skript mot en uppsättning databaser med hjälp av ett [elastiskt jobb](sql-database-elastic-jobs-overview.md)
* Flytta databasmodeller för flera innehavare till en enskild klientmodell med den [verktyget för att dela/sammanslå](sql-database-elastic-scale-overview-split-and-merge.md)
* Hantering av databas i en enskild klientmodell eller en modell med flera innehavare med hjälp av [klientbiblioteket med elastisk skalning](sql-database-elastic-database-client-library.md).

## <a name="additional-resources"></a>Ytterligare resurser
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* [Azure Automation](https://azure.microsoft.com/documentation/services/automation/)
* [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)




<!--HONumber=Dec16_HO3-->


