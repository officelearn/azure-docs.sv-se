---
title: AZURE Synapse SQL-arkitektur
description: Lär dig hur Azure Synapse SQL kombinerar massivt parallell bearbetning (MPP) med Azure Storage för att uppnå hög prestanda och skalbarhet.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431818"
---
# <a name="azure-synapse-sql-architecture"></a>AZURE Synapse SQL-arkitektur 

I den här artikeln beskrivs arkitekturkomponenterna i Synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Synapse SQL-arkitekturkomponenter

Synapse SQL utnyttjar en utskalningsarkitektur för att distribuera beräkningsbearbetning av data över flera noder. Beräkning är separat från lagring, vilket gör att du kan skala beräkning oberoende av data i ditt system. 

För SQL-pool är skalenheten en abstraktion av beräkningskraft som kallas en [informationslagerenhet](resource-consumption-models.md). 

För SQL on-demand, som är serverlös, skala görs automatiskt för att tillgodose krav på frågeresurs. När topologin ändras med tiden genom att lägga till, ta bort noder eller redundans, anpassas den till ändringar och ser till att frågan har tillräckligt med resurser och avslutas. Bilden nedan visar till exempel SQL on-demand med 4 beräkningsnoder för att köra en fråga.

![Synapse SQL-arkitektur](./media//overview-architecture/sql-architecture.png)

Synapse SQL använder en nodbaserad arkitektur. Program ansluter och utfärdar T-SQL-kommandon till en kontrollnod, som är den enda ingångspunkten för Synapse SQL. 

Noden SQL pool Control använder MPP-motor för att optimera frågor för parallell bearbetning och skickar sedan åtgärder till beräkningsnoder för att utföra sitt arbete parallellt. 

SQL on-demand Control-noden använder DQP-motorn (Distributed Query Processing) för att optimera och dirigera distribuerad körning av användarfrågan genom att dela upp den i mindre frågor som ska köras på beräkningsnoder. Varje liten fråga kallas uppgift och representerar distribuerad körningsenhet. Den läser filer från lagring, sammanfogar resultat från andra uppgifter, grupper eller order data som hämtas från andra uppgifter. 

Beräkningsnoderna lagrar alla användardata i Azure Storage och kör de parallella frågorna. Data Movement Service (DMS) är en intern tjänst på systemnivå som flyttar data mellan noder efter behov för att köra frågor parallellt och returnera korrekta resultat. 

Med frikopplad lagring och beräkning kan man vid användning av Synapse SQL dra nytta av oberoende dimensionering av beräkningskraft oavsett dina lagringsbehov. För SQL på begäran skala görs automatiskt, medan för SQL pool kan man:

* Öka eller förminska beräkningskraften i en SQL-pool (informationslager) utan att flytta data.
* Pausa beräkningskapaciteten och lämna data intakta, så att du bara betalar för lagring.
* Återuppta beräkningskapacitet under driftstimmar.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL använder Azure Storage för att skydda dina användardata. Eftersom dina data lagras och hanteras av Azure Storage finns det en separat avgift för din lagringsförbrukning. 

Med SQL on-demand kan du fråga efter filer i datasjön på skrivskyddat sätt, medan sql-poolen låter dig använda data också. När data förtärs i SQL-poolen fragmenteras data till **distributioner** för att optimera systemets prestanda. Du kan välja vilket fragmenteringsmönster som ska användas för att distribuera data när du definierar tabellen. Dessa fragmenteringsmönster stöds:

* Hash
* Resursallokering
* Replikera

## <a name="control-node"></a>Kontrollnoden

Kontrollnoden är arkitekturens hjärna. Det är den som är klientdelen som interagerar med alla program och anslutningar. 

I SQL-poolen körs MPP-motorn på noden Kontroll för att optimera och samordna parallella frågor. När du skickar en T-SQL-fråga till SQL-pool omvandlar kontrollnoden den till frågor som körs mot varje distribution parallellt.

I SQL on-demand körs DQP-motorn på kontrollnod för att optimera och samordna distribuerad körning av användarfrågan genom att dela upp den i mindre frågor som ska köras på beräkningsnoder. Det tilldelar också uppsättningar av filer som ska bearbetas av varje nod.

## <a name="compute-nodes"></a>Beräkningsnoder

Beräkningsnoderna ger dataresurser. 

I SQL-poolen mappas distributioner till beräkningsnoder för bearbetning. När du betalar för fler beräkningsresurser mappas distributionerna till de tillgängliga beräkningsnoderna om. Antalet beräkningsnoder varierar från 1 till 60 och bestäms av tjänstnivån för SQL-pool. Varje beräkningsnod har ett nod-ID som visas i systemvyer. Du kan se beräkningsnod-ID:t genom att leta efter node_id kolumn i systemvyer vars namn börjar med sys.pdw_nodes. En lista över dessa systemvyer finns i [MPP-systemvyer](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

I SQL on-demand tilldelas varje beräkningsnod uppgift och uppsättning filer att köra uppgiften på. Uppgiften är distribuerad frågekörningsenhet, som faktiskt är en del av den skickade frågeanvändaren. Automatisk skalning är i kraft för att se till att tillräckligt med beräkningsnoder används för att köra användarfrågor.

## <a name="data-movement-service"></a>Data Movement Service

Data Movement Service (DMS) är datatransporttekniken i SQL-poolen som samordnar dataflyttning mellan beräkningsnoderna. Vissa frågor kräver dataflyttning för att säkerställa att parallella frågor returnerar korrekta resultat. När data förflyttning krävs säkerställer DMS att rätt data kommer till rätt plats.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distributioner

En distribution är den grundläggande lagrings- och bearbetningsenheten för parallella frågor som körs på distribuerade data i SQL-pool. När SQL-pool kör en fråga delas arbetet upp i 60 mindre frågor som körs parallellt. 

Var och en av de 60 mindre frågorna körs på en av datadistributionerna. Varje Beräkningsnod hanterar en eller flera av de 60 distributionerna. En SQL-pool med maximala beräkningsresurser har en distribution per beräkningsnod. En SQL-pool med minsta beräkningsresurser har alla distributioner på en beräkningsnod. 

## <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
En hash-distribuerad tabell kan leverera högsta frågeprestanda för kopplingar och aggregeringar för stora tabeller. 

Om du vill fragmentera data till en hash-distribuerad tabell använder SQL-poolen en hash-funktion för att deterministiskt tilldela varje rad till en distribution. I tabelldefinitionen utses en av kolumnerna till distributionskolumnen. Hash-funktionen använder värdena i distributionskolumnen för att tilldela varje rad till en distribution.

Följande diagram visar hur en fullständig (ej distribuerad tabell) lagras som en hash-distribuerad tabell. 

![Distribuerad tabell](media//overview-architecture/hash-distributed-table.png "Distribuerad tabell") 

* Varje rad tillhör en fördelning. 
* En deterministisk hash-algoritm tilldelar varje rad till en fördelning. 
* Antalet tabellrader per fördelning varierar beroende på vad som visas av de olika storlekarna på tabeller.

Det finns prestandaöverväganden för valet av en distributionskolumn, till exempel distinktitet, datasnedställning och de typer av frågor som körs i systemet.

## <a name="round-robin-distributed-tables"></a>Distribuerade tabeller med resursallokering

Ett round-robin-bord är den enklaste tabellen för att skapa och ger snabb prestanda när det används som mellanlagringsbord för laster.

En distribuerad tabell med resursallokering distribuerar data jämnt i tabellen men utan ytterligare optimering. En fördelning väljs först slumpmässigt och sedan tilldelas buffertar av rader till distributioner sekventiellt. Det går snabbt att läsa in data i en resursallokeringstabell men frågeprestanda kan ofta vara bättre med hash-distribuerade tabeller. Kopplingar på round-robin-tabeller kräver omfördelningsdata, vilket tar ytterligare tid.

## <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell ger snabbaste frågeprestanda för små tabeller.

En tabell som replikeras cachelagrar en fullständig kopia av tabellen på varje beräkningsnod. Därför behöver du, när du replikerar en tabell, inte överföra data till beräkningsnoder innan en koppling eller aggregering. Replikerade tabeller används bäst med små tabeller. Extra lagringsutrymme krävs och det finns ytterligare omkostnader som uppstår när du skriver data, vilket gör stora tabeller opraktiska. 

Diagrammet nedan visar en replikerad tabell som cachelagras på den första distributionen på varje beräkningsnod. 

![Replikerad tabell](media/overview-architecture/replicated-table.png "Replikerad tabell") 

## <a name="next-steps"></a>Nästa steg

Nu när du vet lite om Synapse SQL kan du lära dig hur du snabbt [skapar en SQL-pool](../quickstart-create-sql-pool.md) och [läser in exempeldata](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Eller så börjar du [använda SQL on-demand](../quickstart-sql-on-demand.md). Om du inte har erfarenhet av Azure kan [Azure-ordlistan](../../azure-glossary-cloud-terminology.md) vara till hjälp om du stöter på ny terminologi. 
