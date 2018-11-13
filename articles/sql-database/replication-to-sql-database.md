---
title: Replikering till Azure SQL Database | Microsoft-Docs ”
description: Läs om hur du använder SQL Server-replikering med Azure SQL Database enskilda databaser och databaser i elastiska pooler
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 11/09/2018
ms.openlocfilehash: 44d6135c8a259d3d4c791d359136963483088c5a
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567901"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikering till SQL Database enkel och delade databaser

SQL Server-replikering kan konfigureras för enkel och delade databaser på en [logisk server](sql-database-logical-servers.md) i Azure SQL Database.  

## <a name="supported-configurations"></a>**Konfigurationer som stöds:**
  
- SQL Server kan vara en instans av SQL Server som körs lokalt eller en instans av SQL Server som körs i Azure-datorer i molnet. Mer information finns i [SQL Server på Azure virtuella datorer – översikt](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Azure SQL-databasen måste vara en push-prenumerant för en SQL Server-utgivare.  
- Distributionsdatabasen och replikeringsagenter kan inte placeras i en Azure SQL database.  
- Ögonblicksbild och enkelriktad Transaktionsreplikering stöds. Peer-to-peer Transaktionsreplikering och Sammanslagningsreplikering stöds inte.
- Replikering är tillgänglig i offentlig förhandsversion på Azure SQL Database Managed Instance. Hanterad instans kan vara värd för utgivaren och distributören prenumerant databaser. Mer information finns i [replikering med SQL Database Managed Instance](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versioner  

- Utgivaren och distributören måste innehålla minst en av följande versioner:  
- SQLServer 2017 (14.x)
- SQLServer 2016 (13.x)
- SQL Server 2014 (12.x) SP1 CU3
- SQL Server 2014 (12.x) RTM CU10
- SQL Server 2012 (11.x) SP2 CU8 eller SP3
- Försök att konfigurera replikering med en äldre version kan resultera i fel antal MSSQL_REPL20084 (processen inte kunde ansluta till prenumeranten.) och MSSQL_REPL40532 (det går inte att öppna servern \<namn > begärdes vid inloggningen. Inloggningen misslyckades.).  
- Om du vill använda alla funktioner i Azure SQL Database, måste du använda de senaste versionerna av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools](https://docs.microsoft.com//sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).  
  
## <a name="remarks"></a>Kommentarer

- Replikering kan konfigureras med hjälp av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller genom att köra Transact-SQL-uttryck på utgivaren. Du kan inte konfigurera replikering med hjälp av Azure portal.  
- Replikering kan bara använda SQL Server-autentiseringsinloggningar för att ansluta till en Azure SQL database.
- Replikerade tabeller måste ha en primärnyckel.  
- Du måste ha en befintlig prenumeration.  
- Azure SQL database-prenumerant kan vara i alla regioner.  
- En enstaka publikation på SQL Server har stöd för både Azure SQL Database och SQL Server (lokalt och SQL Server i virtuella Azure-datorer)-prenumeranter.  
- Replikeringshantering, övervakning och felsökning måste utföras från en lokal SQL Server.  
- Endast push-prenumerationer till Azure SQL Database stöds.  
- Endast `@subscriber_type = 0` stöds i **till sp_addsubscription** för SQL-databas.  
- Azure SQL Database stöder inte dubbelriktad, direkt, uppdateras eller peer-to-peer-replikering.

## <a name="replication-architecture"></a>Arkitektur för replikering  

![replikering till sql database](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenarier  

### <a name="typical-replication-scenario"></a>Vanliga Replikeringsscenario  

1. Skapa en Transaktionsreplikering publikation på en lokal SQL Server-databas.  
2. På en lokal SQL Server använder du den **New Subscription Wizard** eller Transact-SQL-uttryck för att skapa en distribution till Azure SQL Database-prenumeration.  
3. Med enkel och delade databaser i Azure SQL Database är den första datauppsättningen en ögonblicksbild som skapats av Ögonblicksbildagenten och distribueras och tillämpas av Distributionsagenten. Med Azure SQL Database Managed Instance, kan du också använda en säkerhetskopia av databasen för att seeda prenumerationsdatabasen.

### <a name="data-migration-scenario"></a>Data Migreringsscenario  

1. Använda Transaktionsreplikering för att replikera data från en lokal SQL Server-databas till Azure SQL Database.  
2. Omdirigera klienten eller mellannivå program att uppdatera kopian på Azure SQL-databas.  
3. Stoppa uppdaterar SQL Server-versionen av tabellen och ta bort publikationen.  

## <a name="limitations"></a>Begränsningar

Följande alternativ stöds inte för Azure SQL Database-prenumerationer:

- Kopiera filassociation för grupper  
- Kopiera tabell partitionering scheman  
- Kopiera index partitionering scheman  
- Kopiera användardefinierade statistik  
- Kopiera standard-bindningar  
- Kopiera regel bindningar  
- Kopiera fulltext-index  
- Kopiera XML-XSD  
- Kopiera XML-index  
- Kopiera behörigheter  
- Kopiera spatialindex  
- Kopiera filtrerade index  
- Kopiera data komprimering attribut  
- Kopiera attribut för null-optimerad kolumn  
- Konvertera filestream till MAX-datatyper  
- Konvertera hierarchyid till MAX-datatyper  
- Konvertera spatial till MAX-datatyper  
- Kopiera utökade egenskaper  
- Kopiera behörigheter  

### <a name="limitations-to-be-determined"></a>Begränsningar för att fastställa

- Kopiera sortering  
- Körning i en serialiserade transaktion av SP  

## <a name="examples"></a>Exempel

Skapa en publikation och en utgivarinitierad prenumeration. Mer information finns i:
  
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en Push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) genom att använda Azure SQL-databasnamn för logisk server som prenumeranten (till exempel **N'azuresqldbdns.database.windows.net'**) och Azure SQL-databasnamn som mål-databasen ( till exempel **AdventureWorks**).  

## <a name="see-also"></a>Se även  

- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en utgivarinitierad prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
