---
title: Hanterar händelsen ordning och lateness i Azure Stream Analytics
description: Den här artikeln beskriver hur Stream Analytics hanterar out ordning eller försenade händelser i dataströmmar.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: f0ee486d9ff4c05269da23866edad281aa627889
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113902"
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Azure Stream Analytics händelse ordning överväganden

## <a name="arrival-time-and-application-time"></a>Ankomsttid och programmet tid

I en temporal dataström av händelser tilldelas varje händelse en tidsstämpel. Azure Stream Analytics tilldelar en tidsstämpel till varje händelse med hjälp av antingen ankomst tid eller programmet tid. Den **System.Timestamp** kolumn har tilldelats tidsstämpeln. 

Ankomsttid tilldelas vid Indatakällan när händelsen når källan. Du kan komma åt ankomsttid med hjälp av den **EventEnqueuedUtcTime** -egenskapen för Händelsehubbar indata **IoTHub.EnqueuedTime** -egenskapen för IoT-hubb och använder den [BlobProperties.LastModified ](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) -egenskapen för blob-indata. 

Tid för programmet tilldelas när händelsen genereras och den ingår i nyttolasten. För att bearbeta händelser efter programmet tid, Använd den **tidsstämpel av** -satsen i select-frågan. Om den **tidsstämpel av** satsen saknas, händelser som bearbetas av ankomsttid. 

Azure Stream Analytics ger utdata i ordningen som tidsstämpeln och innehåller inställningar för att hantera out ordning data.


## <a name="handling-of-multiple-streams"></a>Hantering av flera strömmar

Azure Stream Analytics-jobbet kombinerar händelser från flera tidslinjer i fall som liknar följande:

* Utdata från flera partitioner. Frågor som inte har en explicit **Partition av PartitionId** sats måste kombinera händelser från alla partitioner.
* Unionen av två eller flera olika indatakällor.
* Koppla indatakällor.

I fall där flera tidslinjer kombineras Azure Stream Analytics ger utdata för tidsstämpel *t1* förrän alla källor som kombineras är minst *t1*. Anta exempelvis att frågan som läser från en event hub partition som har två partitioner. En av partitionerna, *P1*, har händelser tills *t1*. Den andra partitionen *P2*, har händelser tills *t1 + x*. Utdata sedan tills *t1*. Men om det finns en explicit **Partition av PartitionId** -sats båda partitionerna vidare oberoende av varandra.

Inställningen för sen ankomst tolerans används för att hantera avsaknad av data i en del partitioner.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Konfigurera sen ankomst feltolerans och tolerans för out-ordning
Antingen är inkommande dataströmmar som inte är i ordning:
* Sorterad (och därför fördröjd)
* Justeras av systemet, enligt användardefinierade-principer

Stream Analytics kan tolerera sen och out-ordning händelser när du bearbetning av programmet tid. Följande inställningar är tillgängliga i den **händelse ordning** alternativet i Azure-portalen: 

