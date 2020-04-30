---
title: Replikering
description: Lär dig mer om att använda SQL Server replikering med Azure SQL Database enkla databaser och databaser i elastiska pooler
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 49be53febc1783edfa16fd019a094a7e80e1cdf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231653"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Replikering till SQL Database enkla databaser och databaser i pooler

SQL Server replikering kan konfigureras till enstaka databaser och databaser på en [SQL Database-Server](sql-database-servers.md) i Azure SQL Database.  

> [!NOTE]
> I den här artikeln beskrivs användningen [av Transaktionsreplikering](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication) i Azure SQL Database. Den är inte relaterad till [aktiv geo-replikering](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication), en Azure SQL Database funktion som gör att du kan skapa kompletta läsbara repliker av enskilda databaser.

## <a name="supported-configurations"></a>Konfigurationer som stöds
  
- SQL Server kan vara en instans av SQL Server som körs lokalt eller en instans av SQL Server som körs i en virtuell Azure-dator i molnet. Mer information finns i [Översikt över SQL Server på Azure Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
- Azure SQL-databasen måste vara en push-prenumerant av en SQL Server utgivare.  
- Distributions databasen och-replik agenterna kan inte placeras i en Azure SQL-databas.  
- Ögonblicks bilder och enkelriktad transaktionell replikering stöds. Peer-to-peer-transaktionell replikering och Sammanslagningsreplikering stöds inte.
- Replikering är tillgänglig för offentlig för hands version på Azure SQL Database Hanterad instans. Den hanterade instansen kan vara värd för utgivare, distributör och prenumerant databaser. Mer information finns i [replikering med SQL Database Hanterad instans](replication-with-sql-database-managed-instance.md).

## <a name="versions"></a>Versioner  

Lokala SQL Server utgivare och distributörer måste använda (minst) en av följande versioner:  

- SQL Server 2016 och senare
- SQL Server 2014 [RTM-CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) eller [SP1-CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) eller [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

> [!NOTE]
> Försök att konfigurera replikering med en version som inte stöds kan resultera i fel nummer MSSQL_REPL20084 (processen kunde inte ansluta till prenumeranten.) och MSSQL_REPL40532 (det går inte att öppna \<server namnet> begärd inloggning. Inloggningen misslyckades.).  

Om du vill använda alla funktioner i Azure SQL Database måste du använda de senaste versionerna av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

  
## <a name="remarks"></a>Anmärkningar

- Du kan konfigurera replikering med hjälp av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller genom att köra Transact-SQL-uttryck på utgivaren. Du kan inte konfigurera replikering med hjälp av Azure Portal.  
- Replikeringen kan bara använda SQL Server autentiserings-inloggningar för att ansluta till en Azure SQL-databas.
- Replikerade tabeller måste ha en primär nyckel.  
- Du måste ha en befintlig Azure-prenumeration.  
- Azure SQL Database-prenumeranten kan finnas i vilken region som helst.  
- En enda publikation på SQL Server kan stödja både Azure SQL Database och SQL Server (lokalt och SQL Server på en virtuell Azure-dator) prenumeranter.  
- Hantering av replikering, övervakning och fel sökning måste utföras från den lokala SQL Server.  
- Endast push-prenumerationer till Azure SQL Database stöds.  
- Stöds `@subscriber_type = 0` endast i **sp_addsubscription** för SQL Database.  
- Azure SQL Database stöder inte dubbelriktad, omedelbar, uppdaterbar eller peer-to-peer-replikering.

## <a name="replication-architecture"></a>Arkitektur för replikering  

![replikering till SQL-databas](./media/replication-to-sql-database/replication-to-sql-database.png)  

## <a name="scenarios"></a>Scenarier  

### <a name="typical-replication-scenario"></a>Vanligt scenario för replikering  

1. Skapa en transaktionskö för replikering på en lokal SQL Server databas.  
2. På den lokala SQL Server använda **Guiden ny prenumeration** eller Transact-SQL-uttryck för att skapa en push-överföring till-Azure SQL Database.  
3. Med enskilda databaser och databaser i Azure SQL Database är den ursprungliga data uppsättningen en ögonblicks bild som skapas av ögonblicks bild agenten och distribueras och tillämpas av distributions agenten. Med en hanterad instans databas kan du också använda en databas säkerhets kopia för att dirigera prenumerantens databas.

### <a name="data-migration-scenario"></a>Scenario för data migrering  

1. Använd transaktionell replikering för att replikera data från en lokal SQL Server databas till Azure SQL Database.  
2. Omdirigera klienten eller program på mellan nivå för att uppdatera Azure SQL Database-kopian.  
3. Sluta uppdatera SQL Server-versionen av tabellen och ta bort publikationen.  

## <a name="limitations"></a>Begränsningar

Följande alternativ stöds inte för Azure SQL Database prenumerationer:

- Kopiera fil grupps Association  
- Kopiera tabell partitionerings scheman  
- Kopiera index partitionerings scheman  
- Kopiera användardefinierad statistik  
- Kopiera standard bindningar  
- Kopiera regel bindningar  
- Kopiera full text index  
- Kopiera XML XSD  
- Kopiera XML-index  
- Kopiera behörigheter  
- Kopiera rums index  
- Kopiera filtrerade index  
- Kopiera data komprimerings attribut  
- Kopiera sparse-kolumnattribut  
- Konvertera FILESTREAM till MAX data typer  
- Konvertera hierarchyid till MAX data typer  
- Konvertera spatialdata till MAX data typer  
- Kopiera utökade egenskaper  
- Kopiera behörigheter  

### <a name="limitations-to-be-determined"></a>Begränsningar som ska fastställas

- Kopiera sortering  
- Körning i en serialiserad transaktion av SP  

## <a name="examples"></a>Exempel

Skapa en publikation och en utgivarinitierad prenumeration. Mer information finns i:
  
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) med hjälp av Azure SQL Database Server namnet som prenumeranten (till exempel **N'azuresqldbdns. Database. Windows. net '**) och Azure SQL Database-namnet som mål databas (till exempel **AdventureWorks**).  

## <a name="see-also"></a>Se även  

- [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md)
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
