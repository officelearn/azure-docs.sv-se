---
title: Hur distribuerade data fungerar i Azure SQL Data Warehouse | Microsoft Docs
description: "Lär dig hur data distribueras massivt parallell bearbetning (MPP) och alternativ för att distribuera tabeller i Azure SQL Data Warehouse och Parallel Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: bae494a6-7ac5-4c38-8ca3-ab2696c63a9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/12/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3c166acb17193caae32d7bad133ec510ff679353
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Distribuerade data och distribuerade tabeller för massivt parallell bearbetning (MPP)
Lär dig hur användardata distribueras i Azure SQL Data Warehouse och Parallel Data Warehouse är Microsofts massivt parallell bearbetning (MPP) system. Designa ditt informationslager effektivt använda distribuerade data hjälper dig att uppnå frågebearbetning fördelarna med MPP-arkitektur. Några databasen designalternativen kan ha en betydande inverkan på förbättra frågeprestanda.  

> [!NOTE]
> Azure SQL Data Warehouse och Parallel Data Warehouse använder samma massivt parallell bearbetning (MPP) design, men de har några skillnader på grund av den underliggande plattformen. SQL Data Warehouse är en plattform som en tjänst (PaaS) som körs på Azure. Parallel Data Warehouse körs på Analytics Platform System (AP: er) som är ett lokalt program som körs på Windows Server.
> 
> 

## <a name="what-is-distributed-data"></a>Vad är distribuerade data?
SQL Data Warehouse och Parallel Data Warehouse avser distribuerade data användardata som lagras på flera platser i hela MPP-systemet. Var och en av dessa platser fungerar som en oberoende lagring och bearbetningsenhet som kör frågor på sin datadel. Distribuerade data är grundläggande för att köra frågor parallellt att uppnå hög frågeprestanda.

Om du vill distribuera data tilldelar datalagret varje rad i en tabell till en distribuerad plats.  Du kan distribuera tabeller med hash-distributionsmetod eller resursallokering metod. Metoden distribution har angetts i instruktionen CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
Följande diagram illustrerar hur en fullständig (icke-distribuerade tabell) hämtar lagras som en distribuerad hash-tabell. En deterministisk funktion tilldelar varje rad tillhöra en distribution. I tabelldefinitionen, är en av kolumnerna utses kolumnen distribution. Hash-funktionen använder värdet i kolumnen distribution för att tilldela varje rad till en distributionsplats.

Det finns prestandaöverväganden för val av en distribution-kolumn som särskiljbarhet, förskjutning av data och vilka typer av frågor köras på systemet.

![Distribuerade tabell](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "distribuerade tabell")  

* Varje rad hör till en distributionsplats.  
* En deterministisk hash-algoritm tilldelar varje rad med en distribution.  
* Antalet rader per distribution varierar som visas av olika storlekar för tabeller.

## <a name="round-robin-distributed-tables"></a>Resursallokering distribuerade tabeller
En distribuerad tabell resursallokering distribuerar raderna genom att tilldela varje rad till en distributionsplats i ordning. Det går snabbt att läsa in data i en tabell med resursallokering, men frågeprestanda kanske långsammare.  Ansluta till en tabell med resursallokering vanligtvis måste reshuffling rader om du vill aktivera frågan för att producera korrekta resultat, som det tar tid.

## <a name="distributed-storage-locations-are-called-distributions"></a>Distribuerade lagringsplatser kallas distributioner
Varje distribuerad plats kallas för en distributionsplats. När en fråga körs parallellt, utför en SQL-fråga på sin datadel varje distribution. SQL Data Warehouse använder SQL-databas för att köra frågan. Parallel Data Warehouse använder SQL Server för att köra frågan. Den här delade ingenting arkitekturdesign är grundläggande för att uppnå skalbar parallell datorbearbetning.

### <a name="can-i-view-the-distributions"></a>Kan jag visa distributioner?
Varje distribution har ett distribution-ID och visas i systemvyer som hör till SQL Data Warehouse och Parallel Data Warehouse. Du kan använda distribution-ID för att felsöka prestanda för frågor och andra problem. En lista över systemvyer finns i [MPP system Visa](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Skillnaden mellan en distribution och en beräkningsnod
En distributionsplats är den grundläggande enheten för att lagra distribuerade data och bearbeta parallella frågor. Distributioner grupperas i Compute-noder. Varje Compute-nod spårar en eller flera distributioner.  

* Analytics Platform System använder Compute-noder som en central del av maskinvarukapacitet arkitektur och skalbara. Den använder alltid åtta distributioner per beräkningsnod, som visas i diagram för hash-distribuerade tabeller. Antalet datornoder och därför antal distributioner, bestäms av antalet Compute-noder som du köper för produkten. Om du köper åtta datornoderna hämta till exempel 64-distributioner (8 noder x 8 distributioner/beräkningsnod). 
* SQL Data Warehouse har en fast antal 60 distributioner och ett flexibelt antal Compute-noder. Compute-noderna implementeras med Azure och -resurser. Antalet Compute-noder kan ändras beroende på backend-tjänsten arbetsbelastning och beräkningskapacitet (dwu: er) som du anger för datalagret. När antalet datornoderna ändras, ändras även antal distributioner per Compute-nod. 

### <a name="can-i-view-the-compute-nodes"></a>Kan jag visa Compute-noder?
Varje Compute-nod har ett nod-ID och visas i systemvyer som hör till SQL Data Warehouse och Parallel Data Warehouse.  Du kan se Beräkningsnoden genom att söka efter kolumnen node_id i systemvyer vars namn börjar med sys.pdw_nodes. En lista över systemvyer finns i [MPP system Visa](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Replikerade tabeller
En tabell som har replikerats har ett fullständigt kopia av tabellen lagras på varje beräkningsnod. Replikera en tabell eliminerar behovet av att överföra data mellan datornoderna innan ett join- eller aggregering. Replikerade tabeller är bara möjligt med små tabeller på grund av den extra lagringsutrymme som krävs för att lagra hela tabellen på varje beräkningsnod.  

Följande diagram visar en replikerad tabell som lagras på varje Compute-nod. För SQL Data Warehouse kopieras fullständigt replikerad tabell till en distributionsdatabas på varje beräkningsnod. Parallel Data Warehouse lagras replikerad tabell över alla diskar som tilldelats Compute-nod.  Den här disken strategin implementeras med hjälp av SQL Server-filgrupper.  

![Replikerade tabellen](media/sql-data-warehouse-distributed-data/replicated-table.png "replikerade tabell") 

## <a name="next-steps"></a>Nästa steg
Om du vill använda distribuerade tabeller effektivt finns [distribuerar tabeller i SQL Data Warehouse](sql-data-warehouse-tables-distribute.md)  

