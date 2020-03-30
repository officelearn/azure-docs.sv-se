---
title: Databastransaktioner och optimistisk samtidighetskontroll i Azure Cosmos DB
description: I den här artikeln beskrivs databastransaktioner och optimistisk samtidighetskontroll i Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: d453bb4071c4a6972e01b8f7e90375181caf6d01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806532"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Kontroll över transaktioner och optimistisk samtidighet

Databastransaktioner ger en säker och förutsägbar programmeringsmodell för att hantera samtidiga ändringar av data. Traditionella relationsdatabaser, som SQL Server, gör att du kan skriva affärslogiken med hjälp av lagrade procedurer och/eller utlösare, skicka den till servern för körning direkt i databasmotorn. Med traditionella relationsdatabaser måste du hantera två olika programmeringsspråk (icke-transaktionella) programmeringsspråk som JavaScript, Python, C#, Java, etc. och det transaktionsbaserade programmeringsspråket (till exempel T-SQL) som körs inbyggt av databasen.

Databasmotorn i Azure Cosmos DB stöder fullständiga SYRA-, (Atomicity, Consistency, Isolation, Durability) -kompatibla transaktioner med isolering av ögonblicksbilder. Alla databasåtgärder inom ramen för en behållares [logiska partition](partition-data.md) körs transaktionsmässigt i databasmotorn som är värd för partitionens replik. Dessa åtgärder omfattar både skriva (uppdatera en eller flera objekt inom den logiska partitionen) och läsåtgärder. I följande tabell visas olika åtgärder och transaktionstyper:

| **Drift**  | **Åtgärdstyp** | **En eller flera objekt transaktion** |
|---------|---------|---------|
| Infoga (utan för-/inläggsutlösare) | Skriva | Transaktion med enstaka artiklar |
| Infoga (med en pre/post-utlösare) | Skriva och läsa | Transaktion med flera artiklar |
| Byt ut (utan för-/inläggsutlösare) | Skriva | Transaktion med enstaka artiklar |
| Ersätt (med en pre/post-utlösare) | Skriva och läsa | Transaktion med flera artiklar |
| Upsert (utan en pre/post-utlösare) | Skriva | Transaktion med enstaka artiklar |
| Upsert (med en pre/post-utlösare) | Skriva och läsa | Transaktion med flera artiklar |
| Ta bort (utan en utlösare före/efter) | Skriva | Transaktion med enstaka artiklar |
| Ta bort (med en pre/post-utlösare) | Skriva och läsa | Transaktion med flera artiklar |
| Utföra lagrad procedur | Skriva och läsa | Transaktion med flera artiklar |
| Systemet initierade körningen av en sammanslagningsprocedur | Skriva | Transaktion med flera artiklar |
| Systeminitierad körning av borttagning av artiklar baserat på förfallodatum (TTL) för en artikel | Skriva | Transaktion med flera artiklar |
| Läsa | Läsa | Transaktion med en artikel |
| Ändringsflöde | Läsa | Transaktion med flera artiklar |
| Sidnumrerad Läsa | Läsa | Transaktion med flera artiklar |
| Sidnumrerad fråga | Läsa | Transaktion med flera artiklar |
| Kör UDF som en del av den sidnumrerade frågan | Läsa | Transaktion med flera artiklar |

## <a name="multi-item-transactions"></a>Transaktioner med flera artiklar

Med Azure Cosmos DB kan du skriva [lagrade procedurer, före/publicera utlösare, användardefinierade funktioner (UDFs)](stored-procedures-triggers-udfs.md) och kopplingsprocedurer i JavaScript. Azure Cosmos DB stöder inbyggt JavaScript-körning i databasmotorn. Du kan registrera lagrade procedurer, före/efter-utlösare, användardefinierade funktioner (UDFs) och sammanfoga procedurer på en behållare och senare köra dem transaktionellt i Azure Cosmos databasmotor. Genom att skriva programlogik i JavaScript kan du skapa ett naturligt uttryck för kontrollflöde, variabel omfång, tilldelning och integrering av undantagshanteringsprimitiver i databastransaktionerna direkt på JavaScript-språket.

