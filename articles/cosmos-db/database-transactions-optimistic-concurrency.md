---
title: Databastransaktioner och optimistisk samtidighetskontroll i Azure Cosmos DB
description: Den här artikeln beskriver databastransaktioner och optimistisk samtidighetskontroll i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ddd3b7a859e48e3212d8d51d627eea2e69c7c1ff
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024713"
---
# <a name="database-transactions-and-optimistic-concurrency-control"></a>Databastransaktioner och optimistisk samtidighetskontroll

Databastransaktioner ger en säker och förutsägbar programmeringsmodell utan samtidiga ändringar av data. Traditionella relationsdatabaser som SQL Server kan du skriva affärslogik med lagrade procedurer och/eller utlösare, skicka den till servern för körning direkt i databasmotorn. Med traditionella relationsdatabaser du krävs för att hantera två olika programmeringsspråk språk - programmet (icke-transaktionell) programmeringsspråk, till exempel JavaScript, Python, C#, Java, etc. och transaktionell programmeringsspråk (T-SQL) som är internt körs av databasen.

Databasmotorn i Azure Cosmos DB stöder fullständig ACID (Atomicitet, konsekvens, isolering, varaktighet) kompatibla transaktioner med ögonblicksbildisolering. Alla databasåtgärderna inom omfånget för en behållare logisk partition är Verkställ inom databasmotorn som är värd för repliken av partitionen. Dessa åtgärder omfattar både Skriv (uppdaterar ett eller flera objekt i logisk partition) och läs-och skrivåtgärder. I följande tabell visas olika åtgärder och transaktionen typer:

| **Åtgärd**  | **Åtgärdstyp** | **Enda eller flera objekt transaktionen** |
|---------|---------|---------|
| Infoga (utan en före/efter-utlösare) | Skriva | Enskilt objekt transaktion |
| Infoga (med en utlösare för före/efter) | Skriv- och läsåtgärder | Flera objekt transaktion |
| Ersätt (utan en före/efter-utlösare) | Skriva | Enskilt objekt transaktion |
| Ersätt (med en utlösare för före/efter) | Skriv- och läsåtgärder | Flera objekt transaktion |
| Upsert (utan en före/efter-utlösare) | Skriva | Enskilt objekt transaktion |
| Upsert (med en utlösare för före/efter) | Skriv- och läsåtgärder | Flera objekt transaktion |
| Ta bort (utan en före/efter-utlösare) | Skriva | Enskilt objekt transaktion |
| Ta bort (med en utlösare för före/efter) | Skriv- och läsåtgärder | Flera objekt transaktion |
| Kör lagrad procedur | Skriv- och läsåtgärder | Flera objekt transaktion |
| System initierade körningen av en merge-procedur | Skriva | Flera objekt transaktion |
| System initierade körning av att ta bort objekt baserat på upphör att gälla (TTL) för ett objekt | Skriva | Flera objekt transaktion |
| Läsa | Läsa | Single-item transaktion |
| Ändringsfeed | Läsa | Flera objekt transaktion |
| Sidnumrerade Läs | Läsa | Flera objekt transaktion |
| Sidnumrerade fråga | Läsa | Flera objekt transaktion |
| Köra UDF som en del av sidnumrerade frågan | Läsa | Flera objekt transaktion |

## <a name="multi-item-transactions"></a>Flera objekt transaktioner

Azure Cosmos DB kan du skriva lagrade procedurer, utlösare för före/efter, -användardefinierade-funktioner (UDF) och merge procedurer i JavaScript. Azure Cosmos DB stöd har inbyggt för JavaScript-körning i dess databasmotorn. Du kan registrera lagrade procedurer, före/efter utlösare, -användardefinierade-funktioner (UDF) och sammanfoga procedurer i en behållare och senare köra dem transaktionellt i Azure Cosmos-databasmotorn. Skriva programlogiken i JavaScript kan naturlig implementering av Kontrollflöde, variabel omfång, tilldelning och integrering av undantagshantering primitiver i databastransaktioner direkt på språket som JavaScript.

