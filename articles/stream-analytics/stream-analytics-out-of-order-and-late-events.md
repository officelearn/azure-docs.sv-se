---
title: "Hanterar händelsen ordning och lateness med Azure Stream Analytics | Microsoft Docs"
description: "Läs mer om hur Stream Analytics fungerar med out-ordning eller försenade händelser i dataströmmar."
keywords: "i ordning, Sen, händelser"
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 208dfa14d5d18e106d654539cd80bafdeb90cdf8
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Azure Stream Analytics händelse ordning beräkningen

## <a name="understand-arrival-time-and-application-time"></a>Förstå ankomsttid och tid för programmet.

I en temporal dataström av händelser tilldelas varje händelse en tidsstämpel. Azure Stream Analytics tilldelar varje händelse med ankomsttid eller programmet tid tidsstämpel. Kolumnen ”System.Timestamp” har tidsstämpel som tilldelats händelsen. Ankomsttid tilldelas vid Indatakällan när händelsen når källan. Ankomsttid är EventEnqueuedTime för Event Hub indata och [blob senast ändrad](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) för blob-indata. Tid för programmet tilldelas när händelsen genereras och den ingår i nyttolasten. Använda ”tidsstämpel som”-Satsen i select-frågan för att bearbeta händelser vid tidpunkten för programmet. Om instruktionen ”tidsstämpel som” saknas bearbetas händelser av ankomsttid. Ankomsttid kan nås med hjälp av egenskapen EventEnqueuedTime för händelsehubb och använda BlobLastModified-egenskapen för blob-indata. Azure Stream Analytics ger utdata i ordningen som tidsstämpel och innehåller några inställningar för att hantera data i fel ordning.


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Azure Stream Analytics-hantering av flera strömmar

Azure Stream Analytics-jobbet kombinerar händelser från flera tidslinjer i några fall även,

* Utdata från flera partitioner. Frågor som inte har en explicit ”Partition av PartitionId” måste kombinera händelser från alla partitioner.
* Unionen av två eller flera olika indatakällor.
* Koppla indatakällor.

I fall där flera tidslinjer kombineras Azure Stream Analytics resultat för en tidsstämpel *t1* förrän alla källor som kombineras är minst *t1*.
Om frågan som läser från till exempel en *Händelsehubb* partition som har två partitioner och en av partitionen *P1* har händelser tills *t1* och andra partition  *P2* har händelser tills *t1 + x*, utdata tills *t1*.
Men om det har uppstått ett explicit *”Partition av PartitionId”* -sats båda partitionerna fortskrider oberoende av varandra.
Tolerans för sen ankomst används för att hantera frånvaron av data i en del partitioner.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Konfigurera sen ankomst feltolerans och i oordning tolerans
Antingen är inkommande dataströmmar som inte är i ordning:
* Sorterad (och därför **fördröjd**).
* Justeras av systemet, enligt principen som användaren har angett.

Stream Analytics kan tolerera sen och i oordning händelser vid bearbetning av **programmet tid**. Följande inställningar är tillgängliga i den **händelse ordning** alternativet i Azure-portalen: 

