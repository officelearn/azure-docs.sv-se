---
title: Azure Synapse Analytics -arkitektur (tidigare SQL DW)
description: Lär dig hur Azure Synapse Analytics (tidigare SQL DW) kombinerar massivt parallell bearbetning (MPP) med Azure Storage för att uppnå hög prestanda och skalbarhet.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0d768581b8f1a177190da08986af8b60b4861432
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744209"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics -arkitektur (tidigare SQL DW)

Azure Synapse är en obegränsad analystjänst som sammanför informationslager i företagsklass och stordataanalys. Det ger dig friheten att fråga efter data på dina villkor, med hjälp av antingen serverlösa resurser på begäran eller etablerade resurser – i stor skala. Azure Synapse sammanför dessa två världar med en enhetlig upplevelse för att inta, förbereda, hantera och hantera data för omedelbara bi- och maskininlärningsbehov.

 Azure Synapse har fyra komponenter:

- SQL Analytics: Komplett T-SQL-baserad analys

  - SQL pool (betala per DWU etablerad) - Allmänt tillgänglig
  - SQL on-demand (betala per TB bearbetad) – (preview)
- Spark: Djupt integrerad Apache Spark (Preview)
- Dataintegration: Integrering av hybriddata (förhandsversion)
- Studio: enhetlig användarupplevelse.  (Förhandsversion)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Synapse SQL MPP-arkitekturkomponenter

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) utnyttjar en skalningsarkitektur för att distribuera beräkningsbearbetning av data över flera noder. Skalenheten är en abstraktion av beräkningskraft som kallas en [informationslagerenhet](what-is-a-data-warehouse-unit-dwu-cdwu.md). Beräkning är separat från lagring, vilket gör att du kan skala beräkning oberoende av data i ditt system.

