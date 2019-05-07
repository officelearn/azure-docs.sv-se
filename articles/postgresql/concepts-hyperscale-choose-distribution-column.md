---
title: Välj distributionskolumner i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)
description: Bra val för distributionskolumner i vanliga scenarier i hyperskala
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078992"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Välj distributionskolumner i Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion)

Är att välja varje tabell distributionskolumn **något av de viktigaste** modellering beslut. Hyperskala lagrar rader i fragment som baseras på värdet för de rader distributionskolumn.

De rätta val grupperna relaterade data samlas på samma fysiska noder, vilket gör frågor snabba och lägger till stöd för alla SQL-funktioner. Ett felaktigt val blir systemet körs långsamt och inte stöder alla SQL-funktioner för noder.

Det här avsnittet ger distribution kolumnen tips för de två vanligaste scenarierna hyperskala.

### <a name="multi-tenant-apps"></a>Appar för flera klienter

Arkitektur med flera klienter använder en form av hierarkisk databas modellering för att distribuera frågor över noder i servergruppen.  Överst i data-hierarkin kallas den *klient-ID*, och som inte ska lagras i en kolumn i varje tabell.

Hyperskala inspekterar frågor för att se vilka klient-ID de omfattar och söker efter den matchande tabell sharden. Frågan dirigeras till en enskild worker-nod som innehåller fragmentet. Köra en fråga med alla relevanta data placeras på samma nod kallas samordning.

Följande diagram illustrerar samordning i datamodellen för flera innehavare. Den innehåller tabellerna, konton och kampanjer, var och en distribuerad av `account_id`. De skuggade rutorna representerar shards, vars färg representerar vilka arbetsnod innehåller den. Grön shards lagras tillsammans på en underordnad nod och blå på en annan. Observera hur en join-fråga mellan konton och kampanjer skulle ha alla nödvändiga data tillsammans på en nod när du begränsar båda tabellerna på samma konto\_id.

![samordning av flera innehavare](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Identifiera vad som utgör en klient i ditt program om du vill använda den här designen i ditt eget schema. Vanliga instanser omfattar företaget, konto, organisation eller kund. Kolumnnamnet blir något som liknar `company_id` eller `customer_id`. Undersöka var och en av dina frågor och fråga dig själv: skulle det fungera om den hade ytterligare WHERE-satserna att begränsa alla tabeller som ingår till rader med samma klient-ID?
Frågorna i modellen för flera innehavare är begränsade till en klient, till exempel frågor om försäljning eller inventering skulle vara begränsad inom ett visst Arkiv.

#### <a name="best-practices"></a>Metodtips

-   **Partition distribuerade tabeller av en gemensam klient\_id-kolumnen.** Till exempel på ett SaaS-program där klienter är företag, klienten\_id kommer sannolikt att företagets\_id.
-   **Konvertera små tabeller för flera klienter till referenstabeller.** När flera klienter delar en liten tabell med information kan du distribuera den som en tabell.
-   **Begränsa filtrera alla programmet frågar efter klient\_id.** Varje fråga bör begära information för en klient i taget.

Läs den [flera innehavare självstudien](./tutorial-design-database-hyperscale-multi-tenant.md) ett exempel på att skapa den här typen av program.

### <a name="real-time-apps"></a>Appar i realtid

Arkitektur med flera innehavare introducerar en hierarkisk struktur och använder data samordning som dirigerar frågor per klient. Däremot beror i realtid arkitekturer på specifik distribution egenskaper för sina data att uppnå hög parallell bearbetning.

Vi använder ”entitets-ID” som en term för distributionskolumner i modellen i realtid. Vanliga entiteter är användare, värdar eller enheter.

Förfrågningar i realtid be vanligtvis för numeriska mängder grupperade efter datum eller kategori. Hyperskala skickar dessa frågor till varje shard för ofullständiga resultat och monterar det slutliga svaret på koordinatornoden. Frågor köras snabbaste när många noder bidra som möjligt och när ingen enskild nod måste göra en oproportionerlig mängd arbete.

#### <a name="best-practices"></a>Metodtips

-   **Välj en kolumn med hög kardinalitet som kolumnen distribution.** Jämförelse, en \"status\" fältet på en order-tabell med värden som ”nytt”, ”avgiftsbelagt” och ”levereras” är ett dåligt val av distributionskolumn. Den förutsätter att endast de få värdena, vilket begränsar antalet shards som kan innehålla data och antalet noder som kan bearbeta den. Det är bra dessutom för att välja de som används ofta i group by-satser eller som koppling nycklar mellan kolumner med hög kardinalitet.
-   **Välj en kolumn med jämn fördelning.** Om du distribuerar en tabell på en kolumn som påverkas av att vissa gemensamma värden, kommer data i tabellen tenderar att ackumuleras i vissa fragment. Noder som innehåller dessa fragment ska få göra mer arbete än andra noder.
-   **Distribuera fakta- och dimensionstabeller tabeller på deras gemensamma kolumner.**
    Din faktatabell kan ha endast en distributionsnyckeln. Tabeller som ansluter till på en annan nyckel kan inte samordnat med faktatabellen. Välj en dimension samplacera baserat på hur ofta den är ansluten och storleken på de anslutande raderna.
-   **Ändra vissa dimensionstabeller i referenstabeller.** Om en dimensionstabell kan inte samplaceras med faktatabellen, kan du förbättra frågeprestanda genom att distribuera kopior av dimensionstabell till alla noder i form av en tabell.

Läs den [realtidsinstrumentpanel självstudien](./tutorial-design-database-hyperscale-realtime.md) ett exempel på att skapa den här typen av program.

### <a name="timeseries-data"></a>Din

I en time series-arbetsbelastning fråga program och aktuell information under arkivering gammal information.

De vanligaste fel i modellering din information i hyperskala använder tidsstämpeln sig själv som en distributionskolumn. En hash-distribution som baseras på tiden kommer att distribuera gånger till synes slumpmässigt i olika shards i stället för att hålla intervall tid tillsammans i fragment. Frågor som rör tid vanligtvis refererar till adressintervallen tid (till exempel de senaste data), så hash utdelningen skulle leda till nätverks-overhead.

#### <a name="best-practices"></a>Metodtips

-   **Välj inte en tidsstämpel som kolumnen distribution.** Välj en annan distributionsplats kolumn. I en app för flera klienter med klient-ID eller i en i realtid app använder entitets-ID.
-   **Använd PostgreSQL Tabellpartitionering för tid i stället.** Använd Tabellpartitionering för att dela upp en stor tabell tidsordnad data i flera ärvda tabeller med var och en som innehåller olika tidsintervall.  Distribuera en Postgres-partitionerad tabell i hyperskala skapar fragment för ärvda tabeller.

Läs den [timeseries självstudien](https://aka.ms/hyperscale-tutorial-timeseries) ett exempel på att skapa den här typen av program.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur [samordning](concepts-hyperscale-colocation.md) mellan distribuerade data hjälper till att frågor körs snabbt
