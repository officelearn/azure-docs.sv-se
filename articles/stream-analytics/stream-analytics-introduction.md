---
title: Översikt över Azure Stream Analytics
description: Lär dig mer om Stream Analytics, en hanterad tjänst som hjälper dig att analysera data i realtid som skickats från IoT (Internet of Things).
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: f15a4605d28beaf97e877f337051a2ec13148a41
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80235777"
---
# <a name="what-is-azure-stream-analytics"></a>Vad är Azure Stream Analytics?

Azure Stream Analytics är en analys- och komplex händelsebearbetningsmotor i realtid som är utformad för att analysera och bearbeta stora volymer snabbt strömmande data från flera källor samtidigt. Mönster och relationer kan identifieras i information som extraheras från ett antal indatakällor, inklusive enheter, sensorer, klickströmmar, sociala medieflöden och applikationer. Dessa mönster kan användas för att utlösa åtgärder och initiera arbetsflöden som skapar aviseringar, matar information till ett rapporteringsverktyg eller lagrar transformerade data för senare användning. Stream Analytics är också tillgängligt på Azure IoT Edge-körningen och stöder exakt samma språk eller syntax som molnet. 

Följande scenarier är exempel på när du kan använda Azure Stream Analytics:

* Analysera telemetriströmmar i realtid från IoT-enheter
* Webbloggar/klickströmsanalys
* Geospatial analys för hantering av vagnpark och förarlösa fordon
* Fjärrövervakning och prediktivt underhåll av tillgångar med högt värde
* Realtidsanalys av kassadata för lagerkontroll och avvikelseidentifiering

## <a name="how-does-stream-analytics-work"></a>Hur fungerar Stream Analytics?

Ett Azure Stream Analytics-jobb består av en indata, fråga och en utdata. Stream Analytics ertar data från Azure Event Hubs, Azure IoT Hub eller Azure Blob Storage. Frågan, som baseras på SQL-frågespråk, kan användas för att enkelt filtrera, sortera, aggregera och koppla direktuppspelningsdata över en tidsperiod. Du kan också utöka det här SQL-språket med JavaScript- och C#-användardefinierade funktioner (UDFs). Du kan enkelt justera alternativ för händelsebeställning och tidsfönsters varaktighet när du förformar aggregeringsåtgärder genom enkla språkkonstruktioner och/eller konfigurationer.

Varje jobb har en utdata för transformerade data och du kan styra vad som händer som svar på den information du har analyserat. Du kan till exempel:

* Skicka data till tjänster som Azure-funktioner, servicebussavsnitt eller köer för att utlösa kommunikation eller anpassade arbetsflöden nedströms.
* Skicka data till en Power BI-instrumentpanel för instrumentpanel i realtid.
* Lagra data i andra Azure-lagringstjänster för att träna en maskininlärningsmodell baserat på historiska data eller utföra batchanalys.

Följande bild visar hur data skickas till Stream Analytics, analyseras och skickas för andra åtgärder som lagring eller presentation:

![Introduktionspipeline för Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Viktiga funktioner och fördelar

Azure Stream Analytics är avsett att vara enkelt att använda samt flexibelt, tillförlitligt och skalbart för alla jobbstorlekar. Det är tillgängligt i flera Azure-regioner. Följande avbildning illustrerar de viktigaste funktionerna i Azure Stream Analytics:

![Viktiga funktioner i Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Lätt att komma igång

Azure Stream Analytics är enkelt att starta. Det tar bara några klick för att ansluta till flera källor och sänkor, skapa en end-to-end pipeline. Stream Analytics kan ansluta till [Azure Event Hubs](/azure/event-hubs/) och [Azure IoT Hub](/azure/iot-hub/) för direktuppspelning av datainmatning, samt Azure [Blob-lagring](/azure/storage/storage-introduction) till inmatning av historiska data. Jobbindata kan också innehålla statiska eller långsamt föränderliga referensdata från Azure Blob-lagring eller [SQL-databas](stream-analytics-use-reference-data.md#azure-sql-database) som du kan ansluta till strömmande data för att utföra uppslagsåtgärder.

Stream Analytics kan dirigera jobbutdata till många lagringssystem, till exempel [Azure Blob storage,](/azure/storage/storage-introduction) [Azure SQL Database,](/azure/sql-database/) [Azure Data Lake Store](/azure/data-lake-store/)och [Azure CosmosDB](/azure/cosmos-db/introduction). Du kan köra batchanalys på lagrad utdata med Azure HDInsight eller skicka utdata till en annan tjänst, till exempel Event Hubs för förbrukning eller [Power BI](https://docs.microsoft.com/power-bi/) för visualisering i realtid.

Hela listan över Stream Analytics-utdata finns i [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programmerare produktivitet

Azure Stream Analytics använder ett enkelt SQL-baserat frågespråk som har utökats med kraftfulla tidsmässiga begränsningar för att analysera data i rörelse. Om du vill definiera jobbtransformationer använder du ett enkelt, deklarativt [Stream Analytics-frågespråk](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) som gör att du kan skapa komplexa temporala frågor och analyser med hjälp av enkla SQL-konstruktioner. Eftersom Stream Analytics frågespråk är konsekvent till SQL-språket, är förtrogenhet med SQL tillräckligt för att börja skapa jobb. Du kan också skapa jobb med hjälp av utvecklarverktyg som Azure PowerShell, [Stream Analytics Visual Studio-verktyg,](stream-analytics-tools-for-visual-studio-install.md) [tillägget Stream Analytics Visual Studio Code](quick-create-vs-code.md)eller Azure Resource Manager-mallar. Med hjälp av utvecklarverktyg kan du utveckla transformationsfrågor offline och använda [CI/CD-pipelinen](stream-analytics-tools-for-visual-studio-cicd.md) för att skicka jobb till Azure.

Frågespråket Stream Analytics erbjuder ett brett utbud av funktioner för att analysera och bearbeta strömmande data. Det här frågespråket stöder enkel datamanipulering, aggregeringsfunktioner och komplexa geospatiala funktioner. Du kan redigera frågor i portalen och testa dem med hjälp av exempeldata som extraheras från en livestream.

Du kan utöka funktionerna i frågespråket genom att definiera och anropa ytterligare funktioner. Du kan definiera funktionsanrop i Azure Machine Learning för att dra nytta av Azure Machine Learning-lösningar och integrera JavaScript- eller C#-användardefinierade funktioner (UDF) eller användardefinierade aggregat för att utföra komplexa beräkningar som en del av en Stream Analytics-fråga.

## <a name="fully-managed"></a>Fullständigt hanterat

Azure Stream Analytics är ett fullständigt hanterat serverlöst erbjudande (PaaS) på Azure. Du behöver inte etablera någon maskinvara eller hantera kluster för att köra dina jobb. Azure Stream Analytics hanterar ditt jobb helt genom att konfigurera komplexa beräkningskluster i molnet och ta hand om den prestandajustering som krävs för att köra jobbet. Integrering med Azure Event Hubs och Azure IoT Hub gör att ditt jobb kan inta miljontals händelser per sekund som kommer från ett antal källor, för att inkludera anslutna enheter, klickströmmar och loggfiler. Med hjälp av partitioneringsfunktionen i Event Hubs kan du partitionera beräkningar i logiska steg, var och en med möjlighet att partitioneras ytterligare för att öka skalbarheten.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Kör i molnet eller på den intelligenta kanten

Azure Stream Analytics kan köras i molnet, för storskaliga analyser eller köras på IoT Edge för analys av extremt låg latens. Azure Stream Analytics använder samma frågespråk på både molnet och kanten, vilket gör det möjligt för utvecklare att skapa verkligt hybridarkitekturer för databearbetning. 

## <a name="low-total-cost-of-ownership"></a>Låg total ägandekostnad

Som en molntjänst är Stream Analytics optimerad för kostnad. Det finns inga initiala kostnader inblandade - du betalar bara för de [strömningsenheter du förbrukar](stream-analytics-streaming-unit-consumption.md)och mängden data som bearbetas. Det krävs inget åtagande eller klusteretablering och du kan skala jobbet uppåt eller nedåt baserat på dina affärsbehov.

## <a name="mission-critical-ready"></a>Redo för verksamhetskritiskt

Azure Stream Analytics är tillgängligt i flera regioner över hela världen och är utformat för att köra verksamhetskritiska arbetsbelastningar genom att stödja kraven för tillförlitlighet, säkerhet och efterlevnad.

### <a name="reliability"></a>Tillförlitlighet

Azure Stream Analytics garanterar händelsebearbetning exakt en gång och leverans av händelser minst en gång, så inga händelser går förlorade. Exakt en gång bearbetning garanteras med valda utdata som beskrivs i [Händelseleveransgarantier](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics har inbyggda återställningsfunktioner om det inte går att leverera en händelse. Stream Analytics tillhandahåller också inbyggda kontrollpunkter för att upprätthålla jobbets tillstånd och ger repeterbara resultat.

Som en hanterad tjänst garanterar Stream Analytics händelsebearbetning med en 99,9% tillgänglighet på en minuts detaljnivå. Mer information finns på sidan [Stream Analytics SLA.](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) 

### <a name="security"></a>Säkerhet

När det gäller säkerhet krypterar Azure Stream Analytics all inkommande och utgående kommunikation och har stöd för TLS 1.2. Inbyggda kontrollpunkter krypteras också. Stream Analytics lagrar inte inkommande data eftersom all bearbetning sker i minnet.

### <a name="compliance"></a>Efterlevnad

Azure Stream Analytics följer flera efterlevnadscertifieringar enligt beskrivningen i [översikten över Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Prestanda

Stream Analytics kan bearbeta miljontals händelser varje sekund och det kan leverera resultat med extremt låga fördröjningar. Det ger dig möjlighet att skala upp och skala ut för att hantera stora program för bearbetning av händelser i realtid och komplexa händelser. Stream Analytics stöder högre prestanda genom partitionering, vilket gör att komplexa frågor kan parallelliseras och köras på flera strömmande noder. Azure Stream Analytics är byggt på [Trill](https://github.com/Microsoft/Trill), en högpresterande minnesintern analysmotor för direktuppspelning som har utvecklats i samarbete med Microsoft Research.

## <a name="next-steps"></a>Nästa steg

Nu har du en överblick av Azure Stream Analytics. Härnäst kan du gå på djupet och skapa ditt första Stream Analytics-jobb:

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).
* [Skapa ett Stream Analytics-jobb med Visual Studio-kod](quick-create-vs-code.md).