![Synapse SQL-arkitektur](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics använder en nodbaserad arkitektur. Program ansluter och utfärdar T-SQL-kommandon till en kontrollnod, som är den enda ingångspunkten för SQL Analytics. Kontrollnoden kör MPP-motorn, som optimerar frågor för parallell bearbetning, och skickar sedan åtgärder till Beräkningsnoder för att utföra sitt arbete parallellt.

Beräkningsnoderna lagrar alla användardata i Azure Storage och kör de parallella frågorna. Data Movement Service (DMS) är en intern tjänst på systemnivå som flyttar data mellan noder efter behov för att köra frågor parallellt och returnera korrekta resultat.

Med frikopplad lagring och beräkning, när du använder Synapse SQL pool kan man:

- Oberoende storlek beräkna makt oberoende av dina lagringsbehov.
- Öka eller förminska beräkningskraften i en SQL-pool (informationslager) utan att flytta data.
- Pausa beräkningskapaciteten och lämna data intakta, så att du bara betalar för lagring.
- Återuppta beräkningskapacitet under driftstimmar.

### <a name="azure-storage"></a>Azure Storage

Synapse SQL använder Azure Storage för att skydda dina användardata.  Eftersom dina data lagras och hanteras av Azure Storage finns det en separat avgift för din lagringsförbrukning. Data är fragmenterade i **distributioner** för att optimera systemets prestanda. Du kan välja vilket fragmenteringsmönster som ska användas för att distribuera data när du definierar tabellen. Dessa fragmenteringsmönster stöds:

- Hash
- Resursallokering
- Replikera

### <a name="control-node"></a>Kontrollnoden

Kontrollnoden är arkitekturens hjärna. Det är den som är klientdelen som interagerar med alla program och anslutningar. MPP-motorn körs på kontrollnoden för att optimera och koordinera parallella frågor. När du skickar en T-SQL-fråga omvandlar kontrollnoden den till frågor som körs mot varje distribution parallellt.

### <a name="compute-nodes"></a>Beräkningsnoder

Beräkningsnoderna ger dataresurser. Distributioner mappas till Beräkningsnoder för bearbetning. När du betalar för fler beräkningsresurser mappas distributioner om till tillgängliga beräkningsnoder. Antalet beräkningsnoder varierar från 1 till 60 och bestäms av tjänstnivån för Synapse SQL.

Varje beräkningsnod har ett nod-ID som visas i systemvyer. Du kan se beräkningsnod-ID:t genom att leta efter node_id kolumn i systemvyer vars namn börjar med sys.pdw_nodes. En lista över dessa systemvyer finns i [MPP-systemvyer](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) är datatransporttekniken som samordnar dataflyttning mellan beräkningsnoderna. Vissa frågor kräver dataflyttning för att säkerställa att parallella frågor returnerar korrekta resultat. När data förflyttning krävs säkerställer DMS att rätt data kommer till rätt plats.

## <a name="distributions"></a>Distributioner

En distribution är den grundläggande lagringsenheten för parallella frågor som körs på distribuerade data. När SQL Analytics kör en fråga delas arbetet upp i 60 mindre frågor som körs parallellt.

Var och en av de 60 mindre frågorna körs på en av datadistributionerna. Varje Beräkningsnod hanterar en eller flera av de 60 distributionerna. En SQL-pool med maximala beräkningsresurser har en distribution per beräkningsnod. En SQL-pool med minsta beräkningsresurser har alla distributioner på en beräkningsnod.  

## <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller

En hash-distribuerad tabell kan leverera högsta frågeprestanda för kopplingar och aggregeringar för stora tabeller.

En hash-distribuerad tabell kan leverera högsta frågeprestanda för kopplingar och aggregeringar för stora tabeller.

Om du vill fragmentera data till en hash-distribuerad tabell används en hash-funktion för att deterministiskt tilldela varje rad till en fördelning. I tabelldefinitionen utses en av kolumnerna till distributionskolumnen. Hash-funktionen använder värdena i distributionskolumnen för att tilldela varje rad till en distribution.

Följande diagram visar hur en fullständig (ej distribuerad tabell) lagras som en hash-distribuerad tabell.

![Distribuerad tabell](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Distribuerad tabell")  

- Varje rad tillhör en fördelning.  
- En deterministisk hash-algoritm tilldelar varje rad till en fördelning.  
- Antalet tabellrader per fördelning varierar beroende på vad som visas av de olika storlekarna på tabeller.

Det finns prestandaöverväganden för valet av en distributionskolumn, till exempel distinktitet, datasnedställning och de typer av frågor som körs i systemet.

## <a name="round-robin-distributed-tables"></a>Distribuerade tabeller med resursallokering

Ett round-robin-bord är den enklaste tabellen för att skapa och ger snabb prestanda när det används som mellanlagringsbord för laster.

En distribuerad tabell med resursallokering distribuerar data jämnt i tabellen men utan ytterligare optimering. En fördelning väljs först slumpmässigt och sedan tilldelas buffertar av rader till distributioner sekventiellt. Det går snabbt att läsa in data i en resursallokeringstabell men frågeprestanda kan ofta vara bättre med hash-distribuerade tabeller. Kopplingar på round-robin-tabeller kräver omfördelningsdata, vilket tar ytterligare tid.

## <a name="replicated-tables"></a>Replikerade tabeller

En replikerad tabell ger snabbaste frågeprestanda för små tabeller.

En tabell som replikeras cachelagrar en fullständig kopia av tabellen på varje beräkningsnod. Därför behöver du, när du replikerar en tabell, inte överföra data till beräkningsnoder innan en koppling eller aggregering. Replikerade tabeller används bäst med små tabeller. Extra lagringsutrymme krävs och det finns ytterligare omkostnader som uppstår när du skriver data, vilket gör stora tabeller opraktiska.  

Diagrammet nedan visar en replikerad tabell som cachelagras på den första distributionen på varje beräkningsnod.  

![Replikerad tabell](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Replikerad tabell")

## <a name="next-steps"></a>Nästa steg

Nu när du vet lite om Azure Synapse kan du lära dig hur du snabbt [skapar en SQL-pool](create-data-warehouse-portal.md) och [läser in exempeldata](load-data-from-azure-blob-storage-using-polybase.md). Om du inte har erfarenhet av Azure kan [Azure-ordlistan](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) vara till hjälp om du stöter på ny terminologi. Eller titta på några av dessa andra Azure Synapse-resurser.  

- [Kundernas framgångsberättelser](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Funktionsbegäranden](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Videor](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md)
- [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