JavaScript-baserade lagrade procedurer, utlösare, UDF: er och sammanslagning procedurer är omslutna inom en omgivande ACID-transaktion med ögonblicksbildisolering över alla objekt i logisk partition. Under körningen, om programmet JavaScript genererar ett undantag hela transaktionen avbryts och återställas tillbaka. Den resulterande programmeringsmodellen är enkla men ändå kraftfull. JavaScript-utvecklare få en hållbar programmeringsmodell medan du fortfarande använder sitt välbekanta språk konstruerar och biblioteket primitiver.

Möjligheten att köra JavaScript direkt i databasmotorn ger prestanda och transaktionell körning av databasåtgärder mot objekten i en behållare. Eftersom Azure Cosmos-databasmotorn har inbyggt stöd för JSON och JavaScript, finns det inga impedans matchningsfel mellan system som typ av ett program och databasen.

## <a name="optimistic-concurrency-control"></a>Optimistisk samtidighetskontroll 

Optimistisk samtidighetskontroll kan du förhindra att förlorade uppdateringar och tar bort. Samtidiga, motstridiga operationer genomgår regelbundna pessimistisk låsning av databasmotorn som värd för logisk partition som äger objektet. När två samtidiga åtgärder som försöker uppdatera den senaste versionen av ett objekt i en logisk partition, en av dem ska ha och den andra misslyckas. Men om en eller två åtgärder försöker uppdatera samma objekt samtidigt hade tidigare läst en äldre värdet för objektet, databasen inte vet om det tidigare läsa värdet av ett eller båda motstridiga åtgärder var verkligen det senaste värdet för objektet. Som tur är kan kan den här situationen identifieras med Optimistisk samtidighet kontroll (OCC) innan du ger de två åtgärderna ange transaktionen gränsen i databasmotorn. OCC skyddar dina data oavsiktligt skriver över ändringar som gjorts av andra. Det förhindrar också andra skrivs över dina egna ändringar av misstag.

Samtidiga uppdateringar av ett objekt har utsatts för OCC av Azure Cosmos DB-protokollet kommunikationslagret. Azure Cosmos-databasen garanterar att klientsidan-versionen av objektet som du uppdaterar (eller tar bort) är samma som versionen av objektet i Azure Cosmos-behållaren. Detta garanterar att dina skrivåtgärder skyddas från att av misstag skrivas över av skrivningar andra och vice versa. I en miljö med flera användare skyddar dig mot råkar ta bort eller uppdatera fel version av ett objekt i kontrollen Optimistisk samtidighet. Därför är objekt skyddade mot ökända ”förlorad update” eller ”förlorad delete” problem.

Varje objekt som lagras i en Azure Cosmos-behållare har ett system som definierats `_etag` egenskapen. Värdet för den `_etag` genereras och uppdateras av servern varje gång objektet uppdateras automatiskt. `_etag` kan användas med sidhuvudet för klienten som angetts if-match-begäran så att servern för att avgöra om ett objekt kan uppdateras villkorligt. Värdet för if-match-huvudet matchar värdet för den `_etag` på servern, uppdateras sedan objektet. Om värdet för if-match-huvudet för begäran inte längre är aktuell avvisar servern igen med ett ”HTTP 412 Förutsättningsfel” svarsmeddelande. Klienten sedan kan återigen hämta objektet om du vill hämta den aktuella versionen av artikeln på servern eller åsidosätta versionen av objektet i servern med sin egen `_etag` värdet för objektet. Dessutom `_etag` kan användas med if-none-match-huvud för att avgöra om en återigen hämta en resurs är nödvändigt. 

Objektets _etag värdet ändras varje gång objektet uppdateras. För Ersätt objekt uttryckas if-match uttryckligen som en del av begäran alternativen. Ett exempel finns i exempelkoden i [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag` värden kontrolleras implicit för alla skriftliga objekt som berörs av en lagrad procedur. Om eventuella konflikter identifieras den lagrade proceduren återställs transaktionen och utlöser ett undantag. Med den här metoden tillämpas alla eller inga skrivningar i den lagrade proceduren atomiskt. Det här är en signal till programmet att tillämpa uppdateringar och gör om den ursprungliga klientbegäran.

## <a name="next-steps"></a>Nästa steg

Läs mer om databastransaktioner och optimistisk samtidighetskontroll i följande artiklar:

- [Arbeta med Azure Cosmos-databaser, behållare och objekt](databases-containers-items.md)
- [Konsekvensnivåer](consistency-levels.md)
- [Konflikt typer och principer för lösning](conflict-resolution-policies.md)
