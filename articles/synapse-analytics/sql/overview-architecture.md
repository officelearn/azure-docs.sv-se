---
title: Azure Synapse SQL-arkitektur
description: Lär dig hur Azure Synapse SQL kombinerar massivt parallell bearbetning (MPP) med Azure Storage för att uppnå höga prestanda och skalbarhet.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d073bc82b3a9e42e443caa5d3e7855fd4eb5b98b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658907"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL-arkitektur 

I den här artikeln beskrivs arkitektur komponenterna i Synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Komponenter för Synapse-arkitektur i SQL

Synapse SQL utnyttjar en skalbar arkitektur för att distribuera beräknings bearbetning av data över flera noder. Compute är separat från lagring, vilket gör att du kan skala beräkningarna oberoende av data i systemet. 

För SQL-pool är enhets skalan en abstraktion av beräknings kraften som kallas för en [informations lager enhet](resource-consumption-models.md). 

För SQL på begäran, som server lös, görs skalningen automatiskt för att uppfylla resurs kraven för frågor. När topologin ändras över tid genom att lägga till, ta bort noder eller redundans, anpassas till ändringar och kontrollerar att frågan har tillräckligt med resurser och har slutförts. Bilden nedan visar till exempel SQL på begäran som använder 4 Compute-noder för att köra en fråga.