![Stream Analytics händelsehantering](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Tolerans för sen ankomst
Tolerans för sen ankomst gäller bara när du bearbetning av programmet tid. I annat fall ignoreras inställningen.

Tolerans för sen ankomst är största skillnaden mellan ankomsttid och tid för programmet. Om en händelse anländer senare än toleransen för sen ankomst (till exempel program tid *app_t* < ankomsttid *arr_t* -sen ankomst princip tolerans *late_t*), händelsen justeras maximalt för sen ankomst toleransen (*arr_t* - *late_t*). Fönstret sen ankomst är den längsta tillåtna fördröjningen mellan händelse genereras och mottagande av händelse vid Indatakällan. 

När flera partitioner från samma inkommande dataström eller flera inkommande strömmar kombineras, är tolerans för sen ankomst den maximala tid som alla partitioner som väntar på att nya data. 

Justering baserat på toleransen för sen ankomst sker först. Justering baserat på toleransen för out-ordning händer nu. Den **System.Timestamp** kolumnen har slutliga tidsstämpeln som tilldelats händelsen.

### <a name="out-of-order-tolerance"></a>Tolerans för out-ordning
Händelser som anländer utanför ordning, men i fönstret set out ordning tolerans sorteras av tidsstämpel. Händelser som kommer senare än fönstret tolerans är antingen:
* **Justeras**: efter verkar har kommit till den senaste acceptabla tid. 
* **Bort**: ignoreras.

När Stream Analytics sorterar om händelser som tas emot i fönstret out ordning tolerans, fördröjd utdata från frågan av fönstret tolerans för out-ordning.

### <a name="early-events"></a>Tidig händelser
Vid bearbetning av programmet tid händelser vars program är mer än 5 minuter före ankomsttid antingen bort eller justeras enligt konfigurationsalternativet som valts.

### <a name="example"></a>Exempel

* Tolerans för sen ankomst = 10 minuter<br/>
* Out-ordning tolerans = 3 minuter<br/>
* Bearbetning av programmet tid<br/>
* Händelser:
   1. **Programmet tid** = 00:00:00 **ankomsttid** = 00:10:01 **System.Timestamp** = 00:00:01, justera eftersom (**ankomst-tid - programmet**) är Mer än sen ankomst toleransen.
   2. **Programmet tid** = 00:00:01 **ankomsttid** = 00:10:01 **System.Timestamp** = 00:00:01, justeras inte eftersom programmet är i fönstret för sen ankomst.
   3. **Programmet tid** = 00:10:00 **ankomsttid** = 02:00:10 **System.Timestamp** = 00:10:00, justeras inte eftersom programmet är i fönstret för sen ankomst.
   4. **Programmet tid** = 00:09:00 **ankomsttid** = 00:10:03 **System.Timestamp** = 00:09:00, acceptera med ursprungliga tidsstämpeln eftersom programmet är i out av order tolerans.
   5. **Programmet tid** = 00:06:00 **ankomsttid** = 00:10:04 **System.Timestamp** = 00:07:00, justera eftersom programmet tid är äldre än toleransen out ordning.

### <a name="practical-considerations"></a>Praktiska överväganden
Som tidigare nämnts är den största skillnaden mellan program tid och ankomsttid sen ankomst tolerans. När du bearbetningen av programmet tid, justeras händelser som är senare än den konfigurerade toleransen för sen ankomst innan out-ordning inställningarna tillämpas. Gällande i oordning är därför, minimum för sen ankomst feltolerans och tolerans för out-ordning.

För out-ordning händelser i en dataström anledningar:
* Klockavvikelser mellan avsändare.
* Variabel svarstid mellan avsändare och inkommande händelsekälla.

För sen ankomst anledningar:
* Avsändare batchbearbetning och skickar händelser för ett intervall senare efter intervallet.
* Fördröjning mellan händelsen av avsändare skickar och tar emot händelsen på Indatakällan.

Tänk är korrekt, fördröjning och föregående faktorer när du konfigurerar sen ankomst feltolerans och out-ordning tolerans för ett specifikt jobb.

Följande är några exempel.

#### <a name="example-1"></a>Exempel 1 
Frågan har en **Partition av PartitionId** satsen. Inom en enskild partition skickas händelser via synkron skicka metoder. Synkron skicka metoder blockera tills händelser skickas.

I det här fallet i oordning är noll eftersom händelser skickas i ordning med explicit bekräftelse innan nästa händelse skickas. Sen ankomst är den längsta tillåtna fördröjningen mellan orsakar händelsen och skicka händelsen plus Maximal fördröjning mellan avsändare och Indatakällan.

#### <a name="example-2"></a>Exempel 2
Frågan har en **Partition av PartitionId** satsen. Inom en enskild partition skickas händelser via asynkron sändning metoder. Asynkron sändning metoder kan starta flera skickar samtidigt, vilket kan orsaka out ordning händelser.

I det här fallet är både i ordning och sen ankomst minst den längsta tillåtna fördröjningen mellan orsakar händelsen och skicka händelsen plus Maximal fördröjning mellan avsändare och Indatakällan.

#### <a name="example-3"></a>Exempel 3
Frågan har inte en **Partition av PartitionId** -satsen, och det finns minst två partitioner.

Konfigurationen är samma som exempel 2. Men kan avsaknaden av data i en av partitionerna fördröja utdata av ytterligare sen ankomst tolerans ett fönster.

## <a name="handling-event-producers-with-differing-timelines-with-substreams"></a>Hanterar händelsen producenter med olika tidslinjer med ”underströmmar”
En enda inkommande händelseströmmen innehåller ofta händelser som kommer från flera händelse tillverkare, till exempel enskilda enheter. Dessa händelser kan tas emot i rätt ordning på grund av orsaker som nämnts tidigare. I dessa scenarier, även om störning över händelse producenter kanske är stor, är störning i händelser från en enda producent liten (eller även obefintlig).

Azure Stream Analytics ger allmänna metoder för att hantera out ordning händelser. Dessa mekanismer resultatet i bearbetning eller (väntan straggling händelser till systemet), släppa eller justeras händelser eller båda.

Ännu i många fall är bearbetar den önskade frågan händelser från olika händelse producenter oberoende. Det kan till exempel att sammanställa händelser per fönstret per enhet. I dessa fall kan behövs det ingen fördröjning utdata som motsvarar en händelse producenten under väntan på händelsen producenter att komma ikapp. Det finns med andra ord behöver du inte hantera tid skeva mellan producenter. Du kan ignorera den.

Naturligtvis innebär detta att utdatahändelserna själva oordning med avseende på deras tidsstämplar. Underordnade konsumenten måste kunna hantera dessa beteende. Men alla händelser i utdata är korrekt.

Azure Stream Analytics implementerar den här funktionen med hjälp av den [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx) satsen.

## <a name="summary"></a>Sammanfattning
* Konfigurera sen ankomst feltolerans och fönstret out ordning utifrån är korrekt och fördröjning. Överväg också hur händelser skickas.
* Vi rekommenderar att out ordning tolerans är mindre än tolerans för sen ankomst.
* När du kombinerar flera tidslinjer, kan bristen på data i en av källorna eller partitioner fördröja utdata av ytterligare sen ankomst tolerans ett fönster.

## <a name="get-help"></a>Få hjälp
Mer hjälp, försöker den [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språkreferens för Stream Analytics-fråga](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strömma Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
