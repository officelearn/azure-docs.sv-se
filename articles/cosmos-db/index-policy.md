---
title: Azure Cosmos DB indexeringsprinciper
description: Förstå hur indexering fungerar i Azure Cosmos DB. Lär dig mer om att konfigurera och ändra indexeringsprincip för automatisk indexering och bättre prestanda.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 6998db1679e67f8ac4bf7c81ea9373c66a9618ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278572"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Index principen i Azure Cosmos DB

Du kan åsidosätta standardinställningen indexeringspolicy på en Azure Cosmos-behållare genom att konfigurera följande parametrar:

* **Inkludera eller exkludera objekt och sökvägar från indexet**: Du kan exkludera eller innehålla vissa objekt i indexet, när du infoga eller ersätta objekt i en behållare. Du kan även inkludera eller exkludera specifika sökvägar/egenskaper som ska indexeras över behållare. Sökvägar kan innehålla jokerteckensmönster, till exempel *.

* **Konfigurera index typer**: Dessutom för att vara indexerade sökvägar, du kan lägga till andra typer av index, till exempel spatial.

* **Konfigurera index lägen**: Med indexprincip i en behållare kan du konfigurera olika lägen för indexering som *konsekvens* eller *ingen*.

## <a name="indexing-modes"></a>Indexering lägen

Azure Cosmos DB stöder två indexering lägen som du kan konfigurera i en Azure Cosmos-behållare via indexeringsprincip:

* **Konsekvent**: Om en Azure Cosmos-behållare principen är konfigurerad att *konsekvens*, frågor på en specifik behållare följer samma konsekvensnivå som angav för punkt-läsningar (exempel: stark, bunden utgång, session eller eventuell). 

  Indexet har uppdaterats synkront när du uppdaterar objekt. Till exempel resulterar infoga, Ersätt, uppdatera och ta borttagningsåtgärder på ett objekt i index-uppdateringen. Konsekvent indexering stöder konsekvent frågor bekostnad påverkar genomströmning för skrivning. Minskad genomströmning för skrivning är beroende av de ”sökvägar som ingår i indexet” och ”konsekvensnivå”. Konsekvent indexering läge är avsett att hålla index uppdaterade med alla uppdateringar och att direkt hantera frågor.

* **Ingen**: En behållare som har en ingen indexläge har inget index som är kopplade till den. Detta används ofta om Azure Cosmos-databasen används som en nyckel / värde-lagring och objekt används endast av deras ID-egenskap.

  > [!NOTE]
  > Konfigurera indexering läge som en *ingen* har att släppa alla befintliga index. Du bör använda det här alternativet om din åtkomstmönster kräver ID eller självlänken endast.

Fråga konsekvensnivåer bevaras liknar regelbundna läsåtgärder. Azure Cosmos-databasen returnerar ett fel om du frågar behållaren som innehåller en *ingen* indexering läge. Du kan köra frågor som genomsökningar via den explicita **x-ms-documentdb-enable-genomsökning** huvud i REST API eller **EnableScanInQuery** begära alternativet med hjälp av .NET SDK. Vissa fråga funktioner, som ORDER BY inte stöds för närvarande med **EnableScanInQuery**, eftersom de utförda ett motsvarande index.

## <a name="modifying-the-indexing-policy"></a>Ändra indexprincip

Du kan uppdatera indexeringsprincip för en behållare när som helst i Azure Cosmos DB. En ändring i indexeringsprincip på en Azure Cosmos-behållare kan leda till en ändring i form av indexet. Den här ändringen påverkar sökvägarna som kan indexeras, deras precision och konsekvensmodell indexets själva. En ändring i indexeringspolicy effektivt kräver en omvandling av gamla index till ett nytt index.

### <a name="index-transformations"></a>Index omvandlingar

Alla omvandlingar index online. De objekt som indexeras per gamla principen omvandlas effektivt per den nya principen utan att påverka tillgängligheten för skrivning eller dataflödet som tillhandahållits för behållaren. Konsekvens läsa och skriva åtgärderna som utförs med hjälp av REST-API, SDK: er, eller med hjälp av lagrade procedurer och utlösare påverkas inte under index omvandling.

Ändra indexeringsprincip är en asynkron åtgärd och tiden för att slutföra åtgärden beror på hur många objekt, dataflöde och storleken på objekt. Medan omindexering pågår, frågan kanske inte returnerar alla matchande resultat om frågorna råkar använda indexet som ändras och frågorna som inte returnerar eventuella fel/fel. Medan omindexering pågår, är frågorna konsekvent oavsett hur indexering läge. När indexet fortsätter omvandlingen är klar, du att se enhetliga resultat. Detta gäller för frågor som utfärdats av gränssnitt som REST-API, SDK: er, eller lagrade procedurer och utlösare. Omvandling av indexet utförs asynkront, i bakgrunden på repliker med hjälp av ledig resurserna som är tillgängliga för specifika repliker.

Alla index transformeringar görs på plats. Azure Cosmos DB har inte två kopior av indexet. Inga ytterligare diskutrymme är så krävs eller konsumeras i dina behållare medan index omvandling inträffar.

När du ändrar indexprincip ändringar tillämpas för att flytta från det gamla indexet till det nya indexet och baserat främst på indexering läge konfigurationer. Indexering läge konfigurationer spelar en stor roll jämfört med andra egenskaper som inkluderad/exkluderad sökvägar, index-typer och precision.

Om både gamla och nya indexering principer **konsekvens** indexering, Azure Cosmos-databasen utför en omvandling med online-index. Du kan inte använda en annan indexering ändring som har konsekventa indexering läge medan transformeringen pågår. När du flyttar till ingen indexering läge på indexet togs bort omedelbart. Flytta till ingen är användbart när du vill avbryta en pågående omvandling och börja från början med en annan indexeringsprincip.

## <a name="modifying-the-indexing-policy---examples"></a>Ändra indexeringsprincip - exempel

Följande är vanliga användningsområden när du vill uppdatera en indexeringsprincip:

* Om du vill ha enhetliga resultat under normal drift, men återställde den **ingen** indexering läge under bulk dataimporter.

* Om du vill börja använda indexering funktioner på dina aktuella Azure-Cosmos-behållare. Du kan till exempel använda geospatiala frågor, vilket kräver spatialindexet typ eller ORDER BY / sträng intervallfrågor som kräver sträng intervallet index typ.

* Om du vill manuellt väljer du egenskaperna som ska indexeras och ändra dem över tid för att justera dina arbetsbelastningar.

* Om du vill justera indexering precisionen att förbättra prestanda för frågor eller minska förbrukad lagring.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

* [Indexering, översikt](index-overview.md)
* [Indextyper](index-types.md)
* [Indexsökvägar](index-paths.md)
* [Så här hanterar du indexeringsprincip](how-to-manage-indexing-policy.md)
