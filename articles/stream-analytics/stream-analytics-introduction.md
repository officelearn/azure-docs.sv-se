---
title: Introduktion till Stream Analytics | Microsoft Docs
description: "Lär dig mer om Stream Analytics, en hanterad tjänst som hjälper dig att analysera data i realtid som skickats från IoT (Internet of Things)."
keywords: "analytics som en tjänst, hanterade tjänster, stream bearbetning, streaming analytics vad är stream analytics"
services: stream-analytics
documentationcenter: 
author: jenniehubbard
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/08/2017
ms.author: jhubbard
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 5081ecaf569aef9e2f99cc27e91c4b64a25b0deb
ms.contentlocale: sv-se
ms.lasthandoff: 08/11/2017

---

# <a name="what-is-stream-analytics"></a>Vad är Stream Analytics?

Azure Stream Analytics är en helt hanterad händelsebearbetningsmotor som låter dig ställa in analytiska beräkningar i realtid på streamingdata. Data kan komma från enheter, sensorer, webbplatser, sociala medier, program, infrastruktursystem och mer. 

## <a name="what-can-i-do-with-stream-analytics"></a>Vad kan jag göra med Stream Analytics?

Med Stream Analytics kan du undersöka stora mängder data som flödar från enheter eller processer, extrahera information från dataströmmar och leta efter mönster, trender och relationer. Baserat på vad som finns i datan kan du sedan utföra uppgifter för programmet. Du kan till exempel generera aviseringar, starta automatiseringsarbetsflöden, skicka information till ett verktyg, till exempel Power BI, eller lagra data för senare undersökning. 

Exempel:

* Anpassade börshandelanalys i realtid och aviseringar som erbjuds av företag för finansiella tjänster.
* Upptäck bedrägerier i realtid baserat på transaktionsdata. 
* Tjänster för data- och identitetsskydd.
* Analys av data som genereras av sensorer och motstånd som är inbäddade i fysiska objekt (Sakernas internet eller IoT).
* Webbklickanalys.
* Kundrealtionshanteringsprogram (CRM), till exempel varningar när kundupplevelsen inom ett tidsintervall har försämrats.

## <a name="how-does-stream-analytics-work"></a>Hur fungerar Stream Analytics?

Det här diagrammet illustrerar arbetsflödet i Stream Analytics, det vill säga hur data inhämtas, analyseras och sedan skickas för presentation eller åtgärd. 

![Arbetsflöde för Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

Stream Analytics börjar med en källa för dataöverföring. Data kan hämtas till Azure från en enhet med hjälp av en Azure-händelsehubb eller IoT-hubb. Data kan också hämtas från ett dataarkiv som Azure Blob Storage. 

Om du vill kontrollera dataströmmen skapar du ett Stream Analytics-*jobb* som anger var data kommer från. Jobbet kan också ange en *omvandling*&mdash;för hur du söker efter data, mönster eller relationer. För den här uppgifter stöder Stream Analytics ett SQL-liknande frågespråk som låter dig filtrera, sortera, sammanställa och koppla strömmande data över en tidsperiod.

Slutligen anger jobbet en utdatafil som omvandlade data ska skickas till. På så sätt kan du styra vad du gör som svar på den information som du har analyserat. Som svar på analysen kan du till exempel:

* Skicka ett kommando för att ändra inställningarna för en enhet. 
* Skicka data till en kö som övervakas av en process som vidtar åtgärder baserat på vad den hittar. 
* Skicka data till en Power BI-instrumentpanel för rapportering.
* Skicka data till lagring som Data Lake Store, SQL Server-databas eller Azure Blob eller Table storage.

Du kan övervaka det hela och justera hur många händelser som bearbetas per sekund. Du kan också ha jobb som producerar diagnostiska loggar för felsökning.

## <a name="key-capabilities-and-benefits"></a>Viktiga funktioner och fördelar

Stream Analytics är avsett att vara enkelt att använda, flexibelt, skalbart för alla jobbstorlekar och ekonomiskt.

### <a name="connectivity-to-many-inputs-and-outputs"></a>Anslutning till många indata- och utdatakällor

Stream Analytics ansluter direkt till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) och [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) för inhämtning av dataströmmar och till lagringstjänsten [Azure Blob](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) för att mata in historiska data. Om du hämtar data från händelsehubbar kan du kombinera Stream Analytics med andra datakällor och bearbetningsmotorer.

