---
title: Översikt över Azure Stream Analytics
description: Lär dig mer om Stream Analytics, en hanterad tjänst som hjälper dig att analysera data i realtid som skickats från IoT (Internet of Things).
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 05/16/2019
ms.openlocfilehash: ded2011111262eb45818ea149949989eef885f24
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789589"
---
# <a name="what-is-azure-stream-analytics"></a>Vad är Azure Stream Analytics?

Azure Stream Analytics är en händelsebearbetningsmotor som har utformats för att undersöka stora mängder strömmande data. Mönster och relationer kan identifieras på information som hämtas från ett antal indatakällor, inklusive enheter, sensorer, webbplatser, sociala medier och program. Dessa mönster kan användas för att utlösa andra åtgärder nedströms, som att skapa aviseringar, skicka information till ett Rapporteringsverktyg eller lagra omvandlade data för senare användning.

Följande är exempel på när du kan använda Azure Stream Analytics:

* Sakernas Internet (IoT) sensor fusion och analys i realtid på enhetstelemetri
* Webbloggar/klickströmsanalys
* Geospatial analys för hantering av vagnpark och förarlösa fordon
* Fjärrövervakning och förutsägande underhåll av högt värderade tillgångar
* Realtidsanalys av kassadata för lagerkontroll och avvikelseidentifiering

## <a name="how-does-stream-analytics-work"></a>Hur fungerar Stream Analytics?

Azure Stream Analytics-jobb består av en inmatning, en transformationsfråga och produktion. När händelser från din programvara eller enheter är matas in i Azure Event Hubs, Azure IoT Hub eller Azure Blob Storage, kan du ange en eller flera av dessa tjänster som Indatakällan för jobbet. Transformationsfrågan, som är baserad på SQL-frågespråket, används för att enkelt filtrera, sortera, aggregera och ansluta strömmande data under en viss tidsperiod. Du kan justera Händelseordning alternativ och varaktigheten för tidsperioder när preforming aggregeringsåtgärder.

Varje jobb har utdata för transformerade data och du kan styra vad som händer i svar på den information som du har analyserat. Du kan till exempel:

* Skicka data till en övervakad kö för att utlösa aviseringar eller anpassade arbetsflöden nedströms.
* Skicka data till en Power BI-instrumentpanel för visualisering i realtid.
* Store data i andra Azure-lagringstjänster att träna en maskininlärningsmodell baserat på historiska data eller utföra batchanalyser.

Följande bild visar hur data skickas till Stream Analytics, analyseras och skickas för andra åtgärder som lagring eller presentation:

