---
title: Azure Cosmos DB indexeringsprinciper
description: Förstå hur indexering fungerar i Azure Cosmos DB. Lär dig mer om att konfigurera och ändra indexeringsprincip för automatisk indexering och bättre prestanda.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: 2153f0a16df9e79b3f5324ce19880e2708855196
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848008"
---
# <a name="indexing-policy-in-azure-cosmos-db"></a>Indexeringsprincip i Azure Cosmos DB

Du kan åsidosätta standardinställningen indexeringspolicy på en Azure Cosmos-behållare genom att konfigurera följande parametrar:

* **Inkludera eller exkludera objekt och sökvägar från indexet**: du kan exkludera eller innehålla vissa objekt i indexet när du infoga eller ersätta objekt i en behållare. Du kan även inkludera eller exkludera specifika sökvägar/egenskaper som ska indexeras över behållare. Sökvägar kan innehålla jokerteckensmönster, till exempel *.

* **Konfigurera index typer**: dessutom för att vara indexerade sökvägar, du kan lägga till andra typer av index som spatial.

* **Konfigurera index lägen**: med indexprincip i en behållare kan du konfigurera olika lägen för indexering som *konsekvens* eller *ingen*.

## <a name="indexing-modes"></a>Indexering lägen 

Azure Cosmos DB stöder två indexering lägen som du kan konfigurera ett Azure Cosmos-behållaren. Du kan konfigurera följande två indexering lägen via indexprincip: 

* **Konsekvent**: om en Azure Cosmos-behållare principen är konfigurerad att konsekvens, frågor på en specifik behållare följer du samma konsekvensnivå som angav för punkt-läsningar (exempel: stark, bunden utgång, session eller eventuell). 

  Indexet har uppdaterats synkront när du uppdaterar objekt. Till exempel resulterar infoga, Ersätt, uppdatera och ta borttagningsåtgärder på ett objekt i index-uppdateringen. Konsekvent indexering stöder konsekvent frågor bekostnad påverkar genomströmning för skrivning. Minskad genomströmning för skrivning beror på den ”sökvägar ingår i indexering” och ”konsekvensnivå”. Konsekvens indexering läge är utformad för skrivning snabbt och fråga direkt arbetsbelastningar.

* **Ingen**: en behållare som har en ingen indexläge har inget index som är kopplade till den. Detta används ofta om Azure Cosmos-databasen används som en nyckel / värde-lagring och objekt används endast av deras ID-egenskap.

  > [!NOTE]
  > Konfigurera indexering läge som en ingen har att släppa alla befintliga index. Du bör använda det här alternativet om din åtkomstmönster kräver ID eller självlänken endast.

Fråga konsekvensnivåer bevaras liknar regelbundna läsåtgärder. Azure Cosmos-databasen returnerar ett fel om du frågar behållaren som innehåller en ingen indexering läge. Du kan köra frågor som genomsökningar via den explicita **x-ms-documentdb-enable-genomsökning** huvud i REST API eller **EnableScanInQuery** begära alternativet med hjälp av .NET SDK. Vissa fråga funktioner, som ORDER BY inte stöds för närvarande med **EnableScanInQuery**, eftersom de utförda ett motsvarande index.

## <a name="modifying-the-indexing-policy"></a>Ändra indexprincip

Du kan uppdatera indexeringsprincip för en behållare när som helst i Azure Cosmos DB. En ändring i indexeringspolicy på en Azure Cosmos-behållare kan leda till en ändring i form av indexet. Den här ändringen påverkar sökvägarna som kan indexeras, deras precision och konsekvensmodell indexets själva. En ändring i indexeringspolicy effektivt kräver en omvandling av gamla index till ett nytt index.

### <a name="index-transformations"></a>Index omvandlingar

Alla omvandlingar index online. De objekt som indexeras per gamla principen omvandlas effektivt per den nya principen utan att påverka tillgängligheten för skrivning eller dataflödet som tillhandahållits till behållaren. Konsekvenskontroll av läser och skriver åtgärderna som utförs med hjälp av REST-API, SDK: er, eller från lagrade procedurer och utlösare är inte påverkas under index omvandling.

Ändra indexeringsprincip är en asynkron åtgärd och tiden för att slutföra åtgärden beror på hur många objekt, etablerat dataflöde och storleken på objekt. Medan omindexering pågår, frågan kanske inte returnerar alla matchande resultat om frågorna råkar använda indexet som ändras och frågor returnerar inte eventuella fel/fel. Medan omindexering pågår, är frågorna konsekvent oavsett hur indexering läge. När indexet fortsätter omvandlingen är klar, du att se enhetliga resultat. Detta gäller för frågor som utfärdats av gränssnitt som REST-API, SDK: er, eller lagrade procedurer och utlösare. Omvandling av indexet utförs asynkront i bakgrunden på replikerna med hjälp av ledig resurserna som är tillgängliga för en specifik replik.

Alla index transformeringar görs på plats. Azure Cosmos DB har inte två kopior av indexet. Inga ytterligare diskutrymme är så krävs eller konsumeras i dina behållare medan index omvandling inträffar.

När du ändrar indexprincip ändringarna tillämpas för att flytta från det gamla indexet till det nya indexet främst baserat på indexering läge konfigurationer. Indexering läge konfigurationer spelar en stor roll jämfört med andra egenskaper som inkluderad/exkluderad sökvägar, index-typer och precision.

Om både gamla och nya indexering principer **konsekvens** indexering, Azure Cosmos-databasen utför en omvandling med online-index. Du kan inte använda en annan indexering ändring som har konsekventa indexering läge medan transformeringen pågår. När du flyttar till ingen indexering läge på indexet togs bort omedelbart. Flytta till ingen är användbart när du vill avbryta en pågående omvandling och börja från början med en annan indexeringsprincip.

För index omvandling har slutförd, kontrollera att har behållaren tillräckligt med lagringsutrymme. Om behållaren når sin lagringskvot, har index-transformeringen pausats. Index omvandling återupptas automatiskt när lagringsutrymmet är tillgängligt, till exempel när du tar bort några objekt.

## <a name="modifying-the-indexing-policy---examples"></a>Ändra indexeringsprincip - exempel

Här följer några vanliga användningsområden där du kan uppdatera en indexeringsprincip:

* Om du vill ha enhetliga resultat under normal drift men återgå till den **ingen** indexering läge under bulk dataimporter.

* Om du vill börja använda indexering funktioner på dina aktuella Azure-Cosmos-behållare. Du kan till exempel använda geospatiala frågor, vilket kräver spatialindexet typ eller ORDER BY / sträng intervallfrågor som kräver sträng intervallet index typ.

* Om du vill manuellt väljer du egenskaperna som ska indexeras och ändra dem över tid för att justera dina arbetsbelastningar.

* Om du vill justera indexering precisionen att förbättra prestanda för frågor eller minska förbrukad lagring.

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

* [Indexeringsöversikt](index-overview.md)
* [Indextyper](index-types.md)
* [Indexsökvägar](index-paths.md)
* [Så här hanterar du indexeringsprincip](how-to-manage-indexing-policy.md)
