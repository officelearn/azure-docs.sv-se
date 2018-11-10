---
title: Azure SQL Data Warehouse – MPP-arkitektur | Microsoft Docs
description: Lär dig hur Azure SQL Data Warehouse kombinerar massivt parallell bearbetning (MPP) med Azure storage att uppnå höga prestanda och skalbarhet.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 92e16104edb46298d6e503b7546449ed71041047
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005758"
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse – massivt parallell bearbetning (MPP)-arkitektur
Lär dig hur Azure SQL Data Warehouse kombinerar massivt parallell bearbetning (MPP) med Azure storage att uppnå höga prestanda och skalbarhet. 

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="mpp-architecture-components"></a>Komponenter för MPP-arkitektur
SQL Data Warehouse använder en skalbar arkitektur för att distribuera databaserad bearbetning av data över flera noder. Skalning är en abstraktion av beräkningskraft som kallas en [informationslagerenheten](what-is-a-data-warehouse-unit-dwu-cdwu.md). SQL Data Warehouse uppsägningar compute från storage så du skala beräkning oberoende av data i systemet.

![SQL Data Warehouse-arkitektur](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse använder en arkitektur som bygger på noden. Program ansluta och skicka T-SQL-kommandon till en Control-noden är den enda posten för datalagret. Control-noden körs MPP-motorn som optimerar frågor för parallell bearbetning och skickar sedan åtgärder till Compute-noder som utför arbetet parallellt. Compute-noder lagrar alla data i Azure Storage och kör de parallella frågorna. Data Movement Service (DMS) är en intern tjänst som är på systemnivå som flyttar data mellan noderna som behövs för att köra frågor parallellt och returnerar korrekta resultat. 

Med fristående lagringsutrymme och databearbetning kan SQL Data Warehouse:

* Oberoende storlek beräkningskraft oavsett dina lagringsbehov.
* Öka eller minska beräkningskapacitet utan att flytta data.
* Pausa beräkningskapaciteten, lämna data intakta så du betalar bara för lagring.
* Återuppta beräkningskapacitet under driftstimmar.

### <a name="azure-storage"></a>Azure-lagring
SQL Data Warehouse använder Azure storage för att skydda användardata.  Eftersom dina data lagras och hanteras av Azure storage, debiteras SQL Data Warehouse separat för din lagringsanvändning. Själva informationen är fragmenterade (sharded) i **distributioner** optimera prestandan för systemet. Du kan välja vilka mönster för horisontell partitionering du använder för att distribuera data när du har definierat tabellen. SQL Data Warehouse stöder dessa mönster för horisontell partitionering:

* Hash
* Resursallokering
* Replikera

### <a name="control-node"></a>Control-noden

Control-noden är hjärnan för datalagret. Det är den som är klientdelen som interagerar med alla program och anslutningar. MPP-motorn körs på Control-noden för att optimera och samordna parallella frågor. När du skickar en T-SQL-fråga till SQL Data Warehouse omvandlar Control-noden den till frågor som körs mot varje distribution parallellt.

### <a name="compute-nodes"></a>Compute-noder

Compute-noderna ger dataresurser. Distributioner som mappar till Compute-noder för bearbetning. Du behöver betala för mer beräkningsresurser mappar SQL Data Warehouse igen distributioner till tillgängliga beräkningsnoderna. Antalet compute-noderna sträcker sig från 1 till 60 och bestäms av servicenivå för datalagret.

Varje beräkningsnod har ett nod-ID som visas i systemvyer. Du kan se Compute-nod-ID genom att söka efter node_id-kolumnen i systemvyer vars namn börjar med sys.pdw_nodes. En lista över dessa systemvyer finns i [MPP systemvyer](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Data Movement Service
Data Movement Service (DMS) är data transport-teknik som samordnar flyttar data mellan beräkningsnoderna. Vissa frågor kräver dataförflyttning se till de parallella frågorna returnerar korrekta resultat. När dataförflyttning krävs säkerställer DMS rätt data kommer till rätt plats. 

## <a name="distributions"></a>Distributioner

En distribution är den grundläggande körningsenheten på lagring och behandling i samband med parallella frågor som kör på distribuerade data. När du kör en fråga SQL Data Warehouse, är arbetet indelat i 60 mindre frågor som körs parallellt. Alla 60 mindre frågor körs på en av de data-distributionerna. Varje beräkningsnod hanterar ett eller flera av 60 distributioner. Ett informationslager med högsta beräkningsresurser har en distribution per Compute-nod. Ett informationslager med minsta beräkningsresurser har alla distributioner på en beräkningsnod.  

## <a name="hash-distributed-tables"></a>Hash-distribuerad tabeller
En hash-distribuerad tabell kan leverera den högsta frågeprestanda för kopplingar och aggregeringar för stora tabeller. 

Fragmentera data till en hash-distribuerad tabell använder SQL Data Warehouse en hash-funktionen deterministiskt tilldela varje rad till en distributionsplats. En av kolumnerna är utsedd till kolumnen distribution i tabelldefinitionen. Hash-funktionen använder värdena i kolumnen distribution för att tilldela varje rad till en distributionsplats.

Följande diagram illustrerar hur en fullständig (icke-distribuerad tabell) hämtar lagras som en hash-distribuerad tabell. 

![Distribuerad tabell](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuerad tabell")  

* Varje rad tillhör en distribution.  
* En deterministisk hash-algoritm tilldelar varje rad till en distributionsplats.  
* Antalet rader per distribution varierar som visas av olika storlekar med tabeller.

Det finns prestandaöverväganden för att välja en distributionskolumn, till exempel särskiljbarhet datasnedställning och vilka typer av frågor som körs på systemet.

## <a name="round-robin-distributed-tables"></a>Resursallokering distribuerade tabeller
En resursallokeringstabell är den enklaste tabellen för att skapa och ger snabb prestanda när det används som en mellanlagringstabell för belastning.

En distribuerad tabell resursallokering distribuerar data jämnt över tabellen men utan några ytterligare optimering. En distributionsplats först väljs slumpmässigt och sedan buffertar rader är tilldelade till distributioner sekventiellt. Det går snabbt att läsa in data till en resursallokeringstabell, men frågeprestanda kan ofta vara bättre med hash-distribueras tabeller. Kopplingar för resursallokering tabeller kräver reshuffling data och det tar extra tid.


## <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell innehåller de snabbaste frågeprestanda för små tabeller.

En tabell som replikeras cachelagrar en fullständig kopia av tabellen på varje beräkningsnod. Därför eliminerar replikerar en tabell behovet av att överföra data mellan beräkningsnoder innan en join- eller aggregering. Replikerade tabeller används bäst med små tabeller. Extra lagringsutrymme som krävs och det finns ytterligare omkostnader som uppstår när det skrivdata vilket göra stora tabeller opraktiskt.  

Följande diagram visar en replikerad tabell. För SQL Data Warehouse cachelagras replikerad tabell på den första distributionsplatsen på varje beräkningsnod.  

![Replikerat tabell](media/sql-data-warehouse-distributed-data/replicated-table.png "replikerat tabell") 

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse kan du gå vidare och se hur du snabbt [skapar ett SQL Data Warehouse][create a SQL Data Warehouse] och [läsa in exempeldata][load sample data]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Skapa ett supportärende]
* [MSDN-forum]
* [Stack Overflow-forum]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
