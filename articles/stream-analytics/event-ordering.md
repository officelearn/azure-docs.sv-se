---
title: Konfigurera principer för händelsebeställning för Azure Stream Analytics
description: I den här artikeln beskrivs hur du konfigurerar även beställningsinställningar i Stream Analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461188"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Konfigurera principer för händelsebeställning för Azure Stream Analytics

I den här artikeln beskrivs hur du konfigurerar och använder principer för sent ankomst och ovillkor i Azure Stream Analytics. Dessa principer tillämpas bara när du använder [TIMESTAMP](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) BY-satsen i frågan.

## <a name="event-time-and-arrival-time"></a>Evenemangstid och ankomsttid

Ditt Stream Analytics-jobb kan bearbeta händelser baserat på antingen *händelsetid* eller *ankomsttid.* **Händelse-/programtid** är den tidsstämpel som finns i händelsenyttolast (när händelsen genererades). **Ankomsttid** är tidsstämpeln när händelsen togs emot vid indatakällan (Event Hubs/IoT Hub/Blob storage). 

Som standard bearbetar Stream Analytics händelser *efter ankomsttid,* men du kan välja att bearbeta händelser efter *händelsetid* med hjälp av [TIMESTAMP](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) BY-satsen i frågan. Sen ankomst och out-of-order-policyer gäller endast om du behandlar händelser efter händelsetid. Överväg krav på svarstid och korrekthet för ditt scenario när du konfigurerar de här inställningarna. 

## <a name="what-is-late-arrival-policy"></a>Vad är principen för händelser som kommer sent?

Ibland händelser anländer sent på grund av olika skäl. Till exempel kommer en händelse som anländer 40 sekunder för sent att ha händelsetid = 00:10:00 och ankomsttid = 00:10:40. Om du ställer in principen om sen ankomst till 15 sekunder kommer alla händelser som anländer senare än 15 sekunder antingen att släppas (inte bearbetas av Stream Analytics) eller få sin händelsetid justerad. I exemplet ovan, när händelsen kom 40 sekunder för sent (mer än principuppsättningen), justeras dess händelsetid till max för principen för sen ankomst 00:10:25 (ankomsttid - policyvärde för sen ankomst). Standardprincipen för sen ankomst är 5 sekunder.

## <a name="what-is-out-of-order-policy"></a>Vad är out-of-order-policy? 

Händelsen kan komma i oordning också. När händelsetiden har justerats baserat på principen om sen ankomst kan du också välja att automatiskt släppa eller justera händelser som är oordnade. Om du anger den här principen till 8 sekunder ordnas alla händelser som kommer ur funktion men inom 8-sekundersfönstret om efter händelsetid. Händelser som kommer senare kommer antingen att tas bort eller justeras till det maximala principvärdet utanför ordningen. Standardprincipen för oordning är 0 sekunder. 

## <a name="adjust-or-drop-events"></a>Justera eller släpp händelser

Om händelser kommer sent eller oordnat baserat på de principer som du har konfigurerat kan du antingen släppa sådana händelser (som inte bearbetas av Stream Analytics) eller få deras händelsetid justerad.

Låt oss se ett exempel på denna politik i handling.
<br> **Policy för sen ankomst:** 15 sekunder
<br> **Princip utanför ordningen:** 8 sekunder

| Händelsenr | Händelsetid | Ankomsttid | System.Timestamp | Förklaring |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Händelsen kom sent och utanför toleransnivå. Så händelsetiden får justeras till maximal sen ankomst tolerans.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Händelsen kom sent men inom toleransnivå. Så händelsetiden inte få justeras.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Händelsen kom i tid. Ingen justering behövs.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Händelsen kom ur funktion men inom toleransen på 8 sekunder. Så, händelsetiden får inte justeras. I analytics-syfte betraktas den här händelsen som föregående händelse nummer 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Händelsen kom ur funktion och utanför tolerans på 8 sekunder. Händelsetiden justeras därför till maximal tolerans i oordning. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Kan dessa inställningar fördröja utdata för mitt jobb? 

Ja. Som standard är principen i oordning noll (00 minuter och 00 sekunder). Om du ändrar standardvärdet försenas jobbets första utdata av det här värdet (eller mer). 

Om en av partitionerna i dina indata inte får händelser bör du förvänta dig att utdata försenas av policyvärdet för sen ankomst. Om du vill veta varför läser du avsnittet InputPartition error nedan. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Jag ser LateInputEvents-meddelanden i min aktivitetslogg

Dessa meddelanden visas för att informera dig om att händelser har kommit sent och antingen har tappats eller justeras enligt din konfiguration. Du kan ignorera dessa meddelanden om du har konfigurerat principen för sen ankomst på rätt sätt. 

Exempel på det här meddelandet är: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Jag ser InputPartitionNotProgressing i min aktivitetslogg

Din indatakälla (Event Hub/IoT Hub) har troligen flera partitioner. Azure Stream Analytics producerar utdata för tidsstämpel t1 först efter att alla partitioner som har kombinerats är minst vid t1. Anta till exempel att frågan läser från en händelsehubbpartition som har två partitioner. En av partitionerna, P1, har händelser fram till tiden t1. Den andra partitionen, P2, har händelser fram till tid t1 + x. Utdata produceras sedan tills tiden t1. Men om det finns en explicit Partition by PartitionId-sats fortskrider båda partitionerna oberoende av dem. 

När flera partitioner från samma indataström kombineras är toleransen för sen ankomst den maximala tiden som varje partition väntar på nya data. Om det finns en partition i händelsehubben, eller om IoT Hub inte tar emot indata, går inte tidslinjen för den partitionen framåt förrän den når toleranströskeln för sen ankomst. Detta försenar din produktion med toleranströskeln för sen ankomst. I sådana fall kan du se följande meddelande:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Detta meddelande för att informera dig om att minst en partition i din indata är tom och kommer att fördröja din utdata med tröskelvärdet för sen ankomst. För att övervinna detta rekommenderas du antingen:  
1. Se till att alla partitioner i händelsehubben/IoT-hubben får indata. 
2. Använd Partition by PartitionID-satsen i frågan. 

## <a name="next-steps"></a>Nästa steg
* [Överväganden för tidshantering](stream-analytics-time-handling.md)
* [Tillgängliga mätvärden i Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