![Introduktionspipeline för Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Viktiga funktioner och fördelar

Azure Stream Analytics är avsett att vara enkelt att använda samt flexibelt, tillförlitligt och skalbart för alla jobbstorlekar. Det är tillgängligt i flera Azure-regioner. Följande bild illustrerar de viktigaste funktionerna i Azure Stream Analytics:

![Viktiga funktioner i Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Lätt att komma igång

Azure Stream Analytics är enkelt att börja. Det tar bara några klick för att ansluta till flera källor och mottagare, hur du skapar en pipeline för slutpunkt till slutpunkt. Stream Analytics kan ansluta till [Azure Event Hubs](/azure/event-hubs/) och [Azure IoT Hub](/azure/iot-hub/) för inmatning av strömmande data, samt [Azure Blob storage](/azure/storage/storage-introduction) att mata in historiska data. Jobbindata kan även innehålla statiska eller långsamt föränderliga referensdata från Azure Blob storage eller [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database-preview) att du kan ansluta till strömmande data för att utföra sökåtgärder.

Stream Analytics kan dirigera jobbutdata till många lagringssystem som [Azure Blob storage](/azure/storage/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/), och [Azure Cosmos DB](/azure/cosmos-db/introduction). Du kan köra batch-analyser på lagrade utdata med Azure HDInsight eller du kan skicka utdata till en annan tjänst, till exempel Händelsehubbar för förbrukning eller [Power BI](https://docs.microsoft.com/power-bi/) för visualisering i realtid.

Läs hela listan med Stream Analytics-utdata, [förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programmerares produktivitet

Azure Stream Analytics använder ett enkelt SQL-baserat frågespråk som har förstärkts med kraftfulla temporala begränsningar för att analysera data i rörelse. Om du vill definiera jobbtransformationer använder du ett enkelt, deklarativt [Stream Analytics-frågespråk](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) som gör att du kan skapa komplexa temporala frågor och analyser med hjälp av enkla SQL-konstruktioner. Eftersom Stream Analytics-frågespråket är konsekvent SQL-språket, är bekant med SQL tillräckliga för att börja skapa jobb. Du kan också skapa jobb med hjälp av utvecklarverktyg, till exempel Azure PowerShell, [Stream Analytics Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-install.md), [Stream Analytics Visual Studio Code-tillägg](quick-create-vs-code.md), eller Azure Resource Manager-mallar . Med hjälp av utvecklarverktyg kan du utveckla transformationsfrågor offline och använda [CI/CD-pipelinen](stream-analytics-tools-for-visual-studio-cicd.md) för att skicka jobb till Azure.

Stream Analytics-frågespråket erbjuder en mängd olika funktioner för analys och bearbetning av strömmande data. Det här frågespråket stöder enkel datamanipulering, aggregeringsfunktioner och komplexa geospatiala funktioner. Du kan redigera frågor i portalen och testa dem med exempeldata som extraheras från en direktsänd dataström.

Du kan utöka funktionerna i frågespråket genom att definiera och anropa ytterligare funktioner. Du kan definiera funktionsanrop i Azure Machine Learning-tjänsten för att dra nytta av Azure Machine Learning-lösningar och integrera JavaScript eller C# användardefinierade funktioner (UDF) eller användardefinierade aggregeringar för att utföra komplexa beräkningar som en del ett Stream Analytics-fråga.

## <a name="fully-managed"></a>Fullständigt hanterat

Azure Stream Analytics är ett fullständigt hanterat serverlöst erbjudande (PaaS) på Azure. Du behöver inte etablera någon maskinvara eller hantera kluster för att köra dina jobb. Azure Stream Analytics hanterar jobbet helt genom att skapa komplexa beräkningskluster i molnet och tar hand om prestandajustering som krävs för att köra jobbet. Integrering med Azure Event Hubs och Azure IoT Hub ger ditt jobb mata in miljontals händelser per sekund från flera källor, inkludera anslutna enheter, klickströmsdata och loggfiler. Med partitioneringsfunktionen för Händelsehubbar kan du partitionera beräkningar i logiska steg, vart och ett med möjlighet att ytterligare partitioneras för att öka skalbarheten.

## <a name="run-in-the-cloud-on-in-the-intelligent-edge"></a>Körs i molnet på i en intelligent gräns

Azure Stream Analytics kan köras i molnet, för storskaliga analys, eller köras på IoT Edge för extremt låg latens analys. Azure Stream Analytics använder samma frågespråk på både i molnet och gränsen, så att utvecklare kan skapa äkta hybridarkitekturer för bearbetning av dataströmmen.

## <a name="low-total-cost-of-ownership"></a>Låg total ägandekostnad

Som en molntjänst är Stream Analytics optimerad för kostnad. Det finns inga startavgifter ingår – du betalar bara för de [strömningsenheter du förbrukar](stream-analytics-streaming-unit-consumption.md), och hur mycket data som bearbetas. Det finns inget åtagande eller klusteretablering krävs och du kan skala upp eller ned baserat på dina affärsbehov.

## <a name="mission-critical-ready"></a>Redo för verksamhetskritiskt

Azure Stream Analytics är tillgängligt i flera regioner över hela världen och är utformat för att köra verksamhetskritiska arbetsbelastningar genom att stödja kraven för tillförlitlighet, säkerhet och efterlevnad.

### <a name="reliability"></a>Tillförlitlighet

Azure Stream Analytics garanterar händelsebearbetning exakt en gång och leverans av händelser minst en gång, så inga händelser går förlorade. Exakt-när bearbetningen är korrekt med valda enligt beskrivningen i [händelse Leveransgarantier](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics har inbyggda återställningsfunktioner om det inte går att leverera en händelse. Stream Analytics kan du också tillhandahåller inbyggd kontrollpunkter för att upprätthålla jobbets tillstånd och ger upprepningsbara resultat.

Som hanterad tjänst garanterar Stream Analytics händelsebearbetning med 99,9 % tillgänglighet på minutnivå. Mer information finns i den [SLA för Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) sidan. 

### <a name="security"></a>Säkerhet

När det gäller säkerhet krypterar Azure Stream Analytics all inkommande och utgående kommunikation och har stöd för TLS 1.2. Inbyggda kontrollpunkter krypteras också. Stream Analytics lagrar inte inkommande data eftersom all bearbetning sker i minnet.

### <a name="compliance"></a>Efterlevnad

Azure Stream Analytics följer flera efterlevnadscertifieringar enligt beskrivningen i [översikten över Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Prestanda

Stream Analytics kan bearbeta miljontals händelser per sekund och leverera resultat med kort svarstid. Det ger dig möjlighet att skala upp och skala ut för att hantera stora program för bearbetning av händelser i realtid och komplexa händelser. Stream Analytics stöder prestanda genom partitionering, vilket gör att komplexa frågor till parallelliseras och köras på flera strömmande noder. Azure Stream Analytics är byggt på [Trill](https://github.com/Microsoft/Trill), en högpresterande minnesintern analysmotor för direktuppspelning som har utvecklats i samarbete med Microsoft Research.

## <a name="next-steps"></a>Nästa steg

Nu har du en överblick av Azure Stream Analytics. Härnäst kan du gå på djupet och skapa ditt första Stream Analytics-jobb:

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio Code](quick-create-vs-code.md).