![Stream Analytics händelsehantering](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

**Tolerans för sen ankomst**
* Den här inställningen gäller endast vid bearbetning av programmet tid, annars ignoreras.
* Det här är den högsta skillnaden mellan ankomsttid och tid för programmet. Om programmet är tidigare (ankomsttid - sen ankomst fönstret), är den inställd på (ankomsttid - sen ankomst fönstret)
* När flera partitioner från samma inkommande dataström eller flera inkommande strömmar kombineras tillsammans, är tolerans för sen ankomst den maximala tid som alla partitioner som väntar på att nya data. 

Kort, Sen ankomst fönstret är den längsta tillåtna fördröjningen mellan händelse genereras och mottagning av händelse vid Indatakällan.
Inställningen baserat på toleransen för sen ankomst görs först och i oordning görs nästa. Den **System.Timestamp** kolumn har slutliga tidsstämpel som tilldelats händelsen.

**I oordning tolerans**
* Händelser anländer utanför ordning men i uppsättningen med ”för oordnat” är **ordnas om av tidsstämpel**. 
* Händelser som kommer senare än tolerans är **bort eller justeras**.
    * **Justeras**: efter verkar har kommit till den senaste acceptabla tid. 
    * **Bort**: ignoreras.

Om du vill ordna om händelser som tagits emot inom ”för oordnat” utdata från frågan är **skjutas upp med out för oordnat**.

**Exempel**

* Tolerans för sen ankomst = 10 minuter<br/>
* Out-of-ordning tolerans = 3 minuter<br/>
* Bearbetning av programmet tid<br/>
* Händelser:
   * Händelsen 1 _programmet tid_ = 00:00:00 _ankomsttid_ = 00:10:01 _System.Timestamp_ = 00:00:01, justera eftersom (_ankomsttid_  -  _Programmet tid_) är fler än sen ankomst toleransen.
   * Händelsen 2 _programmet tid_ = 00:00:01 _ankomsttid_ = 00:10:01 _System.Timestamp_ = 00:00:01, justeras inte eftersom programmet är inom sen ankomst fönstret.
   * Händelsen 3 _programmet tid_ = 00:10:00 _ankomsttid_ = 02:00:10 _System.Timestamp_ = 00:10:00, justeras inte eftersom webbappen tid anges i fönstret för sen ankomst .
   * Händelsen 4 _programmet tid_ = 00:09:00 _ankomsttid_ = 00:10:03 _System.Timestamp_ = 00:09:00, accepteras med ursprungliga tidsstämpel som programmet är i out ordning toleransnivån.
   * Händelse 5 _programmet tid_ = 00:06:00 _ankomsttid_ = 00:10:04 _System.Timestamp_ = 00:07:00, justera eftersom programmet är äldre än den i fel ordning tolerans.

## <a name="practical-considerations"></a>Praktiska överväganden
Som nämnts ovan, *tolerans för sen ankomst* är den största skillnaden mellan program tid och ankomsttid.
Även när bearbetningen av program samtidigt händelser som är senare än den konfigurerade *tolerans för sen ankomst* justeras innan den *i oordning tolerans* inställningen tillämpas. Gällande i oordning är därför, minimum för sen ankomst feltolerans och i oordning toleransen.

Oordnade händelser i en dataström inträffa av olika orsaker och
* Förskjutning av klockan mellan avsändare.
* Variabel svarstid mellan avsändare och inkommande händelsekälla.

Sen ankomst händer på grund av orsaker och
* Avsändare batch och skickar händelser för ett intervall senare efter intervallet.
* Fördröjning mellan händelsen av avsändare skickar och tar emot händelsen på Indatakällan.

När du konfigurerar *tolerans för sen ankomst* och *i oordning tolerans* för ett specifikt jobb, är korrekt, fördröjning och högre faktorer bör övervägas.

Följande är några exempel

### <a name="example-1"></a>Exempel 1: 
Frågan har ”Partition med partitions-ID”-satsen och inom en enskild partition händelser skickas med hjälp av synkron skicka metoder. Synkron skicka metoder blockera tills händelser skickas.
I det här fallet i oordning är noll eftersom händelser skickas i ordning med explicit bekräftelse innan du skickar nästa händelse. Sen ankomst är Maximal fördröjning mellan orsakar händelsen och skicka händelsen + Maximal fördröjning mellan avsändare och indatakälla

### <a name="example-2"></a>Exempel 2:
Frågan har ”Partition med partitions-ID”-satsen och inom en enskild partition händelser skickas med hjälp av asynkron send-metoden. Asynkron sändning metoder kan starta flera skickar samtidigt, vilket kan orsaka oordnade händelser.
I det här fallet finns både i ordning och sen ankomst minst Maximal fördröjning mellan orsakar händelsen och skicka händelsen + Maximal fördröjning mellan avsändare och Indatakällan.

### <a name="example-3"></a>Exempel 3:
Frågan har inte ”Partition av PartitionId” och det finns minst två partitioner.
Konfigurationen är samma som exempel 2. Men avsaknaden av data i en av partitionerna kan fördröja utdata av ytterligare * sen ankomst tolerans ”fönster.

## <a name="to-summarize"></a>För att sammanfatta
* Sen ankomst feltolerans och i oordning fönstret ska konfigureras baserat på är korrekt, fördröjning och bör också övervägas hur händelser skickas.
* Du rekommenderas att i oordning tolerans är mindre än tolerans för sen ankomst.
* När du kombinerar flera tidslinjer kan bristen på data i en av källorna eller partitioner fördröja utdata av ytterligare sen ankomst tolerans ett fönster.

## <a name="get-help"></a>Få hjälp
Mer hjälp, försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språkreferens för Stream Analytics-fråga](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strömma Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
