---
title: Välj distributionskolumner – Hyperskala (Citus) – Azure-databas för PostgreSQL
description: Lär dig hur du väljer distributionskolumner i vanliga hyperskalescenarier i Azure Database för PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8ced9767d81affceef851820ee587f4f3dd24deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975677"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Välj distributionskolumner i Azure Database för PostgreSQL – Hyperskala (Citus)

Att välja varje tabells distributionskolumn är ett av de viktigaste modelleringsbesluten du ska fatta. Azure Database for PostgreSQL – Hyperscale (Citus) lagrar rader i shards baserat på värdet i radernas distributionskolumn.

Rätt valgrupper relaterade data tillsammans på samma fysiska noder, vilket gör frågor snabba och lägger till stöd för alla SQL-funktioner. Ett felaktigt val gör att systemet går långsamt och stöder inte alla SQL-funktioner över noder.

Den här artikeln ger tips för distributionskolumnen för de två vanligaste scenarierna hyperskala (Citus).

### <a name="multi-tenant-apps"></a>Appar för flera klienter

Arkitekturen för flera innehavare använder en form av hierarkisk databasmodellering för att distribuera frågor mellan noder i servergruppen. Överst i datahierarkin kallas *klient-ID* och måste lagras i en kolumn i varje tabell.

Hyperskala (Citus) inspekterar frågor för att se vilket klient-ID de involverar och hittar den matchande tabellsvantumvan. Frågan dirigeras till en enda arbetsnod som innehåller fragmentet. Köra en fråga med alla relevanta data placeras på samma nod kallas samlokalisering.

Följande diagram illustrerar samlokalisering i datamodellen för flera innehavare. Den innehåller två tabeller, Konton och `account_id`Kampanjer, var och en distribueras av . De skuggade rutorna representerar shards. Gröna shards lagras tillsammans på en arbetsnod och blå shards lagras på en annan arbetsnod. Lägg märke till hur en kopplingsfråga mellan konton och kampanjer har alla nödvändiga\_data tillsammans på en nod när båda tabellerna är begränsade till samma konto-ID.

![Samlokalisering med flera innehavare](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Om du vill använda den här designen i ditt eget schema identifierar du vad som utgör en klient i ditt program. Vanliga instanser är företag, konto, organisation eller kund. Kolumnnamnet blir något `company_id` liknande `customer_id`eller . Undersök var och en av dina frågor och fråga dig själv, skulle det fungera om det hade ytterligare WHERE-satser för att begränsa alla tabeller inblandade till rader med samma klient-ID?
Frågor i modellen med flera innehavare begränsas till en klient. Frågor om försäljning eller lager begränsas till exempel i en viss butik.

#### <a name="best-practices"></a>Bästa praxis

-   **Partition distribuerade tabeller\_av en gemensam klient-ID kolumn.** Till exempel i ett SaaS-program där\_klienter är företag, är\_klient-ID sannolikt företagets id.
-   **Konvertera små tabeller mellan innehavare till referenstabeller.** När flera klienter delar en liten informationstabell distribuerar du den som en referenstabell.
-   **Begränsa filtrering av\_alla programfrågor efter klient-ID.** Varje fråga bör begära information för en klient i taget.

Läs [självstudien](./tutorial-design-database-hyperscale-multi-tenant.md) för flera innehavare för ett exempel på hur du skapar den här typen av program.

### <a name="real-time-apps"></a>Realtidsappar

Arkitekturen för flera innehavare introducerar en hierarkisk struktur och använder datasamlokalisering för att dirigera frågor per klient. Däremot är realtidsarkitekturer beroende av specifika distributionsegenskaper för sina data för att uppnå mycket parallell bearbetning.

Vi använder "entitets-ID" som en term för distributionskolumner i realtidsmodellen. Typiska entiteter är användare, värdar eller enheter.

Realtidsfrågor frågar vanligtvis efter numeriska aggregat grupperade efter datum eller kategori. Hyperskala (Citus) skickar dessa frågor till varje fragment för partiella resultat och sammanställer det slutliga svaret på koordinatornsnoden. Frågor körs snabbast när så många noder bidrar som möjligt och när ingen enskild nod måste utföra en oproportionerligt stor mängd arbete.

#### <a name="best-practices"></a>Bästa praxis

-   **Välj en kolumn med hög kardinalitet som distributionskolumn.** Som jämförelse är ett statusfält i en ordertabell med värdena Nytt, Betalt och Levererat ett dåligt val av distributionskolumn. Det förutsätter bara de få värden, vilket begränsar antalet shards som kan innehålla data, och antalet noder som kan bearbeta den. Bland kolumner med hög kardinalitet är det också bra att välja de kolumner som ofta används i grupp-för-satser eller som kopplingsnycklar.
-   **Välj en kolumn med jämn fördelning.** Om du distribuerar en tabell i en kolumn som är skev till vissa vanliga värden tenderar data i tabellen att ackumuleras i vissa shards. Noderna som håller dessa shards sluta göra mer arbete än andra noder.
-   **Fördela fakta- och dimensionstabeller på deras gemensamma kolumner.**
    Din faktatabell kan bara ha en distributionsnyckel. Tabeller som går med på en annan nyckel kommer inte att samlokaliseras med faktatabellen. Välj en dimension för att samlokalisera baserat på hur ofta den är sammanfogad och storleken på de kopplade raderna.
-   **Ändra vissa dimensionstabeller till referenstabeller.** Om en dimensionstabell inte kan samlokaliseras med faktatabellen kan du förbättra frågeprestanda genom att distribuera kopior av dimensionstabellen till alla noder i form av en referenstabell.

Läs [instrumentpanelshandledningen](./tutorial-design-database-hyperscale-realtime.md) i realtid för ett exempel på hur du skapar den här typen av program.

### <a name="time-series-data"></a>Tidsseriedata

I en arbetsbelastning i tidsserier frågar program den senaste informationen medan de arkiverar gammal information.

Det vanligaste misstaget vid modellering av tidsserieinformation i Hyperscale (Citus) är att använda själva tidsstämpeln som distributionskolumn. En hash-distribution baserad på tid distribuerar gånger till synes slumpmässigt i olika shards i stället för att hålla tidsintervall tillsammans i shards. Frågor som involverar tid i allmänhet referensintervall, till exempel de senaste data. Den här typen av hash-distribution leder till nätverksomkostnader.

#### <a name="best-practices"></a>Bästa praxis

-   **Välj inte en tidsstämpel som distributionskolumn.** Välj en annan distributionskolumn. I en app med flera innehavare använder du klient-ID:t eller använder entitets-ID i realtid.
-   **Använd PostgreSQL-tabellpartitionering för tid i stället.** Använd tabellpartitionering för att dela upp en stor tabell med tidsbeställda data i flera ärvda tabeller med varje tabell som innehåller olika tidsintervall. Om du distribuerar en Postgres-partitionerad tabell i Hyperscale (Citus) skapas shards för de ärvda tabellerna.

Läs [tidsseriehandledningen](https://aka.ms/hyperscale-tutorial-timeseries) för ett exempel på hur du bygger den här typen av program.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur [samlokalisering](concepts-hyperscale-colocation.md) mellan distribuerade data hjälper frågor att köras snabbt.
