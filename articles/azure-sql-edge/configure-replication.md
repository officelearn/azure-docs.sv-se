---
title: Konfigurera replikering till Azure SQL Edge (för hands version)
description: Läs om hur du konfigurerar replikering till Azure SQL Edge (för hands version)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596948"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Konfigurera replikering till Azure SQL Edge (för hands version) 

Azure SQL Edge-instansen kan konfigureras som push-prenumerant för en enkelriktad transaktionell replikering eller ögonblicks bild replikering. Azure SQL Edge-instansen kan inte fungera som utgivare eller distributör för konfiguration av transaktionell replikering. Sammanslagningsreplikering, P2P-replikering, Oracle-publicering stöds inte med Azure SQL Edge.

## <a name="supported-configurations"></a>**Konfigurationer som stöds**:
  
- Azure SQL Edge-instansen måste vara en push-prenumerant för en utgivare.
- Utgivaren och distributören kan antingen vara
   - En instans av SQL Server som körs lokalt eller en instans av SQL Server som körs i en virtuell Azure-dator. Mer information finns i [Översikt över SQL Server på Azure Virtual Machines](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). SQL Server instanser måste använda en version som är större än SQL Server 2016.
   - En instans av Azure SQL Database Hanterad instans. Den hanterade instansen kan vara värd för utgivare, distributör och prenumerant databaser. Mer information finns i [replikering med SQL Database Hanterad instans](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- Distributions databasen och-replik agenterna kan inte placeras på en Azure SQL Edge-instans.  

> [!NOTE]
> Försök att konfigurera replikering med en version som inte stöds kan resultera i fel nummer MSSQL_REPL20084 (processen kunde inte ansluta till prenumeranten.) och MSSQL_REPL40532 (det går inte att öppna Server \< namnet> begärd inloggning. Inloggningen misslyckades.).  

Om du vill använda alla funktioner i Azure SQL Edge måste du använda de senaste versionerna av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

## <a name="remarks"></a>Kommentarer

- Du kan konfigurera replikering med hjälp av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) eller genom att köra Transact-SQL-uttryck på utgivaren med hjälp av antingen SQL Server Management Studio eller [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)
- Replikeringen kan bara använda SQL Server autentiserings-inloggningar för att ansluta till en Azure SQL Edge-instans.
- Replikerade tabeller måste ha en primär nyckel.
- En enda publikation på SQL Server kan stödja både Azure SQL Edge och SQL Server (lokala och SQL Server på en virtuell Azure-dator) prenumeranter.  
- Hantering av replikering, övervakning och fel sökning måste utföras från den lokala SQL Server.  
- Det finns bara stöd för push-prenumerationer på Azure SQL Edge.  
- `@subscriber_type = 0`Stöds endast i **Sp_addsubscription** för Azure SQL Edge.  
- Azure SQL Edge stöder inte dubbelriktad, omedelbar, uppdaterbar eller peer-to-peer-replikering.
- Azure SQL Edge stöder endast en delmängd av funktioner som är tillgängliga i SQL Server eller Azure SQL Database Hanterad instans, vilket innebär att ett försök att replikera en databas (eller objekt i databasen) som innehåller en eller flera funktioner som inte stöds orsakar ett haveri. Om du till exempel försöker replikera en databas som innehåller objekt med spatiala data typer, resulterar det i ett fel. Mer information om funktioner som stöds av Azure SQL Edge finns i [funktioner som stöds i Azure SQL Edge](features.md).

## <a name="scenarios"></a>Scenarier  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Initiera referens data på en Edge-instans

Ett vanligt scenario där replikering kan vara användbart är när det finns behov av att initiera Edge-instansen med referens data som ändras över tid. Exempelvis uppdaterar ML-modeller på Edge-instansen när de har tränats på en lokal SQL Server instans.

1. Skapa en transaktionskö för replikering på en lokal SQL Server databas.  
2. På den lokala SQL Server använder du den **nya prenumerations guiden** eller Transact-SQL-uttryck för att skapa en push to-prenumeration till Azure SQL Edge.  
3. Den replikerade databasen på Azure SQL Edge kan initieras genom att antingen använda en ögonblicks bild som genererats av ögonblicks bild agenten och distribueras och levereras av distributions agenten eller med hjälp av en säkerhets kopia av databasen från utgivaren. Om säkerhets kopian av databasen innehåller objekt/funktioner som inte stöds av Azure SQL Edge, kan återställningen Miss kopie ras.

## <a name="limitations"></a>Begränsningar

Följande alternativ stöds inte för Azure SQL Edge-prenumerationer:

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
- Kopiera FILESTREAM, hierarchyid eller spatiala data typer.
- Konvertera hierarchyid till MAX data typer  
- Konvertera spatialdata till MAX data typer  
- Kopiera utökade egenskaper  
- Kopiera behörigheter  

## <a name="examples"></a>Exempel

Skapa en publikation och en utgivarinitierad prenumeration. Mer information finns i:
  
- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) med hjälp av Azure SQL Edge-servernamnet/IP som prenumerant (till exempel **myEdgeinstance, 1433**) och ett databas namn på Azure SQL Edge-instansen som mål databas (till exempel **AdventureWorks**).  

## <a name="see-also"></a>Se även  

- [Skapa en publikation](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


