---
title: Översikt över Azure Stream Analytics
description: Lär dig mer om Stream Analytics, en hanterad tjänst som hjälper dig att analysera data i realtid som skickats från IoT (Internet of Things).
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 0982cc90d26c9f04e8d547c7d634e09280d7fca2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467909"
---
# <a name="what-is-azure-stream-analytics"></a>Vad är Azure Stream Analytics?

Azure Stream Analytics är en real tids analys och komplex händelse bearbetnings motor som är utformad för att analysera och bearbeta stora mängder snabba strömmande data från flera källor samtidigt. Mönster och relationer kan identifieras i information som extraherats från ett antal inmatnings källor, inklusive enheter, sensorer, klick Ströms data, sociala media-flöden och program. Dessa mönster kan användas för att utlösa åtgärder och initiera arbets flöden som skapar aviseringar, matnings information till ett rapporterings verktyg eller lagrar transformerade data för senare användning. Stream Analytics är också tillgängligt på Azure IoT Edge Runtime och stöder samma exakta språk eller syntax som molnet. 

Följande scenarier är exempel på när du kan använda Azure Stream Analytics:

* Analysera data strömmar i real tid från IoT-enheter
* Webbloggar/klickströmsanalys
* Geospatial analys för hantering av vagnpark och förarlösa fordon
* Fjärrövervakning och förutsägande underhåll av högt värde till gångar
* Realtidsanalys av kassadata för lagerkontroll och avvikelseidentifiering

## <a name="how-does-stream-analytics-work"></a>Hur fungerar Stream Analytics?

Ett Azure Stream Analytics jobb består av indata, frågor och utdata. Stream Analytics matar in data från Azure Event Hubs, Azure IoT Hub eller Azure Blob Storage. Frågan, som baseras på SQL-frågespråket, kan användas för att enkelt filtrera, sortera, sammanställa och koppla strömmande data under en viss tids period. Du kan också utöka detta SQL-språk med Java C# script och användardefinierade funktioner (UDF: er). Du kan enkelt ändra händelse ordnings alternativen och varaktigheten för tiden Windows när du förformerar agg regerings åtgärder via enkla språk konstruktioner och/eller konfigurationer.

Varje jobb har utdata för transformerade data och du kan styra vad som händer som svar på den information som du har analyserat. Du kan till exempel:

* Skicka data till tjänster som Azure Functions, Service Bus ämnen eller köer för att utlösa kommunikation eller anpassade arbets flöden.
* Skicka data till en Power BI instrument panel för instrument paneler i real tid.
* Lagra data i andra Azure Storage-tjänster för att träna en maskin inlärnings modell baserat på historiska data eller utföra batch-analys.

Följande bild visar hur data skickas till Stream Analytics, analyseras och skickas för andra åtgärder, t. ex. lagring eller presentation:

![Introduktionspipeline för Stream Analytics](./media/stream-analytics-introduction/stream-analytics-intro-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Viktiga funktioner och fördelar

Azure Stream Analytics är avsett att vara enkelt att använda samt flexibelt, tillförlitligt och skalbart för alla jobbstorlekar. Det är tillgängligt i flera Azure-regioner. Följande bild illustrerar viktiga funktioner i Azure Stream Analytics:

![Viktiga funktioner i Stream Analytics](./media/stream-analytics-introduction/stream-analytics-key-capabilities.png)

## <a name="ease-of-getting-started"></a>Lätt att komma igång

Azure Stream Analytics är enkelt att starta. Det tar bara några klick att ansluta till flera källor och mottagare, vilket skapar en pipeline från slut punkt till slut punkt. Stream Analytics kan ansluta till [azure Event Hubs](/azure/event-hubs/) och [Azure-IoT Hub](/azure/iot-hub/) för att strömma data inmatning, samt [Azure Blob Storage](/azure/storage/storage-introduction) för att mata in historiska data. Jobb indata kan också innehålla statiska eller långsamma referens data från Azure Blob Storage eller [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database) som du kan ansluta till strömmande data för att utföra söknings åtgärder.

Stream Analytics kan dirigera jobb utdata till många lagrings system, till exempel [Azure Blob Storage](/azure/storage/storage-introduction), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Store](/azure/data-lake-store/)och [Azure CosmosDB](/azure/cosmos-db/introduction). Du kan köra batch Analytics på lagrade utdata med Azure HDInsight, eller så kan du skicka utdata till en annan tjänst, t. ex. Event Hubs för förbrukning eller [Power BI](https://docs.microsoft.com/power-bi/) för visualisering i real tid.

Hela listan med Stream Analytics utdata finns i [förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programmerare produktivitet

Azure Stream Analytics använder ett enkelt SQL-baserat frågespråk som har förbättrats med kraftfulla temporala begränsningar för att analysera data i rörelse. Om du vill definiera jobbtransformationer använder du ett enkelt, deklarativt [Stream Analytics-frågespråk](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) som gör att du kan skapa komplexa temporala frågor och analyser med hjälp av enkla SQL-konstruktioner. Eftersom Stream Analytics frågespråket stämmer överens med SQL-språket räcker det med SQL för att börja skapa jobb. Du kan också skapa jobb med hjälp av utvecklarverktyg som Azure PowerShell, [Stream Analytics Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-install.md), [Stream Analytics Visual Studio Code-tillägg](quick-create-vs-code.md)eller Azure Resource Manager mallar. Med hjälp av utvecklarverktyg kan du utveckla transformationsfrågor offline och använda [CI/CD-pipelinen](stream-analytics-tools-for-visual-studio-cicd.md) för att skicka jobb till Azure.

Stream Analytics frågespråk erbjuder en mängd olika funktioner för analys och bearbetning av strömmande data. Det här frågespråket stöder enkel data manipulation, agg regerings funktioner och komplexa geospatiala funktioner. Du kan redigera frågor i portalen och testa dem med hjälp av exempel data som extraheras från en Live-dataström.

Du kan utöka funktionerna i frågespråket genom att definiera och anropa ytterligare funktioner. Du kan definiera funktions anrop i Azure Machine Learning för att dra nytta av Azure Machine Learning lösningar och integrera java script eller C# användardefinierade funktioner (UDF: er) eller användardefinierade mängder för att utföra komplexa beräkningar som en del av en ström Analytics-fråga.

## <a name="fully-managed"></a>Fullständigt hanterat

Azure Stream Analytics är ett fullständigt hanterat serverlöst erbjudande (PaaS) på Azure. Du behöver inte etablera någon maskin vara eller hantera kluster för att köra dina jobb. Azure Stream Analytics hanterar ditt jobb fullständigt genom att konfigurera komplexa beräknings kluster i molnet och ta hand om den prestanda justering som krävs för att köra jobbet. Genom integrering med Azure Event Hubs och Azure IoT Hub kan ditt jobb Hämta miljon tals händelser per sekund som kommer från ett antal källor, för att inkludera anslutna enheter, klick Ströms data och loggfiler. Med hjälp av partitionerings funktionen i Event Hubs kan du partitionera beräkningar i logiska steg, var och en med möjlighet att ytterligare partitioneras för att öka skalbarheten.

## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Kör i molnet eller på den intelligenta kanten

Azure Stream Analytics kan köras i molnet, för storskalig analys eller köras på IoT Edge för extremt låg latens analys. Azure Stream Analytics använder samma frågespråk både i molnet och på gränsen, så att utvecklare kan bygga verkligt hybrid arkitekturer för bearbetning av data strömmar. 

## <a name="low-total-cost-of-ownership"></a>Låg total ägande kostnad

Som en molntjänst är Stream Analytics optimerad för kostnad. Det finns inga inaktiva kostnader – du betalar bara för de [strömnings enheter du förbrukar](stream-analytics-streaming-unit-consumption.md)och mängden data som bearbetas. Det krävs ingen åtagande-eller kluster etablering, och du kan skala upp eller ned jobbet utifrån dina affärs behov.

## <a name="mission-critical-ready"></a>Redo för verksamhetskritiskt

Azure Stream Analytics är tillgängligt i flera regioner över hela världen och är utformat för att köra verksamhetskritiska arbetsbelastningar genom att stödja kraven för tillförlitlighet, säkerhet och efterlevnad.

### <a name="reliability"></a>Tillförlitlighet

Azure Stream Analytics garanterar händelsebearbetning exakt en gång och leverans av händelser minst en gång, så inga händelser går förlorade. Exakt en bearbetning garanteras med utvalda utdata enligt beskrivningen i [händelse leverans garantier](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics har inbyggda återställningsfunktioner om det inte går att leverera en händelse. Stream Analytics innehåller också inbyggda kontroll punkter för att underhålla jobbets tillstånd och ger upprepnings bara resultat.

Som en hanterad tjänst garanterar Stream Analytics händelse bearbetningen med en tillgänglighet på 99,9% på en minuters kornig het. Mer information finns på sidan om [Stream Analytics service avtal](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) . 

### <a name="security"></a>Säkerhet

När det gäller säkerhet krypterar Azure Stream Analytics all inkommande och utgående kommunikation och har stöd för TLS 1.2. Inbyggda kontrollpunkter krypteras också. Stream Analytics lagrar inte inkommande data eftersom all bearbetning sker i minnet.

### <a name="compliance"></a>Efterlevnad

Azure Stream Analytics följer flera efterlevnadscertifieringar enligt beskrivningen i [översikten över Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Prestanda

Stream Analytics kan bearbeta miljon tals händelser varje sekund och det kan leverera resultat med extremt låg fördröjning. Det ger dig möjlighet att skala upp och skala ut för att hantera stora program för bearbetning av händelser i realtid och komplexa händelser. Stream Analytics stöder högre prestanda genom partitionering, så att komplexa frågor kan vara parallella och köras på flera strömmande noder. Azure Stream Analytics är byggt på [Trill](https://github.com/Microsoft/Trill), en högpresterande minnesintern analysmotor för direktuppspelning som har utvecklats i samarbete med Microsoft Research.

## <a name="next-steps"></a>Nästa steg

Nu har du en överblick av Azure Stream Analytics. Härnäst kan du gå på djupet och skapa ditt första Stream Analytics-jobb:

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).
* [Skapa ett Stream Analytics jobb genom att använda Visual Studio Code](quick-create-vs-code.md).