Jobbindata kan även inkludera referensdata (statiska eller långsamt föränderliga data). Du kan koppla strömmat data till dessa referensdata och utföra åtgärder för sökning på samma sätt som du skulle gjort med databasfrågor.

Dirigera Stream Analytics-jobbutdata i många riktningar. Du kan skriva till lagring, till exempel Azure Storage-blobbar eller tabeller, Azure SQL DB, Azure Data Lake Stores eller Azure Cosmos DB. Därifrån kan data gå vidare för batchanalyser via Azure HDInsight. Du kan skicka utdata till en annan tjänst för användning av en annan process, till exempel händelsehubbar, Azure Service Bus-ämnen eller köer. Du kan skicka utdata till Power BI för visualisering.

### <a name="ease-of-use"></a>Användbarhet

För att definiera omvandlingar, använder du det enkla deklarativa [Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx) som hjälper dig att skapa avancerade analyser utan programmering. Frågespråket tar strömmande data som indata. Därefter kan du filtrera och sortera data, aggregera värden, utföra beräkningar, ansluta till data (inom en ström eller till referensdata) och använda geospatiala funktioner. Du kan redigera frågor i portalen med IntelliSense och syntaxkontroll och du kan testa frågor med exempeldata som du kan extrahera från liveöverföringen.

### <a name="extensible-query-language"></a>Omfattande frågespråk

Du kan utöka funktionerna i frågespråket genom att definiera och anropa ytterligare funktioner. Du kan definiera funktionsanrop i Azure Machine Learning-tjänsten för att dra nytta av Azure Machine Learning-lösningar. Du kan också integrera användardefinierade JavaScript-funktioner (UDF) för att kunna utföra komplexa beräkningar som en del av en Stream Analytics-fråga.

### <a name="scalability"></a>Skalbarhet

Stream Analytics kan hantera upp till 1 GB inkommande data per sekund. Tack vare integreringen med [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) och [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) kan jobb mata in flera miljoner händelser per sekund från anslutna enheter, klickströmsdata och loggfiler för att bara nämna några få. Med funktionen partition av händelsehubbar kan du kan också partitionera beräkningar i logiska steg, vart och ett med möjlighet att ytterligare partitioneras för att öka skalbarheten.

### <a name="low-cost"></a>Låg kostnad

Som en molntjänst är Stream Analytics optimerad för att kan du ska komma igång till låg kostnad. Du betalar per användning baserat på en Streaming Unit och mängden data som bearbetas av systemet. Användningen beräknas baserat på mängden bearbetade händelser och hur mycket datorkraft som tilldelats i klustret för att hantera Stream Analytics-jobb.

### <a name="reliability-quick-recovery-and-repeatability"></a>Tillförlitlighet och snabb återställning och repeterbarhet

Som en hanterad tjänst i molnet, förhindrar Stream Analytics dataförlust och ger kontinuitet för företag. Om fel uppstår innehåller tjänsten inbyggda återställningsfunktioner. Med möjligheten att upprätthålla tillstånd internt tillhandahåller tjänsten repeterbara resultat som ser till att du kan arkivera händelser och återanvända bearbetningar i framtiden, och alltid få samma resultat. Detta ger du möjlighet att gå tillbaka i tiden och undersöka beräkningar i samband med rotorsaksanalyser, konsekvensanalyser osv.

## <a name="next-steps"></a>Nästa steg

* Kom igång genom att [experimentera med indata- och frågor från IoT-enheter](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Skapa en [Stream Analytics-lösning från slutpunkt-till-slutpunkt](stream-analytics-real-time-fraud-detection.md) som undersöker telefonmetadata för att leta efter bedrägliga samtal.
* Lär dig mer om det SQL-liknande frågespråket för Stream Analytics och unika begrepp som [fönsterfunktioner](stream-analytics-window-functions.md).
* Lär dig [skala Stream Analytics-jobb](stream-analytics-scale-jobs.md). 
* Lär dig [integrera Stream Analytics och Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).
* Få svar på dina Stream Analytics-frågor på [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


