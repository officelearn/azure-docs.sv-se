---
title: Replikering
description: Lär dig mer om hur du använder SQL Server-replikering med enstaka Azure SQL Database-databaser och databaser i elastiska pooler
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: f28269b067ee98d69a97799911fd2d84a7f91e34
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381153"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikering till en-SQL-databas med en och poolad databas

SQL Server-replikering kan konfigureras till enstaka och poolade databaser på en [SQL Database-server](sql-database-servers.md) i Azure SQL Database.  

## <a name="supported-configurations"></a>**Konfigurationer som stöds:**
  
- SQL Server kan vara en instans av SQL Server som körs lokalt eller en instans av SQL Server som körs i en virtuell Azure-dator i molnet. Mer information finns i [översikt över SQL Server på Virtuella Azure-datorer](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Azure SQL-databasen måste vara en push-prenumerant på en SQL Server-utgivare.  
- Distributionsdatabasen och replikeringsagenterna kan inte placeras på en Azure SQL-databas.  
- Ögonblicksbild och enkelriktad transaktionsreplikering stöds. Peer-to-peer-transaktionsreplikering och sammanfogningsreplikering stöds inte.
- Replikering är tillgängligt för offentlig förhandsversion på Azure SQL Database Managed Instance. Hanterad instans kan vara värd för utgivar-, distributörs- och prenumerantdatabaser. Mer information finns i [Replikering med SQL Database Managed Instance](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versioner  

Lokala SQL Server-utgivare och distributörer måste använda (minst) någon av följande versioner:  

- SQL Server 2016 och senare
- SQL Server 2014 [RTM CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) eller [SP1 CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) eller [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Om du försöker konfigurera replikering med hjälp av en version som inte stöds kan det leda till felnummer \<MSSQL_REPL20084 (Processen kunde inte ansluta till Prenumeranten.) och MSSQL_REPL40532 (Det går inte att öppna servernamnet> som begärs av inloggningen. Inloggningen misslyckades.).  

Om du vill använda alla funktioner i Azure SQL Database måste du använda de senaste versionerna av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och SQL Server Data [Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

  
## <a name="remarks"></a>Anmärkningar

- Replikering kan konfigureras med hjälp av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller genom att köra Transact-SQL-uttryck på utgivaren. Du kan inte konfigurera replikering med hjälp av Azure-portalen.  
- Replikering kan bara använda SQL Server-autentiseringsinloggningar för att ansluta till en Azure SQL-databas.
- Replikerade tabeller måste ha en primärnyckel.  
- Du måste ha en befintlig Azure-prenumeration.  
- Azure SQL-databasprenumeranten kan finnas i alla regioner.  
- En enda publikation på SQL Server kan stödja både Azure SQL Database och SQL Server (lokalt och SQL Server i en virtuell Azure-dator) prenumeranter.  
- Replikeringshantering, övervakning och felsökning måste utföras från den lokala SQL Server.  
- Endast push-prenumerationer till Azure SQL Database stöds.  
- Endast `@subscriber_type = 0` stöds i **sp_addsubscription** för SQL Database.  
- Azure SQL Database stöder inte dubbelriktad, omedelbar, uppdateringsbar eller peer to peer-replikering.

## <a name="replication-architecture"></a>Replikeringsarkitektur  

![replikering-till-sql-databas](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenarier  

### <a name="typical-replication-scenario"></a>Typiskt replikeringsscenario  

1. Skapa en transaktionsreplikeringspublikation i en lokal SQL Server-databas.  
2. I den lokala SQL Server använder du **guiden Ny prenumeration** eller Transact-SQL-uttryck för att skapa en push till prenumeration till Azure SQL Database.  
3. Med enstaka och poolade databaser i Azure SQL Database är den första datauppsättningen en ögonblicksbild som skapas av Snapshot Agent och distribueras och tillämpas av distributionsagenten. Med en hanterad instansdatabas kan du också använda en säkerhetskopia av databasen för att dirigera prenumerantdatabasen.

### <a name="data-migration-scenario"></a>Scenario för datamigrering  

1. Använd transaktionsreplikering för att replikera data från en lokal SQL Server-databas till Azure SQL Database.  
2. Omdirigera klient- eller mellannivåprogrammen för att uppdatera Azure SQL-databaskopian.  
3. Sluta uppdatera SQL Server-versionen av tabellen och ta bort publikationen.  

## <a name="limitations"></a>Begränsningar

Följande alternativ stöds inte för Azure SQL Database-prenumerationer:

- Kopiera filgruppsassociation  
- Kopiera tabellpartitioneringsscheman  
- Kopiera indexpartitioneringsscheman  
- Kopiera användardefinierad statistik  
- Kopiera standardbindningar  
- Kopiera regelbindningar  
- Kopiera fullständiga textindex  
- Kopiera XML XSD  
- Kopiera XML-index  
- Kopiera behörigheter  
- Kopiera rumsliga index  
- Kopiera filtrerade index  
- Kopiera datakomprimeringsattribut  
- Kopiera ett glest kolumnattribut  
- Konvertera filström till MAX-datatyper  
- Konvertera hierarki till MAX-datatyper  
- Konvertera rumsliga till MAX-datatyper  
- Kopiera utökade egenskaper  
- Kopiera behörigheter  

### <a name="limitations-to-be-determined"></a>Begränsningar som skall fastställas

- Kopiera sortering  
- Körning i en serialiserad transaktion av SP  

## <a name="examples"></a>Exempel

Skapa en publikation och en push-prenumeration. Mer information finns i:
  
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) med hjälp av Azure SQL Database-servernamnet som prenumerant (till exempel **N'azuresqldbdns.database.windows.net'**) och Azure SQL-databasnamnet som måldatabas (till exempel **AdventureWorks**).  

## <a name="see-also"></a>Se även  

- [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md)
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
