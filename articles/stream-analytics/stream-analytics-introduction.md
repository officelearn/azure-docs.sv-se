---
title: Översikt över Azure Stream Analytics
description: Lär dig mer om Stream Analytics, en hanterad tjänst som hjälper dig att analysera data i realtid som skickats från IoT (Internet of Things).
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 10/9/2020
ms.openlocfilehash: 8e11611e679b4a900da0fd09d60ea33be4e15c98
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124874"
---
# <a name="what-is-azure-stream-analytics"></a>Vad är Azure Stream Analytics?

Azure Stream Analytics är en real tids analys och komplex händelse bearbetnings motor som är utformad för att analysera och bearbeta stora mängder snabba strömmande data från flera källor samtidigt. Mönster och relationer kan identifieras i information som extraherats från ett antal inmatnings källor, inklusive enheter, sensorer, klick Ströms data, sociala media-flöden och program. Dessa mönster kan användas för att utlösa åtgärder och initiera arbets flöden, till exempel för att skapa aviseringar, matnings information till ett rapporterings verktyg eller lagra transformerade data för senare användning. Stream Analytics är också tillgängligt på Azure IoT Edge Runtime och gör det möjligt att bearbeta data på IoT-enheter. 

Följande scenarier är exempel på när du kan använda Azure Stream Analytics:

* Analysera data strömmar i real tid från IoT-enheter
* Webbloggar/klickströmsanalys
* Geospatial analys för hantering av vagnpark och förarlösa fordon
* Fjärrövervakning och förutsägande underhåll av högt värde till gångar
* Realtidsanalys av kassadata för lagerkontroll och avvikelseidentifiering

## <a name="how-does-stream-analytics-work"></a>Hur fungerar Stream Analytics?

Ett Azure Stream Analytics jobb består av indata, frågor och utdata. Stream Analytics matar in data från Azure Event Hubs (inklusive Azure Event Hubs från Apache Kafka), Azure IoT Hub eller Azure Blob Storage. Frågan, som baseras på SQL-frågespråket, kan användas för att enkelt filtrera, sortera, sammanställa och koppla strömmande data under en viss tids period. Du kan också utöka detta SQL-språk med Java Script-och C#-användardefinierade funktioner (UDF: er). Du kan enkelt ändra händelse ordnings alternativen och varaktigheten för tiden Windows när du förformerar agg regerings åtgärder via enkla språk konstruktioner och/eller konfigurationer.

Varje jobb har en eller flera utdata för transformerade data, och du kan kontrol lera vad som händer i svaret på den information som du har analyserat. Du kan till exempel:

* Skicka data till tjänster som Azure Functions, Service Bus ämnen eller köer för att utlösa kommunikation eller anpassade arbets flöden.
* Skicka data till en Power BI instrument panel för instrument paneler i real tid.
* Lagra data i andra Azure Storage-tjänster (t. ex. Azure Data Lake, Azure Synapse Analytics osv.) för att träna en maskin inlärnings modell baserat på historiska data eller utföra batch-analys.

Följande bild visar hur data skickas till Stream Analytics, analyseras och skickas för andra åtgärder, t. ex. lagring eller presentation:

