---
title: "Hanterar händelsen ordning och lateness med Azure Stream Analytics | Microsoft Docs"
description: "Läs mer om hur Stream Analytics fungerar med out-ordning eller försenade händelser i dataströmmar."
keywords: "i ordning, Sen, händelser"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: cf57bd12a62b3de8ac49b26ce7cdc40aec0b6738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-event-order-handling"></a>Azure Stream Analytics ordning händelsehantering

I en temporal dataström av händelser tilldelas varje händelse en tidsstämpel. Azure Stream Analytics tilldelar varje händelse med ankomsttid eller programmet tid tidsstämpel. Kolumnen ”System.Timestamp” har tidsstämpel som tilldelats händelsen. Ankomsttid tilldelas vid Indatakällan när händelsen når källan. Ankomsttid är EventEnqueuedTime för Event Hub indata och [blob senast ändrad](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) för blob-indata. Tid för programmet tilldelas när händelsen genereras och den ingår i nyttolasten. Använda ”tidsstämpel som”-Satsen i select-frågan för att bearbeta händelser vid tidpunkten för programmet. Om instruktionen ”tidsstämpel som” saknas bearbetas händelser av ankomsttid. Ankomsttid kan nås med hjälp av egenskapen EventEnqueuedTime för händelsehubb och använda BlobLastModified-egenskapen för blob-indata. Azure Stream Analytics ger utdata i ordningen som tidsstämpel och innehåller några inställningar för att hantera data i fel ordning.

![Stream Analytics händelsehantering](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

Antingen är inkommande dataströmmar som inte är i ordning:
* Sorterad (och därför **fördröjd**).
* Justeras av systemet, enligt principen som användaren har angett.

Stream Analytics kan tolerera sen och i oordning händelser vid bearbetning av programmet tid.

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

Tolerans för sen ankomst = 10 minuter<br/>
Out-of-ordning tolerans = 3 minuter<br/>
Bearbetning av programmet tid<br/>

Händelser:

Händelsen 1 _programmet tid_ = 00:00:00 _ankomsttid_ = 00:10:01 _System.Timestamp_ = 00:00:01, justera eftersom (_ankomsttid_  -  _Programmet tid_) är fler än sen ankomst toleransen.

Händelsen 2 _programmet tid_ = 00:00:01 _ankomsttid_ = 00:10:01 _System.Timestamp_ = 00:00:01, justeras inte eftersom programmet är inom sen ankomst fönstret.

Händelsen 3 _programmet tid_ = 00:10:00 _ankomsttid_ = 02:00:10 _System.Timestamp_ = 00:10:00, justeras inte eftersom webbappen tid anges i fönstret för sen ankomst .

Händelsen 4 _programmet tid_ = 00:09:00 _ankomsttid_ = 00:10:03 _System.Timestamp_ = 00:09:00, accepteras med ursprungliga tidsstämpel som programmet är i out ordning toleransnivån.

Händelse 5 _programmet tid_ = 00:06:00 _ankomsttid_ = 00:10:04 _System.Timestamp_ = 00:07:00, justera eftersom programmet är äldre än den i fel ordning tolerans.



## <a name="get-help"></a>Få hjälp
Mer hjälp, försök vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språkreferens för Stream Analytics-fråga](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strömma Analytics management REST API-referens](https://msdn.microsoft.com/library/azure/dn835031.aspx)
