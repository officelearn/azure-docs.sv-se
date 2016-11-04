---
title: Introduktion till Stream Analytics | Microsoft Docs
description: Lär dig mer om Stream Analytics, en hanterad tjänst som hjälper dig att analysera data i realtid som skickats från IoT (Internet of Things).
keywords: analytics som en tjänst, hanterade tjänster, stream bearbetning, streaming analytics vad är stream analytics
services: stream-analytics
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok

---
# Vad är Stream Analytics?
Azure Stream Analytics är en helt hanterad, kostnadseffektiv motor för händelsebearbetning i realtid som kan ge nya, djupa insikter från dina data. Stream Analytics gör det enkelt att konfigurera analysberäkningar i realtid för data som strömmas från enheter, sensorer, webbplatser, sociala medier, program, infrastruktursystem och mer.

Du kan skapa ett Stream Analytics-jobb med några få klick på Azure-portalen genom att ange indatakällan för strömmande data, utdatamottagaren av resultatet från jobbet och en dataomvandling uttryckt i ett SQL-liknande språk. Du kan övervaka och justera jobbets skala/hastighet på Azure-portalen så att det skalas från några få kilobyte till över en gigabyte händelser som bearbetas per sekund.

Stream Analytics drar nytta av Microsoft Researchs mångåriga arbete med att utveckla mycket finjusterade strömmande motorer för tidskänslig bearbetning, liksom språkintegreringar för intuitiva specifikationer av dessa.

## Vad kan jag använda Stream Analytics för?
Stora mängder data flödar med hög hastighet via kabel i dag. Organisationer som kan bearbeta och agera på dessa strömmande data i realtid kan kraftigt öka effektiviteten och sticka ut på marknaden. Scenarier för realtidsanalys av dataströmmar återfinns inom alla branscher: anpassad aktiehandelsanalys i realtid och aviseringar som erbjuds av företag för finansiella tjänster, bedrägeriidentifiering i realtid, tjänster för data- och identitetsskydd, tillförlitlig inhämtning och analys av data som genereras av sensorer och manövreringsenheter som är inbäddade i fysiska objekt (Internet of Things, eller IoT), klickströmsanalyser på webben, CRM-program (Customer Relationship Management) som skickar varningar när kundupplevelsen inom en bestämd tidsperiod har försämrats och så vidare. Dagens företag behöver ett flexibelt, tillförlitligt och kostnadseffektivt sätt att genomföra den här typen av realtidsanalyser av strömmande händelsedata för att lyckas i företagsvärldens tuffa konkurrens.

## Viktiga funktioner och fördelar
* **Användbarhet:** Stream Analytics stöder en enkel, deklarativ frågemodell för att beskriva transformationer. För att ytterligare underlätta för användaren använder Stream Analytics en T-SQL-variant som gör att kunderna slipper hantera den tekniska komplexiteten som är vanlig i system för dataströmsbearbetning. Genom att använda [Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx) i den webbläsarbaserade frågeredigeraren får du tillgång till automatisk komplettering med IntelliSense så att du snabbt och enkelt kan implementera tidsseriefrågor, inklusive tidsbaserade kopplingar, fönsteraggregeringar, temporala filter och andra vanliga åtgärder som till exempel kopplingar, aggregeringar, projektioner och filter. Webbläsarbaserad frågetestning mot en exempeldatafil bidrar dessutom till en snabb, iterativ utveckling.  
* **Skalbarhet:** Stream Analytics kan hantera ett högt händelsegenomflöde på upp till 1 GB per sekund. Tack vare integreringen med [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) och [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) kan lösningen mata in flera miljoner händelser per sekund från anslutna enheter, klickströmsdata och loggfiler för att bara nämna några få. För att uppnå detta utnyttjar Stream Analytics partitioneringsmöjligheterna i Event Hubs, som klarar upp till 1 MB/s per partition. Användare kan partitionera beräkningen i ett antal logiska steg inom frågedefinitionen, var och en med möjlighet att partitioneras ytterligare för att öka skalbarheten.  
* **Tillförlitlighet, repeterbarhet och snabb återställning:** Som en hanterad tjänst i molnet kan Stream Analytics förhindra dataförlust och upprätthålla affärskontinuiteten vid eventuella fel med hjälp av inbyggda återställningsfunktioner. Med möjligheten att upprätthålla tillstånd internt tillhandahåller tjänsten repeterbara resultat som ser till att du kan arkivera händelser och återanvända bearbetningar i framtiden, och alltid få samma resultat. Detta ger kunderna möjlighet att gå tillbaka i tiden och undersöka beräkningar i samband med rotorsaksanalyser, konsekvensanalyser osv.  
* **Låg kostnad:** Som molntjänst är Stream Analytics optimerad att hjälpa användarna att till en mycket låg kostnad komma igång med och underhålla lösningar för realtidsanalys. Tjänsten är utformad så att du betalar per användning baserat på en Streaming Unit och mängden data som bearbetas av systemet. Användningen beräknas baserat på mängden bearbetade händelser och hur mycket datorkraft som tilldelats i klustret för att hantera motsvarande Stream Analytics-jobb.  
* **Referensdata:** Stream Analytics ger användarna möjlighet att ange och använda referensdata. Detta kan utgöra historiska data eller data som inte strömmas och som förändras mindre ofta över tid. Systemet förenklar användningen av referensdata så att de behandlas precis som alla andra inkommande händelseströmmar som ska kopplas till andra händelseströmmar som inhämtas i realtid för att genomföra transformationer.  
* **Användardefinierade funktioner:** Stream Analytics integreras med Azure Machine Learning för att definiera funktionsanrop i Machine Learning-tjänsten som en del av en Stream Analytics-fråga. Detta utökar funktionerna i Stream Analytics så att man att utnyttjar befintliga Azure Machine Learning-lösningar. Mer information om detta finns i [Självstudie om Machine Learning-integration](stream-analytics-machine-learning-integration-tutorial.md).
* **Anslutningsbarhet:** Stream Analytics ansluter direkt till Azure Event Hubs och Azure IoT Hubs för inhämtning av dataströmmar och till tjänsten Azure Blob för att mata in historiska data. Resultatet kan skrivas från Stream Analytics till Azure Storage-blobbar eller Azure Storage-tabeller, Azure SQL DB, Azure Data Lake Stores, DocumentDB, Event Hubs, Azure Service Bus-ämnen eller Azure Service Bus-köer och Power BI, där det kan bearbetas ytterligare av arbetsflöden, användas i batchanalyser via [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) eller bearbetas igen som en serie händelser. Om du använder Event Hubs kan du skapa flera Stream Analytics-jobb tillsammans med andra datakällor och bearbetningsmotorer utan att beräkningarnas strömmande egenskaper går förlorade.  

## Få hjälp
Om du behöver mer hjälp kan du besöka vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## Nästa steg
Du har fått en introduktion till Stream Analytics, en hanteringstjänst för analys av dataströmmar från Internet of Things. Mer information om den här tjänsten finns här:

* [Komma igång med Azure Stream Analytics](stream-analytics-get-started.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST-API:et](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=Sep16_HO4-->