De JavaScript-baserade lagrade procedurerna, utlösarna, UDF:erna och sammanslagningsprocedurerna är insvepta i en ambient ACID-transaktion med isolering av ögonblicksbilder över alla objekt i den logiska partitionen. Under körningen, om JavaScript-programmet genererar ett undantag, avbryts hela transaktionen och återställs. Den resulterande programmeringsmodellen är enkel men ändå kraftfull. JavaScript-utvecklare får en hållbar programmeringsmodell samtidigt som de använder sina välbekanta språkkonstruktioner och biblioteks primitiver.

Möjligheten att köra JavaScript direkt i databasmotorn ger prestanda och transaktionskörning av databasåtgärder mot objekten i en behållare. Eftersom Azure Cosmos databasmotor internt stöder JSON och JavaScript finns det ingen obalans mellan typsystemen för ett program och databasen.

## <a name="optimistic-concurrency-control"></a>Optimistisk samtidighetskontroll

Med optimistisk samtidighetskontroll kan du förhindra förlorade uppdateringar och borttagningar. Samtidigt utsätts motstridiga åtgärder för regelbunden pessimistisk låsning av databasmotorn som finns i den logiska partitionen som äger objektet. När två samtidiga åtgärder försöker uppdatera den senaste versionen av ett objekt i en logisk partition, kommer en av dem att vinna och den andra misslyckas. Men om en eller två åtgärder som försöker uppdatera samma objekt samtidigt hade läst ett äldre värde för objektet, vet databasen inte om det tidigare läsvärdet av någon eller båda de motstridiga åtgärderna verkligen var det senaste värdet för objektet. Lyckligtvis kan den här situationen identifieras med **OCC (Optimistic Concurrency Control)** innan de två operationerna kan ange transaktionsgränsen inuti databasmotorn. OCC skyddar dina data från att av misstag skriva över ändringar som har gjorts av andra. Det förhindrar också att andra av misstag skriver över dina egna ändringar.

Samtidiga uppdateringar av ett objekt utsätts för OCC av Azure Cosmos DB:s kommunikationsprotokolllager. Azure Cosmos-databasen säkerställer att klientversionen av objektet som du uppdaterar (eller tar bort) är samma som versionen av objektet i Azure Cosmos-behållaren. Detta garanterar att dina skrivningar skyddas från att skrivas över av misstag av andras skrivningar och vice versa. I en miljö med flera användare skyddar den optimistiska samtidighetskontrollen dig från att av misstag ta bort eller uppdatera fel version av ett objekt. Som sådan är objekt skyddade mot den ökända "förlorade uppdateringen" eller "lost delete" problem.

Varje objekt som lagras i en Azure `_etag` Cosmos-behållare har en systemdefinierad egenskap. Värdet på `_etag` objektet genereras och uppdateras automatiskt av servern varje gång objektet uppdateras. `_etag`kan användas med klientens begärandehuvud `if-match` så att servern kan avgöra om ett objekt kan uppdateras villkorligt. Värdet för `if-match` huvudet matchar värdet `_etag` på servern, objektet uppdateras sedan. Om värdet för `if-match` begäranden inte längre är aktuellt avvisar servern åtgärden med ett svarsmeddelande om "HTTP 412-förkonditioneringsfel". Klienten kan sedan hämta objektet igen för att hämta den aktuella versionen av objektet på `_etag` servern eller åsidosätta den version av objektet på servern som har ett eget värde för objektet. Dessutom `_etag` kan användas med `if-none-match` huvudet för att avgöra om en refetch av en resurs behövs.

Artikelns värde `_etag` ändras varje gång artikeln uppdateras. För ersättningsartikeloperationer `if-match` måste uttryckligen uttryckas som en del av alternativen för begäran. Ett exempel finns i exempelkoden i [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L578-L674). `_etag`värden kontrolleras implicit för alla skriftliga objekt som berörs av den lagrade proceduren. Om någon konflikt upptäcks återställs transaktionen och ett undantag genereras. Med denna metod tillämpas antingen alla eller inga skrivningar inom det lagrade förfarandet atomärt. Detta är en signal till programmet för att återansöka uppdateringar och försöka igen den ursprungliga klientbegäran.

## <a name="next-steps"></a>Nästa steg

Läs mer om databastransaktioner och optimistisk samtidighetskontroll i följande artiklar:

- [Arbeta med Azure Cosmos-databaser, behållare och objekt](databases-containers-items.md)
- [Konsekvensnivåer](consistency-levels.md)
- [Konflikttyper och matchningsprinciper](conflict-resolution-policies.md)
- [Lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md)
