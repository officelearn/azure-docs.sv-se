---
title: Azure SQL Data Warehouse - MPP-arkitektur | Microsoft Docs
description: Lär dig hur Azure SQL Data Warehouse kombinerar massivt parallell bearbetning (MPP) med Azure storage att uppnå hög prestanda och skalbarhet.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: e8fef156f4b78c9f7241c9eb9623e061f5a31fe7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="azure-sql-data-warehouse---massively-parallel-processing-mpp-architecture"></a>Azure SQL Data Warehouse - massivt parallell bearbetning (MPP)-arkitektur
Lär dig hur Azure SQL Data Warehouse kombinerar massivt parallell bearbetning (MPP) med Azure storage att uppnå hög prestanda och skalbarhet. 

## <a name="mpp-architecture-components"></a>Komponenter för MPP-arkitektur
SQL Data Warehouse använder en skalbar arkitektur för att distribuera beräkningar bearbetning av data över flera noder. Skala är en abstraktion av datorkraft som kallas en dataenhet för datalager. SQL Data Warehouse avgränsar beräkna från lagringen, vilket gör det möjligt att skala compute oberoende av data i systemet.

![SQL Data Warehouse-arkitektur](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Data Warehouse använder ett nod-baserad arkitektur. Program ansluta och utfärda T-SQL-kommandon till en Control-noden är den enda post för datalagret. Control-noden körs MPP-motorn som optimerar frågor för parallell bearbetning och skickar sedan operations till Compute-noder att utföra sitt arbete parallellt. Compute-noder lagrar alla användardata i Azure Storage och kör de parallella frågorna. Data Movement Service (DMS) är en tjänst med interna på systemnivå som flyttar data mellan noderna som behövs för att köra frågor parallellt och returnerar exakta resultat. 

Med fristående lagringsutrymme och databearbetning kan SQL Data Warehouse:

* Oberoende storlek beräkningskraft oavsett dina lagringsbehov.
* Öka eller minska beräkningskapacitet utan att flytta data.
* Pausa beräkningskapacitet och medan data kvar, så att du betalar bara för lagring.
* Återuppta beräkningskapacitet under driftstimmar.

### <a name="azure-storage"></a>Azure-lagring
SQL Data Warehouse använder Azure-lagring för att skydda användardata.  Eftersom dina data lagras och hanteras av Azure storage, debiterar SQL Data Warehouse separat för användningen av lagringsutrymme. Själva informationen är delat i **distributioner** att optimera prestanda för systemet. Du kan välja vilka horisontell partitionering mönster som ska använda för att distribuera data när du definierar i tabellen. SQL Data Warehouse stöder dessa mönster för horisontell partitionering:

* Hash
* Resursallokering
* Replikera

### <a name="control-node"></a>Control-noden

Control-noden är hjärna för datalagret. Det är den som är klientdelen som interagerar med alla program och anslutningar. MPP-motorn körs på Control-noden för att optimera och samordna parallella frågor. När du skickar en T-SQL-fråga till SQL Data Warehouse, omvandlar Control-noden den till frågor som körs mot varje distribution parallellt.

### <a name="compute-nodes"></a>Compute-noder

Compute-noderna ger dataresurser. Distributioner som mappar till Compute-noder för bearbetning. Som du betalar för fler beräkningsresurser mappar distributioner av tillgängliga datornoderna igen i SQL Data Warehouse. Antalet compute-noderna sträcker sig från 1 till 60 och bestäms av servicenivå för datalagret.

Varje Compute-nod har ett nod-ID som visas i systemvyer. Du kan se Compute-nod-ID genom att söka efter kolumnen node_id i systemvyer vars namn börjar med sys.pdw_nodes. En lista över dessa systemvyer finns [MPP systemvyer](sql-data-warehouse-reference-tsql-statements.md).

### <a name="data-movement-service"></a>Data Movement Service
Data Movement Service (DMS) är data transport-teknik som samordnar flytt av data mellan Compute-noder. Några frågor kräver dataflyttning så de parallella frågorna returnerar exakta resultat. När data flyttas krävs, säkerställer DMS rätt data kommer till rätt plats. 

## <a name="distributions"></a>Distributioner

En distributionsplats är den grundläggande körningsenheten på lagring och behandling i parallella frågor som körs på distribuerade data. När en fråga körs i SQL Data Warehouse är arbetet uppdelat i 60 mindre frågor som körs parallellt. Alla 60 mindre frågor körs på en data-distributioner. Varje Compute-nod hanterar en eller flera av de 60-distributioner. Ett informationslager med högsta beräkningsresurser har en distribution per Compute-nod. Ett informationslager med minsta beräkningsresurser har alla distributioner på en beräkningsnod.  

## <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
En distribuerad hash-tabell kan ge högsta frågeprestanda för kopplingar och aggregeringar för stora tabeller. 

Fragmentera data i en tabell med hash-distribuerad använder SQL Data Warehouse hash-funktion deterministiskt tilldela varje rad med en distribution. I tabelldefinitionen, är en av kolumnerna utses kolumnen distribution. Hash-funktionen använder värdena i kolumnen distribution för att tilldela varje rad till en distributionsplats.

Följande diagram illustrerar hur en fullständig (icke-distribuerade tabell) hämtar lagras som en distribuerad hash-tabell. 

![Distribuerade tabell](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuerade tabell")  

* Varje rad hör till en distributionsplats.  
* En deterministisk hash-algoritm tilldelar varje rad med en distribution.  
* Antalet rader per distribution varierar som visas av olika storlekar för tabeller.

Det finns prestandaöverväganden för val av en kolumn för distribution, till exempel särskiljbarhet, förskjutning av data och vilka typer av frågor som körs på systemet.

## <a name="round-robin-distributed-tables"></a>Resursallokering distribuerade tabeller
En resursallokering tabell är den enklaste tabellen för att skapa och ger snabba prestanda när det används som en mellanlagringstabellen för belastningar.

En distribuerad tabell resursallokering fördelar data jämnt över tabellen men utan några ytterligare optimering. En distributionsplats först väljs slumpmässigt och sedan buffertar för rader som har tilldelats distributioner sekventiellt. Det går snabbt att läsa in data i en tabell med resursallokering, men frågeprestanda kan ofta vara bättre med distribuerade hash-tabeller. Kopplingar för resursallokering tabeller kräver reshuffling data och det tar extra tid.


## <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell ger snabbast frågeprestanda för små tabeller.

En tabell som har replikerats cachelagrar en fullständig kopia av tabellen på varje compute-nod. Därför måste eliminerar replikera en tabell behovet av att överföra data mellan datornoderna innan ett join- eller aggregering. Replikerade tabeller används bäst med små tabeller. Extra lagringsutrymme, och det finns ytterligare omkostnader som uppstår när data skrevs som gör stora tabeller opraktiska.  

Följande diagram visar en replikerad tabell. För SQL Data Warehouse cachelagras replikerad tabell på den första distributionsplatsen på varje beräkningsnod.  

![Replikerade tabellen](media/sql-data-warehouse-distributed-data/replicated-table.png "replikerade tabell") 

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