![Introduktionspipeline för Stream Analytics](./media/stream-analytics-introduction/stream-analytics-e2e-pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Viktiga funktioner och fördelar

Azure Stream Analytics är avsett att vara enkelt att använda samt flexibelt, tillförlitligt och skalbart för alla jobbstorlekar. Den är tillgänglig i flera Azure-regioner och körs på IoT Edge eller Azure Stack.

## <a name="ease-of-getting-started"></a>Lätt att komma igång

Azure Stream Analytics är enkelt att starta. Det tar bara några klick att ansluta till flera källor och mottagare, vilket skapar en pipeline från slut punkt till slut punkt. Stream Analytics kan ansluta till [azure Event Hubs](../event-hubs/index.yml) och [Azure-IoT Hub](../iot-hub/index.yml) för att strömma data inmatning, samt [Azure Blob Storage](../storage/common/storage-introduction.md) för att mata in historiska data. Jobb indata kan också innehålla statiska eller långsamma referens data från Azure Blob Storage eller [SQL Database](stream-analytics-use-reference-data.md#azure-sql-database) som du kan ansluta till strömmande data för att utföra söknings åtgärder.

Stream Analytics kan dirigera jobb utdata till många lagrings system, till exempel [Azure Blob Storage](../storage/common/storage-introduction.md), [Azure SQL Database](/azure/sql-database/), [Azure Data Lake Store](../data-lake-store/index.yml)och [Azure CosmosDB](../cosmos-db/introduction.md). Du kan också köra batch Analytics på Stream-outpust med Azure Synapse Analytics eller HDInsight, eller så kan du skicka utdata till en annan tjänst, t. ex. Event Hubs för förbrukning eller [Power BI](/power-bi/) för visualisering i real tid.

Hela listan med Stream Analytics utdata finns i [förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md).

## <a name="programmer-productivity"></a>Programmerare produktivitet

Azure Stream Analytics använder ett SQL-frågespråk som har förbättrats med kraftfulla temporala begränsningar för att analysera data i rörelse. Du kan också skapa jobb med hjälp av utvecklarverktyg som Azure PowerShell, Azure CLI, [Stream Analytics Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-install.md), [Stream Analytics Visual Studio Code Extension](quick-create-visual-studio-code.md)eller Azure Resource Manager mallar. Med utvecklarverktyg kan du utveckla omvandlings frågor offline och använda [CI/CD-pipeline](stream-analytics-tools-for-visual-studio-cicd.md) för att skicka jobb till Azure.

Med Stream Analytics frågespråk kan du utföra CEP (komplex Event procssing) genom att erbjuda en mängd olika funktioner för analys av strömmande data. Det här frågespråket stöder enkel data manipulation, agg regerings-och analys funktioner, [geospatiala funktioner](./stream-analytics-geospatial-functions.md), [mönster matchning](/stream-analytics-query/match-recognize-stream-analytics) och [avvikelse identifiering](./stream-analytics-machine-learning-anomaly-detection.md). Du kan redigera frågor i portalen eller med hjälp av våra utvecklingsverktyg och testa dem med hjälp av exempel data som extraheras från en Live-dataström.

Du kan utöka funktionerna i frågespråket genom att definiera och anropa ytterligare funktioner. Du kan definiera funktions anrop i Azure Machine Learning för att dra nytta av Azure Machine Learning lösningar och integrera JavaScript-eller C#-användardefinierade funktioner (UDF: er) eller användardefinierade agg regeringar för att utföra komplexa beräkningar som en del av en Stream Analytics fråga.

## <a name="fully-managed"></a>Fullständigt hanterad

Azure Stream Analytics är ett fullständigt hanterat (PaaS) erbjudande på Azure. Du behöver inte etablera någon maskin vara eller infrastruktur, uppdatera operativ system eller program vara. Azure Stream Analytics hanterar ditt jobb fullständigt, så att du kan fokusera på affärs logiken och inte på infrastrukturen.


## <a name="run-in-the-cloud-or-on-the-intelligent-edge"></a>Kör i molnet eller på den intelligenta kanten

Azure Stream Analytics kan köras i molnet, för storskalig analys eller köras på IoT Edge eller Azure Stack för extremt låg latens analys. Azure Stream Analytics använder samma verktyg och frågespråk både i molnet och på gränsen, så att utvecklare kan bygga verkligt hybrid arkitekturer för bearbetning av data strömmar. 

## <a name="low-total-cost-of-ownership"></a>Låg total ägande kostnad

Som en molntjänst är Stream Analytics optimerad för kostnad. Det finns inga inaktiva kostnader – du betalar bara för de [strömnings enheter du förbrukar](stream-analytics-streaming-unit-consumption.md). Det krävs ingen åtagande-eller kluster etablering, och du kan skala upp eller ned jobbet utifrån dina affärs behov.

## <a name="mission-critical-ready"></a>Redo för verksamhetskritiskt

Azure Stream Analytics är tillgängligt i flera regioner över hela världen och är utformat för att köra verksamhetskritiska arbetsbelastningar genom att stödja kraven för tillförlitlighet, säkerhet och efterlevnad.

### <a name="reliability"></a>Tillförlitlighet

Azure Stream Analytics garanterar händelsebearbetning exakt en gång och leverans av händelser minst en gång, så inga händelser går förlorade. Exakt en bearbetning garanteras med utvalda utdata enligt beskrivningen i [händelse leverans garantier](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).

Azure Stream Analytics har inbyggda återställningsfunktioner om det inte går att leverera en händelse. Stream Analytics innehåller också inbyggda kontroll punkter för att underhålla jobbets tillstånd och ger upprepnings bara resultat.

Som en hanterad tjänst garanterar Stream Analytics händelse bearbetningen med en tillgänglighet på 99,9% på en minuters kornig het. Mer information finns på sidan om [Stream Analytics service avtal](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) . 

### <a name="security"></a>Säkerhet

När det gäller säkerhet krypterar Azure Stream Analytics all inkommande och utgående kommunikation och har stöd för TLS 1.2. Inbyggda kontrollpunkter krypteras också. Stream Analytics lagrar inte inkommande data eftersom all bearbetning sker i minnet. Stream Analytics stöder också virtuella Azure-nätverk (VNET) när ett jobb körs i ett [Stream Analytics-kluster](./cluster-overview.md).

### <a name="compliance"></a>Efterlevnad

Azure Stream Analytics följer flera efterlevnadscertifieringar enligt beskrivningen i [översikten över Azure-efterlevnad](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="performance"></a>Prestanda

Stream Analytics kan bearbeta miljon tals händelser varje sekund och det kan leverera resultat med extremt låg fördröjning. Det ger dig möjlighet att skala upp och skala ut för att hantera stora program för bearbetning av händelser i realtid och komplexa händelser. Stream Analytics stöder högre prestanda genom partitionering, så att komplexa frågor kan vara parallella och köras på flera strömmande noder. Azure Stream Analytics är byggt på [Trill](https://github.com/Microsoft/Trill), en högpresterande minnesintern analysmotor för direktuppspelning som har utvecklats i samarbete med Microsoft Research.

## <a name="next-steps"></a>Nästa steg

Nu har du en överblick av Azure Stream Analytics. Härnäst kan du gå på djupet och skapa ditt första Stream Analytics-jobb:

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).
* [Skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Skapa ett Stream Analytics-jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md).
* [Skapa ett Stream Analytics jobb genom att använda Visual Studio Code](quick-create-visual-studio-code.md).