![Synapse SQL-arkitektur](./media//overview-architecture/sql-architecture.png)

Synapse SQL använder en Node-baserad arkitektur. Program ansluter och utfärdar T-SQL-kommandon till en Control-nod, vilket är den enda punkten i posten för Synapse SQL. 

Noden för SQL-adresspoolen använder MPP-motorn för att optimera frågor för parallell bearbetning och skickar sedan åtgärder till Compute-noder för att utföra sitt arbete parallellt. 

Noden SQL-kontroll på begäran använder sig av DQP-motorn (Distributed Query Processing) för att optimera och dirigera distribuerad körning av användar fråga genom att dela upp den i mindre frågor som ska utföras på Compute-noder. Varje liten fråga kallas aktivitet och representerar distribuerad körnings enhet. Den läser fil (er) från lagringen, ger resultat från andra aktiviteter, grupper eller order data som hämtats från andra uppgifter. 

Beräkningsnoderna lagrar alla användardata i Azure Storage och kör de parallella frågorna. Data Movement Service (DMS) är en intern tjänst på systemnivå som flyttar data mellan noder efter behov för att köra frågor parallellt och returnera korrekta resultat. 

När du använder Synapse SQL kan du med fristående lagrings utrymme och data bearbetning dra nytta av oberoende storleks ändring av beräknings kraften oavsett dina lagrings behov. För SQL-skalning på begäran görs automatiskt, medan för SQL-pool kan:

* Öka eller minska beräknings kraften i en SQL-pool (data lager) utan att flytta data.
* Pausa beräkningskapaciteten och lämna data intakta, så att du bara betalar för lagring.
* Återuppta beräkningskapacitet under driftstimmar.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL utnyttjar Azure Storage för att skydda dina användar data. Eftersom dina data lagras och hanteras av Azure Storage, finns det en separat avgift för lagrings användningen. 

Med SQL på begäran kan du fråga filer i data Lake i skrivskyddat läge, medan SQL-poolen gör att du kan mata in data också. När data matas in i SQL-poolen, är data shardade i **distributioner** för att optimera systemets prestanda. Du kan välja vilket horisontell partitionering-mönster som ska användas för att distribuera data när du definierar tabellen. Dessa horisontell partitionering-mönster stöds:

* Hash
* Resursallokering
* Replikera

## <a name="control-node"></a>Kontrollnoden

Noden kontroll är hjärna i arkitekturen. Det är den som är klientdelen som interagerar med alla program och anslutningar. 

I SQL-poolen körs MPP-motorn på noden kontroll för att optimera och koordinera parallella frågor. När du skickar en T-SQL-fråga till SQL-poolen, omvandlar noden kontroll den till frågor som körs mot varje distribution parallellt.

I SQL på begäran körs DQP-motorn på noden kontroll för att optimera och koordinera distribuerad körning av användar frågor genom att dela upp den i mindre frågor som ska utföras på datornoderna. Det tilldelar också uppsättningar av filer som ska bearbetas av varje nod.

## <a name="compute-nodes"></a>Beräkningsnoder

Beräkningsnoderna ger dataresurser. 

I SQL-poolen mappar distributioner till Compute-noder för bearbetning. När du betalar för fler beräknings resurser mappar poolen om distributionerna till de tillgängliga datornoderna. Antalet datornoder sträcker sig från 1 till 60 och bestäms av Service nivån för SQL-poolen. Varje Compute-nod har ett nod-ID som visas i systemvyer. Du kan se Compute Node ID genom att leta efter kolumnen node_id i systemvyer vars namn börjar med sys. pdw_nodes. En lista över dessa systemvyer finns i [MPP system views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

I SQL på begäran tilldelas varje datornod en uppgift och en uppsättning filer att köra uppgiften på. Uppgiften är distribuerad frågekörning, som faktiskt är en del av frågan som användaren skickat. Automatisk skalning används för att se till att tillräckligt många datornoder används för att köra användar frågor.

## <a name="data-movement-service"></a>Data Movement Service

Data flyttnings tjänsten (DMS) är data transport teknik i SQL-poolen som samordnar data förflyttningen mellan Compute-noderna. Vissa frågor kräver data förflyttning för att säkerställa att de parallella frågorna returnerar korrekta resultat. När data förflyttning krävs säkerställer DMS att rätt data får rätt plats.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distributioner

En distribution är den grundläggande enheten för lagring och bearbetning av parallella frågor som körs på distribuerade data i SQL-poolen. När SQL-poolen kör en fråga delas arbetet upp i 60 mindre frågor som körs parallellt. 

Var och en av de 60 mindre frågorna körs på en av data distributionerna. Varje Compute-nod hanterar en eller flera av 60-distributionerna. En SQL-pool med maximala beräknings resurser har en distribution per Compute-nod. En SQL-pool med minsta beräknings resurser har alla distributioner på en Compute-nod. 

## <a name="hash-distributed-tables"></a>Hash-distribuerade tabeller
En hash-distribuerad tabell kan leverera högsta frågeprestanda för kopplingar och aggregeringar för stora tabeller. 

För att Shard data till en hash-distribuerad tabell använder SQL-poolen en hash-funktion för att deterministiskt tilldela varje rad till en distribution. I tabelldefinitionen utses en av kolumnerna till distributionskolumnen. Hash-funktionen använder värdena i distributionskolumnen för att tilldela varje rad till en distribution.

Följande diagram illustrerar hur en fullständig (icke-distribuerad tabell) lagras som en hash-distribuerad tabell. 

![Distribuerad tabell](media//overview-architecture/hash-distributed-table.png "Distribuerad tabell") 

* Varje rad tillhör en distribution. 
* En deterministisk hash-algoritm tilldelar varje rad till en distribution. 
* Antalet tabell rader per distribution varierar så som de visas i olika tabell storlekar.

Det finns prestanda överväganden för valet av en distributions kolumn, till exempel distinkthet, data skevning och de typer av frågor som körs i systemet.

## <a name="round-robin-distributed-tables"></a>Distribuerade tabeller med resursallokering

En Round Robin-tabell är den enklaste tabellen för att skapa och leverera snabba prestanda när den används som en mellanlagrings tabell för belastningar.

En distribuerad tabell med resursallokering distribuerar data jämnt i tabellen men utan ytterligare optimering. En distribution väljs först slumpmässigt och sedan är buffertar för rader kopplade till distributioner sekventiellt. Det går snabbt att läsa in data i en resursallokeringstabell men frågeprestanda kan ofta vara bättre med hash-distribuerade tabeller. Kopplingar i Round-Robin-tabeller kräver reshuffling-data, vilket tar ytterligare tid.

## <a name="replicated-tables"></a>Replikerade tabeller
En replikerad tabell ger snabbaste frågeprestanda för små tabeller.

En tabell som replikeras cachelagrar en fullständig kopia av tabellen på varje Compute-nod. Därför behöver du, när du replikerar en tabell, inte överföra data till beräkningsnoder innan en koppling eller aggregering. Replikerade tabeller används bäst med små tabeller. Extra lagring krävs och det finns ytterligare kostnader som uppstår när du skriver data, vilket gör stora tabeller opraktiska. 

Diagrammet nedan visar en replikerad tabell som cachelagras på den första distributionen på varje Compute-nod. 

![Replikerad tabell](media/overview-architecture/replicated-table.png "Replikerad tabell") 

## <a name="next-steps"></a>Nästa steg

Nu när du vet lite om Synapse SQL, lär dig hur du snabbt [skapar en SQL-pool](../quickstart-create-sql-pool-portal.md) och [läser in exempel data](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-Data-Warehouse-load-Sample-databases.MD). Eller så börjar du [använda SQL på begäran](../quickstart-sql-on-demand.md). Om du inte har erfarenhet av Azure kan [Azure-ordlistan](../../azure-glossary-cloud-terminology.md) vara till hjälp om du stöter på ny terminologi. 
